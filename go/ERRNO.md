errno 在 <errno.h> 中定义，错误 Exx 的宏定义在 /usr/include/asm-generic 文件夹下面的  errno-base.h 和 errno.h，分别定义了 1-34 、35-132 的错误定义。

strerror() 函数依据 errno 值返回错误描述字符串，下面程序打印对照表：

[cpp] view plain copy
#include <errno.h>  
#include <string.h>  
#include <stdio.h>  
  
int main()  
{  
    int i;  
    for(i = 0; i < 140; ++i)  
    {  
        errno = i;  
        printf("errno %d :\t\t%s\n",i,strerror(errno));  
    }  
    return 0;  
}  
错误对照表：
errno0 :     Success

errno1 :     Operation not permitted

errno2 :     No such file or directory

errno3 :     No such process

errno4 :     Interrupted system call

errno5 :     Input/output error

errno6 :     No such device or address

errno7 :     Argument list too long

errno8 :     Exec format error

errno9 :     Bad file descriptor

errno10 :    No child processes

errno11 :    Resource temporarily unavailable

errno12 :    Cannot allocate memory

errno13 :    Permission denied

errno14 :    Bad address

errno15 :    Block device required

errno16 :    Device or resource busy

errno17 :    File exists

errno18 :    Invalid cross-device link

errno19 :    No such device

errno20 :    Not a directory

errno21 :    Is a directory

errno22 :    Invalid argument

errno23 :    Too many open files in system

errno24 :    Too many open files

errno25 :    Inappropriate ioctl for device

errno26 :    Text file busy

errno27 :    File too large

errno28 :    No space left on device

errno29 :    Illegal seek

errno30 :    Read-only file system

errno31 :    Too many links

errno32 :    Broken pipe

errno33 :    Numerical argument out of domain

errno34 :    Numerical result out of range

errno35 :    Resource deadlock avoided

errno36 :    File name too long

errno37 :    No locks available

errno38 :    Function not implemented

errno39 :    Directory not empty

errno40 :    Too many levels of symbolic links

errno41 :    Unknown error 41

errno42 :    No message of desired type

errno43 :    Identifier removed

errno44 :    Channel number out of range

errno45 :    Level 2 not synchronized

errno46 :    Level 3 halted

errno47 :    Level 3 reset

errno48 :    Link number out of range

errno49 :    Protocol driver not attached

errno50 :    No CSI structure available

errno51 :    Level 2 halted

errno52 :    Invalid exchange

errno53 :    Invalid request descriptor

errno54 :    Exchange full

errno55 :    No anode

errno56 :    Invalid request code

errno57 :    Invalid slot

errno58 :    Unknown error 58

errno59 :    Bad font file format

errno60 :    Device not a stream

errno61 :    No data available

errno62 :    Timer expired

errno63 :    Out of streams resources

errno64 :    Machine is not on the network

errno65 :    Package not installed

errno66 :    Object is remote

errno67 :    Link has been severed

errno68 :    Advertise error

errno69 :    Srmount error

errno70 :    Communication error on send

errno71 :    Protocol error

errno72 :    Multihop attempted

errno73 :    RFS specific error

errno74 :    Bad message

errno75 :    Value too large for defined datatype

errno76 :    Name not unique on network

errno77 :    File descriptor in bad state

errno78 :    Remote address changed

errno79 :    Can not access a needed sharedlibrary

errno80 :    Accessing a corrupted sharedlibrary

errno81 :    .lib section in a.out corrupted

errno82 :    Attempting to link in too manyshared libraries

errno83 :    Cannot exec a shared librarydirectly

errno84 :    Invalid or incomplete multibyte orwide character

errno85 :    Interrupted system call should berestarted

errno86 :    Streams pipe error

errno87 :    Too many users

errno88 :    Socket operation on non-socket

errno89 :    Destinationaddress required

errno90 :    Message too long

errno91 :    Protocol wrong type for socket

errno92 :    Protocol not available

errno93 :    Protocol not supported

errno94 :    Socket type not supported

errno95 :    Operation not supported

errno96 :    Protocol family not supported

errno97 :    Address family not supported byprotocol

errno98 :    Address already in use

errno99 :    Cannot assign requested address

errno100 :   Network is down

errno101 :   Network is unreachable

errno102 :   Network dropped connection onreset

errno103 :   Software caused connection abort

errno104 :   Connection reset by peer

errno105 :   No buffer space available

errno106 :   Transport endpoint is alreadyconnected

errno107 :   Transport endpoint is notconnected

errno108 :   Cannot send after transportendpoint shutdown

errno109 :   Too many references: cannot splice

errno110 :   Connection timed out

errno111 :   Connection refused

errno112 :   Host is down

errno113 :   No route to host

errno114 :   Operation already in progress

errno115 :   Operation now in progress

errno116 :   Stale NFS file handle

errno117 :   Structure needs cleaning

errno118 :   Not a XENIX named type file

errno119 :   No XENIX semaphores available

errno120 :   Is a named type file

errno121 :   Remote I/O error

errno122 :   Disk quota exceeded

errno123 :   No medium found

errno124 :   Wrong medium type

errno125 :   Operation canceled

errno126 :   Required key not available

errno127 :   Key has expired

errno128 :   Key has been revoked

errno129 :   Key was rejected by service

errno130 :   Owner died

errno131 :   State not recoverable

errno132 :   Operation not possible due toRF-kill

errno133 :   Unknown error 133

errno134 :   Unknown error 134

errno135 :   Unknown error 135

errno136 :   Unknown error 136

errno137 :   Unknown error 137

errno138 :   Unknown error 138

errno139 :   Unknown error 139


由上可见Linux对错误宏的定义。

头文件 /usr/include/asm-generic/errno-base.h 的源码：

#ifndef _ASM_GENERIC_ERRNO_BASE_H
#define _ASM_GENERIC_ERRNO_BASE_H

#define EPERM  1 /* Operation not permitted */
#define ENOENT2
/* No such file or directory */
#define ESRCH  3 /* No such process */
#define EINTR  4 /* Interrupted system call */
#define EIO  5 /* I/O error */
#define ENXIO  6 /* No such device or address */
#define E2BIG  7 /* Argument list too long */
#define ENOEXEC8
/* Exec format error */
#define EBADF  9 /* Bad file number */
#define ECHILD10
/* No child processes */
#define EAGAIN11
/* Try again */
#define ENOMEM12
/* Out of memory */
#define EACCES13
/* Permission denied */
#define EFAULT14
/* Bad address */
#define ENOTBLK15
/* Block device required */
#define EBUSY  16 /* Device or resource busy */
#define EEXIST17
/* File exists */
#define EXDEV  18 /* Cross-device link */
#define ENODEV19
/* No such device */
#define ENOTDIR20
/* Not a directory */
#define EISDIR21
/* Is a directory */
#define EINVAL22
/* Invalid argument */
#define ENFILE23
/* File table overflow */
#define EMFILE24
/* Too many open files */
#define ENOTTY25
/* Not a typewriter */
#define ETXTBSY26
/* Text file busy */
#define EFBIG  27 /* File too large */
#define ENOSPC28
/* No space left on device */
#define ESPIPE29
/* Illegal seek */
#define EROFS  30 /* Read-only file system */
#define EMLINK31
/* Too many links */
#define EPIPE  32 /* Broken pipe */
#define EDOM  33 /* Math argument out of domain of func */
#define ERANGE34
/* Math result not representable */

#endif

头文件/usr/include/asm-generic/erno.h源码：

#ifndef _ASM_GENERIC_ERRNO_H
#define _ASM_GENERIC_ERRNO_H

#include <asm-generic/errno-base.h>

#define EDEADLK35
/* Resource deadlock would occur */
#define ENAMETOOLONG36
/* File name too long */
#define ENOLCK37
/* No record locks available */
#define ENOSYS38
/* Function not implemented */
#define ENOTEMPTY39
/* Directory not empty */
#define ELOOP  40 /* Too many symbolic links encountered */
#define EWOULDBLOCKEAGAIN
/* Operation would block */
#define ENOMSG42
/* No message of desired type */
#define EIDRM  43 /* Identifier removed */
#define ECHRNG44
/* Channel number out of range */
#define EL2NSYNC45
/* Level 2 not synchronized */
#define EL3HLT46
/* Level 3 halted */
#define EL3RST47
/* Level 3 reset */
#define ELNRNG48
/* Link number out of range */
#define EUNATCH49
/* Protocol driver not attached */
#define ENOCSI50
/* No CSI structure available */
#define EL2HLT51
/* Level 2 halted */
#define EBADE  52 /* Invalid exchange */
#define EBADR  53 /* Invalid request descriptor */
#define EXFULL54
/* Exchange full */
#define ENOANO55
/* No anode */
#define EBADRQC56
/* Invalid request code */
#define EBADSLT57
/* Invalid slot */

#define EDEADLOCKEDEADLK

#define EBFONT59
/* Bad font file format */
#define ENOSTR60
/* Device not a stream */
#define ENODATA61
/* No data available */
#define ETIME  62 /* Timer expired */
#define ENOSR  63 /* Out of streams resources */
#define ENONET64
/* Machine is not on the network */
#define ENOPKG65
/* Package not installed */
#define EREMOTE66
/* Object is remote */
#define ENOLINK67
/* Link has been severed */
#define EADV  68 /* Advertise error */
#define ESRMNT69
/* Srmount error */
#define ECOMM  70 /* Communication error on send */
#define EPROTO71
/* Protocol error */
#define EMULTIHOP72
/* Multihop attempted */
#define EDOTDOT73
/* RFS specific error */
#define EBADMSG74
/* Not a data message */
#define EOVERFLOW75
/* Value too large for defined data type */
#define ENOTUNIQ76
/* Name not unique on network */
#define EBADFD77
/* File descriptor in bad state */
#define EREMCHG78
/* Remote address changed */
#define ELIBACC79
/* Can not access a needed shared library */
#define ELIBBAD80
/* Accessing a corrupted shared library */
#define ELIBSCN81
/* .lib section in a.out corrupted */
#define ELIBMAX82
/* Attempting to link in too many shared libraries */
#define ELIBEXEC83
/* Cannot exec a shared library directly */
#define EILSEQ84
/* Illegal byte sequence */
#define ERESTART85
/* Interrupted system call should be restarted */
#define ESTRPIPE86
/* Streams pipe error */
#define EUSERS87
/* Too many users */
#define ENOTSOCK88
/* Socket operation on non-socket */
#define EDESTADDRREQ89
/* Destination address required */
#define EMSGSIZE90
/* Message too long */
#define EPROTOTYPE91
/* Protocol wrong type for socket */
#define ENOPROTOOPT92
/* Protocol not available */
#define EPROTONOSUPPORT93
/* Protocol not supported */
#define ESOCKTNOSUPPORT94
/* Socket type not supported */
#define EOPNOTSUPP95
/* Operation not supported on transport endpoint */
#define EPFNOSUPPORT96
/* Protocol family not supported */
#define EAFNOSUPPORT97
/* Address family not supported by protocol */
#define EADDRINUSE98
/* Address already in use */
#define EADDRNOTAVAIL99
/* Cannot assign requested address */
#define ENETDOWN100
/* Network is down */
#define ENETUNREACH101
/* Network is unreachable */
#define ENETRESET102
/* Network dropped connection because of reset */
#define ECONNABORTED103
/* Software caused connection abort */
#define ECONNRESET104
/* Connection reset by peer */
#define ENOBUFS105
/* No buffer space available */
#define EISCONN106
/* Transport endpoint is already connected */
#define ENOTCONN107
/* Transport endpoint is not connected */
#define ESHUTDOWN108
/* Cannot send after transport endpoint shutdown */
#define ETOOMANYREFS109
/* Too many references: cannot splice */
#define ETIMEDOUT110
/* Connection timed out */
#define ECONNREFUSED111
/* Connection refused */
#define EHOSTDOWN112
/* Host is down */
#define EHOSTUNREACH113
/* No route to host */
#define EALREADY114
/* Operation already in progress */
#define EINPROGRESS115
/* Operation now in progress */
#define ESTALE116
/* Stale NFS file handle */
#define EUCLEAN117
/* Structure needs cleaning */
#define ENOTNAM118
/* Not a XENIX named type file */
#define ENAVAIL119
/* No XENIX semaphores available */
#define EISNAM120
/* Is a named type file */
#define EREMOTEIO121
/* Remote I/O error */
#define EDQUOT122
/* Quota exceeded */

#define ENOMEDIUM123
/* No medium found */
#define EMEDIUMTYPE124
/* Wrong medium type */
#define ECANCELED125
/* Operation Canceled */
#define ENOKEY126
/* Required key not available */
#define EKEYEXPIRED127
/* Key has expired */
#define EKEYREVOKED128
/* Key has been revoked */
#define EKEYREJECTED129
/* Key was rejected by service */

/* for robust mutexes */
#define EOWNERDEAD130
/* Owner died */
#define ENOTRECOVERABLE131
/* State not recoverable */

#define ERFKILL 132/* Operation not possible due to RF-kill */

#endif