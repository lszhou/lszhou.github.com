FIFO and LRU are two famous and feasible page replacement algorithms. In order to understand the behaviors of the two algorihms, this artical introduce a simply simulator to analysis the above two algorithms.

##Introduction

Each process is corresponding to a independent address space, namely the process address space (PAS). This concept, at first glance, is hard to grab since it is essentially a kind of abstraction. Such abstraction is pretty common in the OS learning, many concepts such as process, thread are all well defined abstractions. One process's PAS is just some contiguous (virtual) address. The address is virtual address, we say this is just to distinguish with the physical addresses, which is corresponding to physical memory.

In modern computer, there is special unit connected to the CPU chips, that is the MMU (Memory Management Unit). It acts as abridge between the CPU, physical memory and disks. Its prime function is to map the virtual address onto the physical memory addresses.

When a program is executed, it becomes a process and thus has its own address space. The size of the space depends on particular architecture. Generally, the size is much more than the size of the pgysical memory. So how can we deal with this problem?

To solve this problem, the first thing we need to know is that if the PAS is broken up int chunks (virtual pages), not all the pages have to be in the physical memory to run the program. CPU only needs to tell MMU: "I want the data at the virtual address ..... (say 3333)", than the MMU checks whether the virtual page corresponding to 3333 has been mapped to memory (physical frame). If the answer is yes, than MMU just needs to give the data in the phsical frame to CPU; if no, than MMU first need to finish mapping task then do the above thing.

Technically, when the answer is no, MMU will cause CPU to trap in the OS, such trap is so called Page Fault. OS will next execute a trap instruction: telling free frame, evicting a occupied frame, than changing the mapping. Namely, if there is a free frame, then the OS selects it, fetches the data from disk, writes the page into the destination frame and updating the index as needed. However, if all the frames are occupied, then the OS beats its brain out to try to evict one of them. Such problem is so called page replacement problem. 

##Analysis and Simply Simulation of Page Replacement Algorithms

Naturally, the above evicting operation must do according to some rules. Namely, select which frame to evict in favor of an incoming page? Such rule is also called page replacement algorithm. Conceptually, the lower fault rate, the better the replacement algorithm is.

FIFO (Fisrt In First Out) and LRU (Least Recently Used) is two famous replacement algorithms. In the following session, we will try to take a look at the properties of the algorithms respectively via implementing a simulator. Here we still use the Linux standard language C (not C++).

Simulator is named 'memsim' . The output and invocation are in the following style:

```
[host@tect~]$./memsim --virt 100 --phy 10 --alg FIFO --state COLD
```

There are four variables (or options/arguments):
- the size of virtual memory, in pages (**name: virt**)
- the number of available "physical" page frames (**name: phy**)
- the algorithm to simulate (**name: alg**)
- a starting state of memory (**name: state**)


If starting state is "COLD", all physical frames are available,holding no virtual page.  If it is "HOT", then the simulator randomly maps virtual pages to each and every physical page frame before the simulation starts.

We use the the getopt_long(3) to parse the command line and apply atoi() and strdup() to get the values of optarg respectively and sotred them respectively in variables: "pageSize", "frameCount" and "alg". We also need to issue a random sequence of "memory references" to the set of virtual pages. (Each run proceeds for 100,000 random page references.)

Thus, the main function looks like this:

```C
/*Function Declarations*/
void FIFO(int pc[],int bc[],int pageCount,int frameCount) 
void FIFO2(int pc[],int bc[],int pageCount,int frameCount)   
void LRU(int pc[],int bc[],int pageCount,int frameCount)   
void LRU2(int pc[],int bc[],int pageCount,int frameCount)  

int main(int argc, char *argv[])   
{
  int opt = 0;
  char alg_FIFO[4]={'F','I','F','O'};
  char alg_LRU[3]={'L','R','U'};
  char HOT[3]={'H','O','T'};
  char COLD[4]={'C','O','L','D'};

  int pageSize;/*the size of virtual memory, in pages*/
  int frameCount;/*the number of available "physical page frames"*/
  int pageCount=100000;
  int bc[pageCount];
  char *alg;
  char *key;

  /*Options setting*/
  static struct option long_options[] = {
    {"virt",  required_argument, 0,  'v' },
    {"phy",   required_argument, 0,  'p' },
    {"alg",   required_argument, 0,  'a' },
    {"state", required_argument, 0,  's' }
  };
  
  int long_index =0;
  opt = getopt_long(argc,argv,"v:p:a:s:", long_options, &long_index);
  while(opt != -1)
    {
      switch(opt)
 {
 case 'v':
   pageSize=atoi(optarg);
   break;

  case 'p':
   frameCount=atoi(optarg);
   break;

  case 'a':
   alg=strdup(optarg);
   break;
   
 case 's':
   key=strdup(optarg);
   break; 
 }
      opt = getopt_long(argc,argv,"v:p:a:s:", long_options, &long_index);  
    }

  /*producing a random sequence of memory reference*/
  srand((int)time(0));

  int i;
  for(i=0; i<pageCount; i++)
    {
      pc[i]=random(pageSize);
    }
  if(alg_FIFO[0]==alg[0]&&alg_FIFO[1]==alg[1]&&alg_FIFO[2]==alg[2]&&alg_FIFO[3]==alg[3]){
      if(key[0]==COLD[0])
   FIFO(pc,bc,pageCount,frameCount);
      else if(key[0]==HOT[0])
   FIFO2(pc,bc,pageCount,frameCount);
      else
   printf("State error, please input the right state (HOT, COLD).");    
    }

  else if(alg_LRU[0]==alg[0]&&alg_LRU[1]==alg[1]&&alg_LRU[2]==alg[2]){
      if(key[0]==COLD[0])
   LRU(pc,bc,pageCount,frameCount);
      else if(key[0]==HOT[0])
   LRU2(pc,bc,pageCount,frameCount);
      else
   printf("State error!\nPlease Choose the Right state (HOT, COLD).\n");     
    }

  else
    printf("Algorithm Error!\nPlease Choose the Right Algorithm (FIFO, LRU)!\n");

  return 0;
}

```
The following step is to write the FIFO and LRU. The two implementation are easy to find from website. Output Report and Analysis:

Simulating 50 runs of each of the two algorithms FIFO and LRU, the mean,  median and std. dev. of the number of page faults are reported as follows:

``` 
                FIFO(COLD)                LRU(COLD)  

mean            90001                      89984      

media           90027                      89966    

std.dev         78.16                      52.45

``` 
From the above statics, LRU produces fewer page faults. But the data of  FIFO is pretty close to LRU. The page fault rate are both about 90%.

##Conclusion

FIFO is the algorithm that when the page fault occurs, evict the page that was brought into the cache least recently. While LRU is the one when a page fault occcurs evict the page that was accessed least recently. Because the the number of physical page frames is much less than the size of virtual memory and the 100000 references are uniformly distributed, so he page fault rate are both about 0.9. In another words, LRU keeps the things that were most recently used in memory.  FIFO keeps the things that were most recently added. LRU is, in general,  more efficient, because there are generally memory items that are added once and never used again, and there are items that are added and used frequently. LRU is much more likely to keep the frequently-used items in memory. But the reference is uniformaly distributed, so probabilities of the items to be used are alomost the same. So 90% references will produce page faults. But generally, to actual sequence of reference. LRU is better than FIFO since the one has been accessed recently is more likely to be accessed again soon.

##Appendix:

Here are some interesting online materials that may help you understand the page relacement algorithms.
http://stackoverflow.com/questions/2058403/why-is-lru-better-than-fifo
http://blog.csdn.net/crayondeng/article/details/9017823




##Reference:

- Wikipedia Website, http://en.wikipedia.org/wiki/Page_replacement_algorithm
- Modern Operating Systems, Third Edition by Andrew S. Tanenbaum, published by Pearson/Prentice Hall (a textbook-like -tome on operating systems concepts)
- Linux Kernel Development, 3rd Edition by Robert Love











