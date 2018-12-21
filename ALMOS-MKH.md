Ce fichier contient les infos essentielles à la compréhension du kernel ALMOS-MKH.

## La procédure du boot

Elle se compose de phases :

- La phase dépendante de l'architecture qui est implémenté par une procédure boot_loader spécifique à une architecture.
- La phase indépendante de l'architecture qui est implémenté par une procédure kernel_init générique.

# La procédure boot_loader

La tâche principale de cette procédure est d'instancier dans chacun des clusters, une copie local du code kernel d'ALMOS-MKH.
Ce code contient une description de l'architecture hardware, contenue dans la structure de données boot_info_t qui est de taille fixe. C'est le boot loader qui construit cette structure et qui la place au début de la copie local placé dans le segment kdata. Etant utilisé par la fonction kernel_init, cette structure doit être le premier object du segment kdata.

Dans le TSAR, le boot-loader utilise un pre-loader chargé dans la ROM externe. Ce pre-loader, a pour mission de chargé le code du boot-loader, de la plateforme de TSAR, d'un périphérique de stockage externe (disque dur) vers la mémoire (de chacun des cluster). Le pre-loader sujet, est spécifique à la machine TSAR et indépendante de l'OS, mais reste une méthode de chargement utilisé par d'autres OS tel que Linux ou encore NetBSD.

Le boot-loader, quand à lui, commence par allouer, dans chacun des cluster possédant un banc mémoire physique local, 5 zones mémoires de taille fixé afin d'y chargé différents fichiers binaires et strucures de données :

- Une copie du code préloader, chargé à l'adresse physique 0x0 (en réalité la zone mémoire est alloué mais elle est vide).
- Le code de la copie local du boot-loader, chargé à l'adresse 0x100000.
- Une copie local du fichier arch_info.bin, chargé à l'adresse 0x200000.
- Le fichier binaire du code kernel kernel.elf, chargé à l'adresse 0x400000.
- Une pile d'exécution, dont l'adresse de base est 0x500000.

(Sur la machine TSAR, le pre-loader est chargé dans le banc mémoire physique du Cluster 0 par le microblaze du FPGA présent sur la plateforme. Il n'est donc pas présent en mémoire sur cette machine mais se trouve dans une carte SD de la carte FPGA. Le proc0 du cluster0 va s'occuper d'exécuter le code du pre-loader présent maintenant dans le banc mémoire physique local et va permettre de chargé dans ce même bance mémoire le code du boot-loader à l'adresse 0x100000. Le proc0 exécute ainsi le code du boot-loader. Une IIP (Interrupt Inter Process) va être envoyé à chacun des procs de chacun des cluster pour faire fetche la première instruction de la phase 2 du code du boot-loader par chacun des proc0 de chaque cluster. Chaque proc0 de chacun des cluster va donc exécuter la phase 2 du code du boot-loader qui consiste à allouer dans le banc mémoire local de chacun des clusters, les cinqs zones mémoires qui va permettre le boot de chacun des cluster. Une fois les copies chargées et la pile d'exécution local créé dans chaque banc mémoires physique de chaque cluster, une dernière instruction de la phase 2 du code du boot-loader va se charger de modifier le contenue du registre 25 du proc0 de chaque cluster pour que ceux-ci exécute la suite du code boot-loader en local (ils exécutent leur copie local du code du boot-loader en mémoire physique local à leur cluster)).





