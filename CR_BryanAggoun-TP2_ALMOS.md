AGGOUN
Bryan

## Compte-rendu du TP2 d'ALMOS-MKH


__Question 1:__

Le preloader est spécifique à la machine TSAR et indépendant de l'OS. Sont rôle est de charger le code du boot loader d'un périphérique de stockage vers le banc mémoire du cluster0.

__Question 2:__

De manière générale, le preloader, au démarage de la plateforme, se trouve dans une mémoire non volatile (une ROM). Il est ensuite chargé dans le banc mémoire du cluster0 pour être exécuté par le proc0 de ce même cluster.

Sur la machine TSAR, le preloader est chargé dans le banc mémoire physique du Cluster 0 par le microblaze du FPGA présent sur la plateforme. Il n'est donc pas présent en mémoire sur cette machine mais se trouve dans une des cartes SD de la carte FPGA.

__Question 3:__

Le boot loader sert à charger dans chaque banc mémoire de chaque cluster de la machine, une copie local du code kernel d'ALMOS-MKH.

__Question 4:__

La description des composants de la plateforme est rangée dans le fichier arch_info.bin.

__Question 5:__

Les cores sont identifiés par leurs bits d'extensions contenue dans un registre défini dont le nombre désigne le core X d'un cluster Y.

__Question 6:__

Le TTY est utilisé pour afficher des informations sur le déroulement de processus du boot.
Le périphérique IOC est utilisé pour accéder au disk et exécuter des transferts du disk vers la mémoire.

__Question 7:__

__Question 8:__

Pour qu'un code soit exécuté que par un seul core, il faut endormir les autres cores de la plateforme.

__Question 9:__

Au démarrage de la machine, les bits du registre d'extension d'adresse sont à 0. Ce qui signifie que chaque accès mémoire que feront les porcs X d'un cluster Y, iront vers le banc mémoire physique du cluster0. De plus, le preloader est placé à l'adresse 0x00000000 du banc mémoire du cluster0 et est donc accessible pour n'importe quel processeur faisant un accès mémoire à l'adresse 0x0 avec la veleur 0 pour ses bits d'extension d'adresse.

__Question 10:__

Les cores sortent du preloader lorsque le CP0 du cluster 0 les réveilles en leur envoyant une IPI. Ils exécutent alors le code du boot loader se trouvant dans le core 0 du cluster0.

__Question 11:__

Il est recopié dans le banc mémoire de chaque cluster afin qu'il puisse être exécuté en local.

__Question 12:__

Chaque core à besoin d'une pile qui lui est propre car après que chacun des cores aient mis à jour leur registre d'extension d'adresse, ils peuvent chacun exécuter le code du bootloader indépendament des autres cores. Il leur faut donc une pile chacun pour pouvoir gérer leurs propre variables local sans géner les autres cores qui exécutent ce même code. Plus généralement, chaque se met à exécuter un code et il est dont nécessaire qu'il ait sa propre pile d'exécution.

Les piles de chaque core d'un cluster sont placées à l'adresse 0x600000 - 4Ko * lid.
lid étant le numéro du core d'un cluster Y.

__Question 13:__

Chaque core obtient un résultat différent car cette fonction renvoie l'identifiant de chaque core et pour l'OS chaque core de chaque cluster possède un identifiant qui lui est propre. Afin de connaitre l'identifiant du core exécutant la fonction, get_core_indentifiers fait appel à une autre fonction hal_get_gid qui permet de renvoyer le bonne identifiant du core en allant regarder dans son registre d'extention d'adresse qui point vers lui même suite au bootloader.

__Question 14:__

Les descripteurs de threads idle sont rangés dans le segment kidle du banc mémoire physique de chaque cluster.

__Question 15:__

Il y a autant de threads IDLE qu'il y a de core sur la plateforme.

__Question 16:__

La liste des verrous utilisés par un thread est initialisé par l'appel de la fonction xlist_root_init.
Pour la création du thread idle, c'est la fonction kernel_init qui fait appel à cette fonction. Sinon, c'est la fonction thread_init qui s'en charge.
Par l'appel de la fonction thread_idle_init, on initialise encore les verrous car celle-ci fait appel à la fonction thread_init. Ceci se passe dans l'étape 0 de la fonction kernel_init.

__Question 17:__

Le rôle de la fonction cluster_init est d'initialiser dans un premier temps les constantes de la structure d'un cluster local, le cluster_manager.

__Question 18:__

La DQDT est initialisé le CP0 de chaque cluster de la plateforme. Cette initialisation se fait à l'étape 1 de la fonction kernel_init par l'appel de la fonction dqdt_init() qui est exécuté seulement par les CP0.

__Question 19:__

La structure DQDT est distribuée afin d'éviter les contentions mais aussi pour éviter les problèmes de cohérence.
En effet, centralisé --> contentions et répliqué --> problème de cohérences entre les cluster.
__Question 20:__

Cette fonction est nécessaire pour compléter l'initialisation du contrôleur d'interruption programmable. Plus précisement, cette fonction initialise les informations rattachées au contrôleur d'interruption programmable avancé local.

__Question 21:__

IPI signifie Inter Process Interrupt. Il s'agit d'une interruption généré par un CPU de la plateforme vers un autre CPU. C'est la méthode utilisé, par exemple, par le CP0 pour réveiller les CP0 des autres clusters au moment du boot de la machine.

__Question 22:__

VFS signifie Virtual File System. C'est une représentation virtuelle du descripteur de fichier.

__Question 23:__

Durant la phase de boot, il est nécessaire d'utiliser des barrières de synchronisation. En effet, les différentes étapes du boot de la machine necessitent que les CPU se synchronisent entre eux pour éviter que l'avance d'un CPU par rapport à un autre, empèche l'initialisation des autres CPU. Ainsi, sans barrières de synchronisation il est possible de rentrer dans le cas où le CP0 du cluster 0 écrase le code du preloader tandis que certains CPU n'en sont pas encore sortis.

__Question 24:__

Il est nécessaire d'avoir des barrières local pour synchroniser l'exécution des tâches des CPU d'un même cluster avec le CP0 de ce même cluster.
Il est nécessaire d'avoir des barrières remote pour synchroniser l'exécution des tâches des CP0 de chaque cluster entre eux.


