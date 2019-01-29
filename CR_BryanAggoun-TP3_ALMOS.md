## Création Process et Thread

# Question 1

Les process sont identifiés par différents ID codés sur 32 bits permettant non seulement de pointer vers leur structure au sein des différents cluster mais aussi pour identifier la nature du process dont il est question. Pour chaque cluster, il existe une structure _cluster manager_ contenant 2 tableaux permettants de parcourir par pointage (_xptr_) ou par liste chaîné (_xlist_) l'ensemble des différents structures process présentes dans le banc mémoire du cluster mais aussi des process copies se trouvant dans les autres cluster. 
Il existe aussi des pointeurs interne à la structure process permettant de pointer les process père, référence et owner du process en question (PID, PPID, PREF, POWNER).

Les différents threads d'un process sont identifiés à l'intérieur de la structure du process. En effet, pour chaque structure process il existe un tableau TH_TLB contenant les pointeurs vers les threads du process en question et indexé par le TRDID (identifiant du thread) présent dans chaque structure thread.

# Question 2 

Pour chaque process créé on distingue :

- Le cluster owner correspond au cluster dans lequel un process en question a été créé. Il est le cluster "proprétaire" et le cluster de "naissance" du process.

- Le cluster réference est le cluster contenant la version la plus complète de la structure process d'un process. Actuellement, dans ALMOS-MKH, ce cluster est identique au owner. Néanmoins, on distingue ces deux cas car dans le cas où on constate qu'il n'y a plus assez de place dans un cluster, il faut le réorganiser et placé certaines struct proc dans un autre cluster. Ainsi, le cluster owner ne sera plus le même que le cluster de reférence pour le process qui a été migré autre part dans la machine. Cependant, pour l'instant ALMOS-MKH ne fait pas de migration de processus.

- Les cluster copy contenants au moins 1 thread du process concerné et contenant une version incomplète ou complète de sa structure porcess.

# Question 3

Le même mécanisme n'est reproduit pour les différents threads d'un même process. Néanmoins, les threads étants associés aux différents process owner, reférence et copy d'un même process, ils sont aussi liés et concernés par cette organisation au seins des différents cluster.

Il n'existe donc pas de copies de la structure d'un thread dispersés au seins des différents cluster mais chaque thread est associé à un process qui lui même possède une table des thread présent dans son cluster d'exécutioni qu'il soit de type owner, reférence ou copy.

# Question 4

Il y a 4 types de threads :

- USR --> thread user créés et définis à la demande de l'utilisateur afin d'exécuter du code user
- DEV --> thread device (I/O) exécutant les opérantions d'entrées/sorties
- RPC --> thread de requête RPC entre noyau
- IDL --> thread IDLE qui est le thread par défaut 

# Question 5

Un VSEG est un Virtual Segment. Il s'agit d'un segment d'adresses virtuels contigües alignés sur des pages vituelles de 4Ko. A chaque Espace virtuel (EV) d'un process est associé un ensemble de VSEG.

Un GPT correspond à la Generic Page Table. Il s'agit d'une table propre à chaque process et permettant de mappé en mémoire physique les VSEG. La GPT associe à chaque Page Virtuel (PV) une Page Physique (PP).

# Quesion 6

Pour chaque process, est associé un ensemble de VSEG de type différents et ayant chacun leur droit d'accès (private ou public).

Types de VSEG associé à un process :

- STACK --> les différents segments de piles des différents threads du process
- CODE --> segment de texte de l'application
- DATA --> les données de l'application
- ANON
- FILE --> 	Liste des fichiers ouverts
- REMOTE

# Question 7 

# Question 8

On un problème de cohérence des VSEG car dans chaque cluster, pour une adresse virtuel d'un thread on aura une adresse physique associé différentes et propre à chaque cluster.

# Question 9 

Lors de la création d'un process, le rôle de _fork()_ est de créer un nouveau process à l'image du process père qui le créé. Ainsi, à la fin de l'appel système _fork()_ on obtient un nouveau process enfant avec un nouvel identifiant et qui a hérité de la table des fichiers ouverts et limage mémoire du process père. 

L'appel système _exec()_, quand à lui, peut être appelé par le process fils venant d'être créé à la suite du _fork()_. Cette appel système permet d'allouer une nouvelle image mémoire au process fils pour que celui-ci exécute sont propre code.

# Question 10

T1 exécute un _fork()_, il y a donc création d'un nouveau process à l'image du père. On obtient donc 2 process composés de 3 threads chacun.

# Question 11

Une RPC signifie Remote Procedure Call et permet aux threads des différents cluster de communiquer avec les différentes insatances du noyau présentent dans chaque cluster afin de leur rendre différents types de services. 

# Quesion 12

Une RPC simple correspond à un thread client une simple requête demandant une simple et unique réponse.

Une RPC multicast (qui je pense, correspond au parallel RPC request) permet à un thread client d'evoyer une multitude de requêtes en paralleles attendant de multiples répnses.


