# QUESTION 

qui a deux partions sde de 20g su un disque sda de 25g en utilisant la commande dd on te demande Attention ! Il est crucial d’effectuer les étapes suivantes de manière rigoureuse !
Les modifications réalisées avec fdisk ne sont effectives que lorsque vous demandez l'écriture
• Exécutez la commande fdisk de manière interactive sur DiskFinal.
• Expliquez la signification du message en rouge qui apparaît et pourquoi nous l’obtenons.
• Affichez la liste des partitions et identifiez la plus grosse.
◦ Pour cette partition, relevez les secteurs de début et de fin en faisant un copier/coller dans un
éditeur de texte.5
◦ Supprimez cette partition. Affichez à nouveau la liste des partitions.
• Créez une nouvelle partition avec :
◦ Le même numéro de partition,
◦ Le même numéro de secteur de départ que la partition supprimée,
◦ Pour le secteur de fin, laissez la valeur proposée par défaut par fdisk (fin du disque).
• Affichez à nouveau la liste des partitions puis si tout vous semble correct, écrivez la nouvelle table
des partitions sur le disque.
• Exécutez à nouveau la commande précédente pour obtenir l’espace libre sur le système de fichiers
de la plus grosse partition.
• Que constatez vous ?
• Exécutez la commande suivante pour instruire son système de fichiers d’occuper tout l’espace
disponible de la partition qui le contient : sudo resize2fs <DiskFinal/PartMax>
Exécutez la commande e2fsck si resize2fs vous le demande puis recommencez.
• Exécutez à nouveau la commande donnant l’espace libre sur le système de fichiers de PartMax.
Que constatez vous ?
----
Pour répondre à cette demande, voici les étapes que vous devez suivre pour redimensionner une partition après avoir cloné un disque sur un autre disque plus grand, tout en utilisant des commandes sous Linux.

### 1. **Exécuter `fdisk` sur le nouveau disque (sda)**

Pour commencer, vous devez utiliser la commande `fdisk` pour ouvrir une session interactive sur le disque `sda` :

```bash
sudo fdisk /dev/sda
```

### 2. **Message en rouge**

Dans `fdisk`, il peut y avoir un message d'avertissement en rouge du type : "The partition table has been altered." ou "Partition 1 does not start on physical sector boundary."

- **Signification :** Ce message indique que vous avez modifié la table de partition et que ces changements ne prendront effet qu'une fois que vous aurez demandé l'écriture explicite de la nouvelle table de partitions. Cela s'explique parce que `fdisk` ne fait que manipuler une copie en mémoire de la table de partitions tant que vous n'avez pas confirmé son écriture.
- **Raison de l'obtention du message :** Vous voyez ce message parce que les modifications effectuées (par exemple, suppression et création de partitions) doivent être confirmées avant d'être réellement appliquées au disque.

### 3. **Lister les partitions**

Tapez la commande suivante dans `fdisk` pour afficher les partitions présentes :

```bash
p
```

Cela affichera la liste des partitions sur le disque. Identifiez la plus grosse partition, qui sera probablement celle de 20 Go (clonée depuis l’ancien disque).

### 4. **Copier les secteurs de départ et de fin**

Une fois que vous avez identifié la plus grosse partition, notez ses secteurs de départ et de fin. Vous pouvez copier ces informations dans un éditeur de texte pour les utiliser lors de la création de la nouvelle partition.

### 5. **Supprimer la plus grosse partition**

Supprimez la plus grosse partition identifiée avec la commande suivante dans `fdisk` :

```bash
d
```

Ensuite, choisissez le numéro de la partition que vous souhaitez supprimer (celle que vous avez identifiée).

### 6. **Créer une nouvelle partition**

Maintenant, recréez la partition avec les mêmes secteurs de début que la partition supprimée. Utilisez la commande suivante pour commencer :

```bash
n
```

Suivez les étapes suivantes :
- **Type de partition :** primaire (ou selon les besoins du système),
- **Numéro de la partition :** utilisez le même numéro que celui de la partition supprimée,
- **Secteur de départ :** collez le secteur de départ que vous aviez noté,
- **Secteur de fin :** laissez la valeur par défaut proposée par `fdisk`, qui sera la fin du disque (vous utiliserez ainsi tout l’espace disponible).

### 7. **Lister à nouveau les partitions**

Après avoir créé la nouvelle partition, tapez à nouveau la commande `p` pour lister les partitions et vérifier que tout est correct.

### 8. **Écrire la table des partitions**

Si tout vous semble correct, écrivez la nouvelle table de partitions avec la commande suivante :

```bash
w
```

Cela rendra les changements permanents sur le disque.

### 9. **Vérifier l'espace disponible**

Une fois la nouvelle partition créée et la table écrite, vous devez vérifier l’espace libre sur le système de fichiers de cette partition. Utilisez la commande suivante :

```bash
df -h
```

Vous constaterez probablement que l’espace libre n’a pas encore changé, car le système de fichiers ne sait pas encore qu’il peut utiliser tout l’espace de la partition.

### 10. **Redimensionner le système de fichiers**

Pour permettre au système de fichiers d’occuper tout l’espace disponible, utilisez la commande `resize2fs` :

```bash
sudo resize2fs /dev/sdaX
```

(Remplacez `/dev/sdaX` par la partition correspondante.)

Si `resize2fs` vous demande d’exécuter `e2fsck`, faites-le avant de réessayer :

```bash
sudo e2fsck -f /dev/sdaX
sudo resize2fs /dev/sdaX
```

### 11. **Vérifier l'espace libre après redimensionnement**

Enfin, exécutez à nouveau la commande `df -h` pour vérifier l’espace libre sur le système de fichiers. Vous devriez maintenant constater que le système de fichiers utilise tout l’espace disponible sur la partition.

### Conclusion

Après toutes ces étapes, vous aurez réussi à redimensionner la partition pour utiliser l'espace total disponible du disque.
----

# Quetion

Exécutez à nouveau la commande donnant l’espace libre sur le système de fichiers de sda2
Que constatez vous ?
----
Voici comment procéder pour l'observation finale, en prenant en compte la gestion des UUID des disques et la suppression du contenu du disque source (DiskInitial).

### 1. **Lister les UUID des périphériques de type bloc**

Pour lister tous les UUID des périphériques de type bloc, vous pouvez utiliser la commande suivante :

```bash
lsblk -f
```

### Ce que vous constatez

- Vous allez voir les **UUID** de chaque périphérique, qui sont uniques pour chaque système de fichiers.
- Si vous comparez les UUID avant et après le clonage, vous constaterez que les UUID du système cloné (DiskFinal) **sont les mêmes que ceux du disque source (DiskInitial)**. Cela peut entraîner des conflits si les deux disques sont présents dans le système au redémarrage, car les UUID sont utilisés pour identifier les partitions dans des fichiers comme `/etc/fstab` ou pour le montage automatique.

### **Risques si vous redémarrez le système avec les deux disques**

Si vous laissez les deux disques (DiskInitial et DiskFinal) avec les mêmes UUID :
- Le système pourrait tenter de monter la partition d'origine ou celle clonée, provoquant un conflit. Cela pourrait empêcher le bon fonctionnement du démarrage.
- Il pourrait y avoir un risque de corruption ou de confusion des points de montage, car le système pourrait ne pas savoir quel disque utiliser.

### 2. **Lister les périphériques de type bloc**

Avant de procéder à l'effacement du premier disque, vous pouvez lister les périphériques de type bloc à l'aide de la commande suivante pour vérifier les disques présents :

```bash
lsblk
```

Cela vous permettra de vérifier que vous avez bien identifié le disque source (DiskInitial) avant d'exécuter une commande dangereuse.

### 3. **Effacer la table des partitions du premier disque (DiskInitial)**

Cette commande va écrire des zéros sur les premiers secteurs du disque, effaçant ainsi la table des partitions du disque source. Assurez-vous d'identifier correctement le disque pour éviter toute perte de données non souhaitée.

La commande pour effacer le contenu du disque DiskInitial :

```bash
sudo dd if=/dev/zero of=/dev/sde bs=1k count=100 status=progress
```

(Remplacez `/dev/sde` par le nom du périphérique DiskInitial, tel que vous l'avez identifié avec `lsblk`.)

### **Précautions**

- Cette commande va **effacer définitivement** les informations de la table des partitions de DiskInitial. Soyez certain que vous voulez le faire avant de l'exécuter.
- Vérifiez à plusieurs reprises que vous utilisez le bon disque, car l'erreur peut entraîner une perte de données.

### 4. **Vérifier que le contenu de DiskInitial a disparu**

Après avoir exécuté la commande `dd`, vous pouvez à nouveau lister les périphériques de type bloc pour vérifier que la table des partitions de DiskInitial a été supprimée :

```bash
lsblk
```

Si l’opération s’est bien déroulée, DiskInitial ne devrait plus avoir de partitions listées.

---

### Conclusion

- Vous avez cloné un disque sur un autre disque plus grand, mais les UUID étant les mêmes, cela pose des risques de conflits au redémarrage.
- En effaçant la table des partitions de DiskInitial avec `dd`, vous éliminez ce risque en supprimant les informations de l'ancien disque, garantissant que le système démarrera uniquement sur le nouveau disque (DiskFinal).

# Question

Une fois que votre VM a redémarré sur votre système installé, connectez-vous avec votre utilisateur.
◦ Exécutez la commande lsblk.
◦ Comment pouvez-vous vérifier que votre système à bien démarré sur le nouveau disque ?
◦ Identifiez à nouveau les périphériques de type bloc et demandez l’espace libre des système de
fichiers avec la commande df -h
----
Voici les étapes pour vérifier que votre machine virtuelle (VM) a bien démarré sur le nouveau disque après le redémarrage.

### 1. **Exécuter la commande `lsblk`**

Après avoir redémarré votre VM, connectez-vous avec votre utilisateur et exécutez la commande suivante :

```bash
lsblk
```

Cette commande affiche la liste des périphériques de type bloc (disques et partitions) connectés à votre système. Vous y verrez les disques et leurs partitions.

### 2. **Vérifier que votre système a bien démarré sur le nouveau disque**

Pour vérifier que votre système a démarré sur le nouveau disque (`sda`), vous pouvez comparer les informations obtenues par `lsblk` avec ce que vous savez du disque cloné (`sda` en l'occurrence).

Voici quelques moyens de confirmer :

- **Taille du disque** : Le disque sur lequel votre système a démarré doit correspondre à la taille du nouveau disque (25 Go). Si vous voyez que la taille totale du disque sur lequel le système de fichiers racine (`/`) est monté correspond à cette taille, vous pouvez conclure qu'il s'agit du nouveau disque.
  
- **Point de montage** : En utilisant `lsblk`, regardez sous quelle partition le répertoire racine (`/`) est monté. Si `/dev/sdaX` est monté sur `/`, cela signifie que le système a démarré sur le disque `sda`, le nouveau disque.

- **UUIDs** : Si vous voulez être encore plus précis, comparez les UUIDs du nouveau disque en utilisant la commande `blkid` :

  ```bash
  sudo blkid /dev/sdaX
  ```

  Cela vous permettra de voir si l'UUID de la partition racine correspond à celui du nouveau disque, confirmant que le démarrage s'est fait sur celui-ci.

### 3. **Identifier à nouveau les périphériques de type bloc**

Vous pouvez lister à nouveau les périphériques de type bloc pour vérifier la structure du disque et des partitions après le redémarrage :

```bash
lsblk
```

Cela vous donnera une vue claire des partitions présentes et de leurs points de montage.

### 4. **Demander l’espace libre sur les systèmes de fichiers**

Pour afficher l'espace libre disponible sur tous les systèmes de fichiers, utilisez la commande `df -h` :

```bash
df -h
```

Cette commande affichera la taille totale, l'espace utilisé, et l'espace disponible pour chaque partition montée. Vous pourrez confirmer que la partition principale (celle contenant `/`) utilise bien toute la taille du nouveau disque (25 Go), et que l'espace libre est conforme à vos attentes après le redimensionnement de la partition.

### Ce que vous constatez :

- La partition principale (`/`) doit avoir une taille plus grande (environ 25 Go) après avoir cloné le disque et redimensionné le système de fichiers.
- Vous ne devriez plus voir l'ancien disque (DiskInitial) dans la liste des périphériques de type bloc, car il a été supprimé ou effacé.
  
En résumé, la commande `lsblk` vous permet de vérifier que le système a démarré sur le nouveau disque, et `df -h` vous permet de vérifier l’espace libre disponible après redimensionnement.
----
# Question
Redémarrez votre système en mode live comme indiqué à la section III .
Attention !! Dans la suite, nous appellerons :
DiskInitial Le second disque de forte capacité qui a été ajouté à votre VM et sur lequel est
maintenant installé Linux ubuntu-server,
DiskFinal Le disque initial de votre VM qui est maintenant sans aucune partition.
• Recherchez dans le man de la commande lsblk l’effet du commutateur -f.
En utilisant tous les outils nécessaires, pour chacune des partitions existantes, relevez :
• Le numéro de partition,
• La taille de la partition,
• Le type associé à la partition,
• Le type de système de fichiers contenu dans la partition,
• La version de ce système de fichiers,
• L’UUID du système de fichiers.
Relevez également le type de partitionnement (normalement déjà connu)
----
Pour accomplir ces tâches, voici les étapes détaillées que vous devez suivre après avoir redémarré votre système en **mode live** avec les disques `DiskInitial` et `DiskFinal` comme définis.

### 1. **Rechercher dans le man de la commande `lsblk` l'effet du commutateur `-f`**

Pour comprendre l’effet du commutateur `-f` dans la commande `lsblk`, vous pouvez utiliser la commande suivante pour accéder au manuel de la commande :

```bash
man lsblk
```

Dans le manuel, recherchez la section relative au commutateur `-f`. Voici un résumé de ce que fait ce commutateur :
- **`-f` :** Affiche des informations sur les systèmes de fichiers associés aux périphériques de type bloc, y compris le type de système de fichiers, l'UUID, l'étiquette, etc.

### 2. **Lister les partitions avec `lsblk -f`**

Après avoir redémarré en mode live, utilisez la commande suivante pour afficher toutes les informations sur les partitions présentes sur les disques `DiskInitial` et `DiskFinal` :

```bash
lsblk -f
```

Cette commande va afficher :

- Le numéro de la partition,
- La taille de la partition,
- Le type de la partition (périphérique de type bloc),
- Le système de fichiers contenu dans la partition (si applicable),
- L'UUID des systèmes de fichiers,
- Les labels (si existants).

### 3. **Utiliser des outils supplémentaires si nécessaire**

Si certaines informations ne sont pas affichées avec `lsblk -f`, vous pouvez utiliser les commandes suivantes pour obtenir des informations plus détaillées sur les partitions et systèmes de fichiers.

#### a) **Obtenir les informations sur la partition**

Pour chaque partition, vous pouvez utiliser la commande `fdisk` pour obtenir des informations plus détaillées sur le partitionnement du disque et vérifier les numéros de partitions et le type de partitionnement (MBR ou GPT) :

```bash
sudo fdisk -l /dev/sda  # Pour DiskFinal
sudo fdisk -l /dev/sdb  # Pour DiskInitial
```

Cette commande vous donnera des informations sur :
- Le numéro de partition,
- La taille de chaque partition,
- Le type de partitionnement (MBR ou GPT),
- Le type de chaque partition.

#### b) **Obtenir les informations détaillées sur les systèmes de fichiers**

Si vous voulez obtenir des informations sur la version du système de fichiers, vous pouvez utiliser la commande `dumpe2fs` pour les partitions de type `ext` (comme `ext4`) :

```bash
sudo dumpe2fs /dev/sdaX | grep 'Filesystem revision'
```

(Remplacez `/dev/sdaX` par le numéro de partition correspondant.)

#### c) **Rechercher l’UUID et la version du système de fichiers**

Vous pouvez également utiliser la commande suivante pour obtenir l'UUID des partitions ainsi que le type de système de fichiers (si `lsblk -f` ne le montre pas) :

```bash
sudo blkid
```

### 4. **Informations à collecter pour chaque partition**

Voici ce que vous devez relever pour chaque partition existante sur `DiskInitial` et `DiskFinal` :

1. **Numéro de partition :** Relevé via `lsblk` ou `fdisk`.
2. **Taille de la partition :** Relevée via `lsblk` ou `fdisk`.
3. **Type de partition :** Relevé via `fdisk` (MBR ou GPT pour le disque entier, et `Primary`, `Extended`, etc. pour les partitions).
4. **Type de système de fichiers :** Affiché par `lsblk -f` ou `blkid`.
5. **Version du système de fichiers :** Pour les systèmes `ext`, utilisez `dumpe2fs`.
6. **UUID du système de fichiers :** Affiché par `lsblk -f` ou `blkid`.

### 5. **Type de partitionnement**

Le type de partitionnement du disque devrait déjà être connu (probablement GPT si vous utilisez un système moderne). Si vous ne l’avez pas encore vérifié, vous pouvez le faire avec `fdisk -l`. 

Si vous voyez une mention comme **"Disklabel type: gpt"** dans la sortie de `fdisk`, cela signifie que le disque utilise GPT (GUID Partition Table). S’il est marqué **"Disklabel type: dos"**, cela signifie que le disque utilise MBR (Master Boot Record).

### Exemple de commande complète

Voici un exemple de commande qui pourrait être utilisée pour extraire toutes les informations nécessaires d’un disque (`/dev/sda` ou `/dev/sdb`) :

```bash
sudo lsblk -f    # Liste des partitions et systèmes de fichiers avec UUID
sudo fdisk -l /dev/sda  # Informations détaillées sur les partitions
sudo blkid       # Informations détaillées sur les UUID
sudo dumpe2fs /dev/sdaX | grep 'Filesystem revision'  # Version du système de fichiers ext
```

### Conclusion

En utilisant ces outils, vous pourrez identifier toutes les partitions et systèmes de fichiers existants sur les disques `DiskInitial` et `DiskFinal`, collecter les informations demandées (numéros, tailles, types de systèmes de fichiers, UUID), et confirmer le type de partitionnement.
---
# Question
• Créez sur DiskFinal (sde de 20G)une structure de partitions similaire à celle de DiskInitial (sda avec deux partition 1 de 953M type uefi file  system format vfat fat32 de de  et la partition 2 de type ext4 1.0 de 24.4G).
Vous veillerez à respecter :
◦ Le type de partitionnement,
◦ La taille des partitions : une taille similaire pour la partition EFI et le reste du disque pour la
partition du système,
◦ Le type des partitions.
• Comparez vos deux disques à l’aide des commandes fdisk -l et lsblk -f. 
---
Pour créer une structure de partitions similaire sur le disque `DiskFinal` (sde) à celle du disque `DiskInitial` (sda) et effectuer la comparaison, suivez les étapes ci-dessous. Vous utiliserez des outils tels que `fdisk` et `mkfs` pour créer les partitions et formater les systèmes de fichiers.

### Étapes pour créer la structure de partitions sur `DiskFinal` (sde)

1. **Lancer `fdisk` pour partitionner DiskFinal**

   Lancez `fdisk` pour gérer les partitions du disque `/dev/sde` (DiskFinal) :

   ```bash
   sudo fdisk /dev/sde
   ```

2. **Vérifiez le type de partitionnement (GPT ou MBR)**

   Lors de la création des partitions, vous devrez respecter le type de partitionnement de DiskInitial. Si DiskInitial utilise GPT, vous devrez vous assurer que DiskFinal utilise aussi GPT (si nécessaire, utilisez la commande `g` pour créer une nouvelle table de partition GPT dans `fdisk`).

   - **GPT (GUID Partition Table)** : Utilisé par les systèmes UEFI modernes.
   - **MBR (Master Boot Record)** : Système plus ancien, principalement utilisé sur les systèmes BIOS.

   Si vous avez besoin de GPT, tapez `g` pour créer une nouvelle table de partition GPT dans `fdisk`.

3. **Créer la partition EFI (953 Mo)**

   a) Tapez `n` pour créer une nouvelle partition. 
   b) Entrez `1` pour le numéro de partition.
   c) Pour le secteur de début, laissez la valeur par défaut.
   d) Entrez `+953M` pour la taille de la partition.
   e) Tapez `t` pour changer le type de partition. Entrez `1` pour changer la partition 1. Ensuite, tapez `EFI` ou `1` pour choisir le type **EFI System**.

4. **Créer la partition principale (ext4, le reste du disque)**

   a) Tapez `n` pour créer une deuxième partition. 
   b) Entrez `2` pour le numéro de partition.
   c) Pour le secteur de début, laissez la valeur par défaut.
   d) Pour la taille, laissez aussi la valeur par défaut pour utiliser tout l'espace restant sur le disque.
   e) La partition par défaut sera de type **Linux filesystem**, ce qui convient pour ext4.

5. **Vérifiez les partitions**

   Tapez `p` dans `fdisk` pour afficher la liste des partitions et vérifier que vous avez bien deux partitions avec les tailles correctes.

6. **Écrire les modifications sur le disque**

   Une fois satisfait de la configuration, tapez `w` pour écrire la nouvelle table de partitions sur le disque.

### Formater les partitions

Maintenant que les partitions sont créées, vous devez formater chacune d’elles avec le bon système de fichiers.

1. **Formater la partition EFI en FAT32**

   Utilisez la commande suivante pour formater la partition 1 (`/dev/sde1`) en FAT32 pour l'EFI :

   ```bash
   sudo mkfs.vfat -F32 /dev/sde1
   ```

2. **Formater la partition système en ext4**

   Utilisez la commande suivante pour formater la partition 2 (`/dev/sde2`) en ext4 :

   ```bash
   sudo mkfs.ext4 /dev/sde2
   ```

### Comparer les disques avec `fdisk -l` et `lsblk -f`

1. **Comparer avec `fdisk -l`**

   Utilisez la commande `fdisk -l` pour lister les partitions sur les deux disques (`/dev/sda` et `/dev/sde`) :

   ```bash
   sudo fdisk -l /dev/sda
   sudo fdisk -l /dev/sde
   ```

   Vous devriez voir une sortie similaire pour les deux disques, avec une partition EFI (~953M) et une partition principale (~24.4G sur DiskInitial, 20G sur DiskFinal).

2. **Comparer avec `lsblk -f`**

   Utilisez la commande `lsblk -f` pour voir les systèmes de fichiers et les UUID sur chaque disque :

   ```bash
   lsblk -f /dev/sda
   lsblk -f /dev/sde
   ```

   La sortie affichera :
   - Les numéros de partitions,
   - La taille des partitions,
   - Le type de système de fichiers (vfat pour EFI, ext4 pour la partition principale),
   - L’UUID de chaque système de fichiers.

### Résultat attendu

Après avoir suivi ces étapes :

- **`DiskInitial` (/dev/sda)** : Devrait avoir une partition EFI de 953M en FAT32 et une partition principale de 24.4G en ext4.
- **`DiskFinal` (/dev/sde)** : Devrait avoir une partition EFI de 953M en FAT32 et une partition principale de 19-20G en ext4.

La commande `fdisk -l` montrera que les deux disques ont des partitions avec les mêmes types et tailles (en dehors de la différence de taille totale du disque). La commande `lsblk -f` confirmera que les systèmes de fichiers (vfat et ext4) sont correctement créés et montés.
---
# Question

Notre disque DiskFinal contient maintenant un schéma de partition et des systèmes de fichiers identiques au
disque d’origine DiskInitial mais ne contiennent aucune donnée. Nous allons recopier ces dernières, cette
fois, fichier par fichier.
Nous allons effectuer cette copie en tant que root. Cependant, si nous ne prenons pas de précautions
particulières, root sera le propriétaire des copies, leurs dates de création et de modification seront définies à la
date de la copie, et le droits d’accès seront ceux des droits par défaut de création de nouveaux fichiers pour
root.
• Recherchez dans la documentation de la commande cp le(s) commutateur(s) qui permet(tent) de
conserver toutes les méta-données des fichiers lors de la copie. Recherchez également le commutateur
qui permet de faire une copie en profondeur (récursive) pour copier le contenu des sous-répertoires. • Créez deux répertoires /mnt/initial et /mnt/final.
• Montez sur ces répertoires les partitions EFI des disques correspondant. Vérifiez à l’aide de la
commande ls -la le contenu de ces deux répertoires.
• Effectuez la copie des fichiers. Vérifiez à l’aide de la commande ls -la que le contenu de ces deux
répertoires est maintenant identique.
• Démontez les deux systèmes de fichiers.
----

Voici les étapes détaillées pour copier les données d'un disque à l'autre tout en conservant les méta-données (propriétaires, droits, dates) des fichiers, et en vérifiant le contenu des partitions EFI de `DiskInitial` et `DiskFinal` après montage et copie.

### 1. **Rechercher dans la documentation de la commande `cp` les commutateurs appropriés**

Dans la documentation de la commande `cp`, vous pouvez utiliser les commutateurs suivants pour conserver toutes les méta-données des fichiers et copier récursivement les répertoires :

- **`-a` (archive)** : Ce commutateur permet de conserver toutes les méta-données des fichiers (propriétaire, droits d’accès, dates de modification, liens symboliques) et copie récursivement tout le contenu.
- **`-r` (recursive)** : Utilisé pour copier en profondeur, c’est-à-dire inclure les sous-répertoires. Le commutateur `-a` inclut déjà cette option.
- **`-v` (verbose)** : Ce commutateur est optionnel et permet d'afficher les fichiers en cours de copie.

La commande que vous utiliserez sera donc `cp -a`.

### 2. **Créer les répertoires `/mnt/initial` et `/mnt/final`**

Créez deux répertoires où vous monterez les partitions EFI des disques `DiskInitial` et `DiskFinal` :

```bash
sudo mkdir /mnt/initial
sudo mkdir /mnt/final
```

### 3. **Monter les partitions EFI des deux disques**

Montez les partitions EFI (de type FAT32) sur ces répertoires :

- Pour **DiskInitial** (`/dev/sda1`), qui est la partition EFI de l'ancien disque :
  
  ```bash
  sudo mount /dev/sda1 /mnt/initial
  ```

- Pour **DiskFinal** (`/dev/sde1`), qui est la partition EFI du nouveau disque :

  ```bash
  sudo mount /dev/sde1 /mnt/final
  ```

### 4. **Vérifier le contenu des répertoires montés**

Une fois les partitions montées, utilisez la commande `ls -la` pour vérifier le contenu de ces deux répertoires. Cela vous permettra de vous assurer que vous accédez bien aux données EFI.

- Pour vérifier le contenu de `/mnt/initial` :

  ```bash
  ls -la /mnt/initial
  ```

- Pour vérifier le contenu de `/mnt/final` :

  ```bash
  ls -la /mnt/final
  ```

Vous devriez voir les fichiers et dossiers EFI typiques, comme `EFI`, `boot`, etc.

### 5. **Copier les fichiers tout en conservant les méta-données**

Pour copier les fichiers de la partition EFI de `DiskInitial` vers celle de `DiskFinal`, utilisez la commande suivante :

```bash
sudo cp -a /mnt/initial/* /mnt/final/
```

Cette commande va :

- **Recopier tout le contenu de la partition EFI de `DiskInitial`** (fichiers et sous-répertoires) vers la partition EFI de `DiskFinal`.
- **Conserver les méta-données** des fichiers d'origine (propriétaires, droits d'accès, dates de modification).

### 6. **Vérifier que le contenu des répertoires est identique**

Après la copie, vérifiez à nouveau le contenu des deux répertoires pour vous assurer qu'ils sont identiques :

- Pour vérifier le contenu de `/mnt/initial` :

  ```bash
  ls -la /mnt/initial
  ```

- Pour vérifier le contenu de `/mnt/final` :

  ```bash
  ls -la /mnt/final
  ```

Comparez la sortie des deux commandes pour vérifier que tous les fichiers et répertoires ont été copiés correctement avec les mêmes permissions, propriétaires, et dates de modification.

### 7. **Démonter les partitions**

Une fois la copie terminée et vérifiée, démontez les deux systèmes de fichiers pour libérer les points de montage :

- Pour démonter la partition EFI de `DiskInitial` :

  ```bash
  sudo umount /mnt/initial
  ```

- Pour démonter la partition EFI de `DiskFinal` :

  ```bash
  sudo umount /mnt/final
  ```

### Résumé des commandes utilisées :

1. Créer les répertoires de montage :
   ```bash
   sudo mkdir /mnt/initial /mnt/final
   ```

2. Monter les partitions EFI :
   ```bash
   sudo mount /dev/sda1 /mnt/initial
   sudo mount /dev/sde1 /mnt/final
   ```

3. Vérifier le contenu des répertoires montés :
   ```bash
   ls -la /mnt/initial
   ls -la /mnt/final
   ```

4. Copier les fichiers avec conservation des méta-données :
   ```bash
   sudo cp -a /mnt/initial/* /mnt/final/
   ```

5. Vérifier que le contenu est identique :
   ```bash
   ls -la /mnt/initial
   ls -la /mnt/final
   ```

6. Démonter les partitions :
   ```bash
   sudo umount /mnt/initial
   sudo umount /mnt/final
   ```

Cette procédure assure que toutes les données sont correctement copiées avec les mêmes propriétés (méta-données) sur la nouvelle partition EFI, tout en respectant les structures et permissions des fichiers.
---
# Question
Montez sur les répertoires /mnt/initial et /mnt/final les partitions systèmes des disques
correspondant. Vérifiez à l’aide de la commande ls -la le contenu de ces deux répertoires.
Note : on observe deux choses : le système de fichiers cible n’est pas vide. Il contient un répertoire lost+found
destiné à sa maintenance. On voudra éviter de l’écraser avec celui du système de fichiers d’origine. Ensuite, le
système de fichiers d’origine contient un énorme fichier d’échange swap.img qu’il n’est pas nécessaire de
copier.
• À l’aide de la commande echo, vérifiez que le motif /mnt/initial/!(swap.img|lost+found)
vous donne bien la liste des fichiers du répertoire source moins les deux que nous ne souhaitons pas
copier.
• Effectuez la copie, conforme comme précédemment, des fichiers en utilisant ce motif. Pour comparer
l’efficacité de cette approche par rapport à un transfert par image, vous appliquerez la commande time
sur la commande cp. N’hésitez pas à utiliser la commande echo pour valider votre commande avant de
l’exécuter.
Vérifiez à l’aide de la commande ls -la le contenu de ces deux répertoires. Par acquis de conscience,
faites de même avec les sous-répertoires home.
• Démontez les deux systèmes de fichiers
----
Voici les étapes détaillées pour monter les partitions systèmes, exclure certains fichiers comme `swap.img` et `lost+found` lors de la copie, puis mesurer l’efficacité avec la commande `time`.

### 1. **Monter les partitions systèmes des deux disques**

Créez deux points de montage pour les partitions systèmes des disques `DiskInitial` et `DiskFinal`, puis montez-les :

- **Créer les répertoires de montage** :
  
  ```bash
  sudo mkdir /mnt/initial
  sudo mkdir /mnt/final
  ```

- **Monter la partition système de `DiskInitial`** :
  
  ```bash
  sudo mount /dev/sda2 /mnt/initial
  ```

- **Monter la partition système de `DiskFinal`** :
  
  ```bash
  sudo mount /dev/sde2 /mnt/final
  ```

### 2. **Vérifier le contenu des répertoires montés**

Après avoir monté les partitions, vérifiez le contenu des répertoires pour s’assurer que vous accédez correctement aux systèmes de fichiers.

- **Vérifier le contenu de `/mnt/initial`** :
  
  ```bash
  ls -la /mnt/initial
  ```

- **Vérifier le contenu de `/mnt/final`** :
  
  ```bash
  ls -la /mnt/final
  ```

Vous devriez observer la présence du répertoire **`lost+found`** sur le disque cible (`/mnt/final`), ainsi que le fichier **`swap.img`** sur le disque source (`/mnt/initial`), qui ne doivent pas être copiés.

### 3. **Vérifier le motif d’exclusion des fichiers avec `echo`**

Avant d’effectuer la copie, utilisez la commande `echo` pour tester le motif qui exclut les fichiers **`swap.img`** et **`lost+found`**. Ce motif utilise une expansion de bash appelée extglob. Activez-la d'abord avec la commande suivante :

```bash
shopt -s extglob
```

Ensuite, testez le motif d'exclusion :

```bash
echo /mnt/initial/!(swap.img|lost+found)
```

Cette commande affichera la liste des fichiers qui seront copiés, excluant les fichiers **`swap.img`** et **`lost+found`**. Si tout semble correct, vous pouvez passer à la copie.

### 4. **Effectuer la copie avec la commande `cp` et mesurer l’efficacité avec `time`**

Utilisez la commande `time` pour mesurer la durée de la copie, tout en conservant les méta-données et en excluant les fichiers non nécessaires :

```bash
time sudo cp -a /mnt/initial/!(swap.img|lost+found) /mnt/final/
```

Cette commande :
- Copiera tous les fichiers de `/mnt/initial` vers `/mnt/final`, tout en excluant les répertoires `lost+found` et `swap.img`.
- Conservera les permissions, propriétaires, et dates grâce à l’option `-a` (archive).
- Mesurera le temps d'exécution grâce à `time`.

### 5. **Vérifier le contenu des répertoires après la copie**

Après la copie, vérifiez à nouveau le contenu des deux répertoires pour vous assurer que les fichiers ont bien été copiés et que les fichiers exclus ne sont pas présents dans le système de destination :

- **Vérifier le contenu de `/mnt/final`** :

  ```bash
  ls -la /mnt/final
  ```

- **Vérifier le contenu du sous-répertoire `/mnt/final/home`** pour confirmer que les fichiers de l’utilisateur ont bien été copiés :

  ```bash
  ls -la /mnt/final/home
  ```

### 6. **Démonter les systèmes de fichiers**

Une fois la copie vérifiée, démontez les systèmes de fichiers des deux disques :

- **Démonter la partition de `DiskInitial`** :

  ```bash
  sudo umount /mnt/initial
  ```

- **Démonter la partition de `DiskFinal`** :

  ```bash
  sudo umount /mnt/final
  ```

### Résumé des commandes à utiliser :

1. **Créer les répertoires de montage** :
   ```bash
   sudo mkdir /mnt/initial /mnt/final
   ```

2. **Monter les partitions systèmes** :
   ```bash
   sudo mount /dev/sda2 /mnt/initial
   sudo mount /dev/sde2 /mnt/final
   ```

3. **Vérifier le contenu des répertoires montés** :
   ```bash
   ls -la /mnt/initial
   ls -la /mnt/final
   ```

4. **Tester le motif d’exclusion avec `echo`** :
   ```bash
   shopt -s extglob
   echo /mnt/initial/!(swap.img|lost+found)
   ```

5. **Effectuer la copie avec la commande `cp` et mesurer le temps** :
   ```bash
   time sudo cp -a /mnt/initial/!(swap.img|lost+found) /mnt/final/
   ```

6. **Vérifier le contenu des répertoires après la copie** :
   ```bash
   ls -la /mnt/final
   ls -la /mnt/final/home
   ```

7. **Démonter les systèmes de fichiers** :
   ```bash
   sudo umount /mnt/initial
   sudo umount /mnt/final
   ```

Cette approche permet d’éviter la copie inutile des fichiers comme `swap.img` et `lost+found`, tout en garantissant une copie fidèle des fichiers essentiels avec conservation des méta-données. La commande `time` vous donnera également une idée de l'efficacité par rapport à une copie de disque par image (comme via `dd`).
---
# Question
Assurez-vous d’avoir effectué toutes les opérations avec succès et que vos disques sont identiques.
• Effacez la table de partitions de DiskInitial comme nous l’avons fait pour la méthode précédente.
Vérifiez que les partitions de DiskInitial ont bien disparu.
• Redémarrez le système en éjectant le CDROM.
Votre système va redémarrer sur votre nouveau disque. Constatez-le en vous logeant avec votre utilisateur et en
vérifiant les points de montages actifs avec lsblk.
---
Voici les étapes finales pour effacer la table de partitions de `DiskInitial`, vérifier qu'il ne contient plus de partitions, redémarrer le système et s'assurer que tout fonctionne correctement avec votre nouveau disque.

### 1. **Effacer la table de partitions de `DiskInitial`**

Pour effacer la table de partitions de `DiskInitial` (ici identifié comme `/dev/sda`), utilisez la commande `dd` pour écrire des zéros sur les premiers secteurs du disque, comme indiqué dans la méthode précédente :

```bash
sudo dd if=/dev/zero of=/dev/sda bs=1k count=100 status=progress
```

Cette commande :

- Écrit des zéros sur les 100 premiers kilooctets de `DiskInitial`, ce qui effacera la table de partitions.
- Le commutateur `status=progress` permet de suivre l'évolution de l'opération en temps réel.

### 2. **Vérifier que les partitions de `DiskInitial` ont disparu**

Après avoir effacé la table de partitions, vérifiez que `DiskInitial` ne contient plus de partitions à l'aide de la commande `lsblk` ou `fdisk` :

- Avec `lsblk` :

  ```bash
  lsblk
  ```

  Vérifiez que `DiskInitial` (généralement `/dev/sda`) ne montre plus aucune partition.

- Avec `fdisk` :

  ```bash
  sudo fdisk -l /dev/sda
  ```

  Cette commande affichera un message indiquant qu'il n'y a pas de partition sur le disque.

### 3. **Redémarrer le système en éjectant le CDROM**

Éjectez le support d'installation (CDROM ou ISO) avant de redémarrer, pour vous assurer que le système démarre bien sur votre nouveau disque (`DiskFinal`, généralement `/dev/sde`).

- **Éjecter le CDROM/ISO** (si nécessaire via VMWare ou l'interface de la machine virtuelle).
  
- **Redémarrer le système** :

  ```bash
  sudo reboot
  ```

### 4. **Vérifier que le système a démarré sur le nouveau disque**

Une fois le système redémarré, connectez-vous avec votre utilisateur habituel. Ensuite, vérifiez les points de montage actifs avec la commande `lsblk` :

```bash
lsblk
```

Vous devriez voir que le système a démarré sur le nouveau disque (`DiskFinal`), qui devrait maintenant être `/dev/sda` après redémarrage (les noms de périphériques peuvent changer après suppression de l’ancien disque). Vérifiez les points de montage pour confirmer que les partitions système et EFI sont bien montées à leurs emplacements respectifs (`/`, `/boot/efi`, etc.).

### 5. **Vérification finale**

- Assurez-vous que tout fonctionne correctement, que les partitions sont montées comme prévu.
- Si vous avez des doutes sur le disque utilisé pour démarrer, vous pouvez également vérifier les UUID des systèmes de fichiers avec la commande `blkid` et comparer les résultats avec ceux relevés lors des opérations précédentes :

  ```bash
  sudo blkid
  ```

Cela confirmera que vous avez bien démarré sur le nouveau disque, et non sur un ancien ou un disque d’installation.

### Résumé des étapes :

1. **Effacer la table de partitions de `DiskInitial`** :

   ```bash
   sudo dd if=/dev/zero of=/dev/sda bs=1k count=100 status=progress
   ```

2. **Vérifier que les partitions ont disparu** :

   ```bash
   lsblk
   sudo fdisk -l /dev/sda
   ```

3. **Redémarrer le système** en éjectant le CDROM/ISO.

4. **Vérifier les points de montage** après redémarrage avec :

   ```bash
   lsblk
   sudo blkid
   ```

Si toutes ces opérations sont effectuées avec succès, votre système fonctionnera correctement sur le nouveau disque avec les mêmes données que le disque d'origine.