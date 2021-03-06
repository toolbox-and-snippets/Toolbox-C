//Charles Cary
//https://github.com/cioc/C_Reference

C_Reference
===========

A Short (~200 lines) & Incomplete C Reference

//a C reference for practitioners

General Includes
================
#include <sys/types.h> //pid_t type and others
#include <stdlib.h> //system
#include <unistd.h> //sleep, execve, fork,read,write, close signals lots of sys call wrappers
//basically need to include unistd to do useful things
#include <stdio.h> //printf, includes stdin, stdout, stderr

Concurrency
==========
//concurrency - processes
#include <sys/wait.h> //wait functions
pid_t fork(void); //returns 0 in child 
pid_t waitpid(pid_t pid,int *status, int options); //options = 0, status is result status=-1 or a pid (-1 == all children)
pid_t wait(int *status) // equivalent to waitpid(-1, status, 0)
WIFEXITED(status) // true is child exited normally
unsigned int sleep(unsigned int secs)

//concurrency - threads
#include <pthread.h>
//typedef void *(func)(void *)
int pthread_create(pthread_t *tid, pthread_attr_t *attr, func *f, void *arg)//attr = NULL
pthread_t pthread_self(void)
int pthread_cancel(pthread_t tid)
int pthread_join(pthread_t tid, void **thread_return) //thread_return is void * output
int pthread_detach(pthread_t tid)

//concurrency - semaphore & mutex & cond vars
#include <semaphore.h>
sem_t mutex;
int sem_init(sem_t *sem, 0, unsigned int value); //for mutex set v=1
int sem_wait(set_t *s) //wait
int sem_post(sem_t *s) //release
#include <pthread.h>
int pthread_mutex_init(pthread_mutex_t *mutex, const pthread_mutexattr_t *attr); //attr = NULL
int pthread_mutex_lock(pthread_mutex_t *mutex);
int pthread_mutex_trylock(pthread_mutex_t *mutex);
int pthread_mutex_unlock(pthread_mutex_t *mutex);

System
======
int execve(const char *filename, char char *argv[], const char *envp[])
int *getenv(const char *name)
int setenv(const char *name, const char *newvalue, int overwrite)
void unsetenv(const char *name)
int system(const char *command) // like running a terminal command

Other
=====
int rand(void)

Signals
=======
#include <signal.h>
pid_t getpgrp(void) // returns process group id
int setpgid(pid_t pid, pid_t pgid) // pid = 0 sets current process
int kill(pid_t pid, int sig) // send signal sig to process pid, if pid=0 then sends to all in group of caller
sighandler_t signal(int signum, sighandler_t handler) 
sighandler - void (*fn)(int)
unsigned int alarm(unisgned int secs)
signals: SIGALRM, SIGINT, SIGQUIT, SIGKILL, SIGSEGV, SIGCHLD, SIGCONT

//portable
int sigprocmask(int how, const sigset_t *set, sigset_t *oldset); //actually installs a sigset_t
//how - SIG_BLOCK, SIG_UNBLOCK, SIG_SETMASK (blocked = set)
int sigemptyset(sigset_t *set);
int sigfillset(sigset_t *set);
int sigaddset(sigset_t *set, int signum)
int sigdelset(sidset_t *set, int signum)
int sigismember(const sigset_t *set, int signum)
int sigaction(int signum, struct sigaction *act, struct sigaction *oldact)
struct sigaction action
action.sa_handler
action.sa_flags = SA_RESTART// restart syscalls 

Jumping
=======
#include <setjmp.h>
int setjmp(jmp_buf env)
void longjmp(jump_buf env, int retval)

Files
=====
//File IO
#include <sys/stat.h>
#include <fcntl.h>
int open(char *filename, int flags, mode_t mode) // flags - O_RDONLY, O_WRONLY, O_RDWR, O_APPEND | mode = 0
//mode - for new files | S_IRUSR, S_IWUSR, S_IXUSR
                         S_IRGRP, S_IWGRP, S_IXGRP
                         S_IROTH, S_IWOTH, S_IXOTH
int close(int fd)
ssize_t read(int fd, void *buf, size_t n) //-1 error, 0 EOF
ssize_t write(int fd, const void *buf, size_t n)
off_t lseek(int fd, off_t offset, int whence) //SEEK_SET,SEEK_CUR,SEEK_END
int stat(const char *filename, struct stat *buf)
int fstat(int fd, struct stat *buf)
struct stat {
  dev_t st_dev; //device
  ino_t st_ino; //inode
  mode_t st_mode; //protection and file type
  nlink_t st_nlink; // number of hard links
  uid_t st_uid; //user id of owner
  gid_t st_gid; //group id of owner
  dev_t st_rdev; //device type
  off_t st_size; //total size
  unsigned long st_blksize; //blocksize of filesys io
  unsigned long st_blocks; //number of allocated blocks
  time_t st_atime; //time of last access
  time_t st_mtime; //time of last modification
  time_t st_ctime; //time of last change
};

//these are in stdio
FILE *fopen(const char *path, const char *mode) //mode: r,rb,w,wb,a,ab,r+,rb+, r+b, w+,wb+,w+b, a+,ab+,a+b
size_t fread(void *ptr, size_t size, size_t count, FILE *stream)
int fscanf(FILE * stream, const char* format, ...)
int sscanf(const char *s, const char *format, ...)
char * fgets(char * str, int num, FILE * stream)
int fflush(FILE * stream)
int fclose(FILE *stream)
size_t fwrite(const void * ptr, size_t size, size_t count, FILE *stream)
int fprintf(FILE *stream, const char *format,...)
int sprintf(char * str, const char *format, ...)
int fputs(const char *str, FILE * stream)
fseek(FILE *stream, long int offset, int origin)
void rewind(FILE *stream)

Memory Mapping
==============
#include <sys/mman.h>
void *mmap(void *addr, size_t len, int prot, int flags, int fd, off_t off)
//prot - PROT_READ, PROT_WRITE, PROT_EXEC, PROT_NONE
//flags = MAP_SHARED, MAP_PRIVATE, MAP_FIXED
int munmap(void *addr, size_t len)

Networking
==========

//big endian
#include <netinet/in.h>
unsigned long int htonl(unsigned long int hostlong); //converting host to network byte order
unsigned short int hton(unsigned short int hostshort);
unsigned long int ntohl(unsigned long int netlong); //convert net to host
unsigned short int ntohs(unsigned short int netshort);

//structs
struct sockaddr {
  unsigned short    sa_family;    // address family, AF_xxx
  char              sa_data[14];  // 14 bytes of protocol address
};
struct in_addr {
  unsigned long s_addr;          
};
struct sockaddr_in {
  short            sin_family;   // AF_INET, AF_INET6
  unsigned short   sin_port;     // htons(8080)
  struct in_addr   sin_addr;     
  char             sin_zero[8];  // zero this if you want to
};

//#include <arpa/inet.h>
int inet_aton(const char *cp, struct in_addr *inp) //converts decimal dotted string to ip address
char *inet_ntoa(struct in_addr in)

//DNS
#include <netdb.h>
struct hostent {
  char *h_name;
  char **h_aliases;
  int h_addrtype; //AF_INET
  int h_length;
  char **h_addr_list; 
};
struct hostent *gethostbyname(const char *name);
struct hostend *gethostbyaddr(const char *addr, int len, 0)

//SOCKETS
#include <sys/socket.h>
int socket(int domain, int type, int protocol) //AF_INET, SOCK_STREAM, 0
int connect(int sockfd, struct sockaddr *serv_addr, int addrlen)
int bind(int sockfd, struct sockaddr *my_addr, int addrlen)
int listen(int sockfd, int backlog)
int accept(int listenfd, struct sockaddr *addr, int *addrlen)

Strings & Memory
================
#include <string.h>
void *memcpy(void *destination, const void *source, size_t n)
void *memmove(void *destinationb, const void *source, size_t n)
void *memset(void * ptr, int value, size_t n)
int memcmp(const void *ptr1, const void *ptr2, size_t n)
char *strncpy(char * destination, const char *source, size_t n)
char *strncat(char * destination, char *source, size_t n)
int strncmp(const char *str1, const char * str2, size_t n)
char *strstr(const char *str1, const char *str) //scan first, sequence second
char *strtok(char *str, const char * delimiters) //str=NULL after first call
size_t strlen(const char *str)

Memory allocation
===================
void *malloc(size_t size)
void *calloc(size_t number, size_t s)
void *realloc(void *ptr, size_t size)
void free(void *ptr)

Operators
=========
//bitwise
|
&
>> //right shift
<< //left shirt
^ //xor
~ //complement

//logical
?
&&
!= 
==  
