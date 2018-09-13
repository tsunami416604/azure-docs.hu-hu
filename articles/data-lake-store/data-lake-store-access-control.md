---
title: Hozzáférés-vezérlés a Data Lake Storage Gen1 áttekintése |} A Microsoft Docs
description: Hozzáférés-vezérlés működésének megismerése az Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: d16f8c09-c954-40d3-afab-c86ffa8c353d
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: 72bc0408ed1eba2d959d246a55677ee9964ef106
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44718814"
---
# <a name="access-control-in-azure-data-lake-storage-gen1"></a>Hozzáférés-vezérlés az Azure Data Lake Storage Gen1

Az Azure Data Lake Storage Gen1 egy hozzáférés-vezérlési modellből származó HDFS, a POSIX hozzáférés-vezérlési modellből származó valamint valósítja meg. Ez a cikk a Data Lake Storage Gen1 foglalja össze a hozzáférés-vezérlési modelljének alapjait. 

## <a name="access-control-lists-on-files-and-folders"></a>Fájlokra és mappákra vonatkozó hozzáférés-vezérlési listák

Kétféle hozzáférés-vezérlési lista (ACL) létezik – a **hozzáférési ACL** és az **alapértelmezett ACL**.

* **Hozzáférési ACL-ek**: Egy objektum hozzáféréseit vezérlik. A fájlok és mappák egyaránt rendelkeznek hozzáférési ACL-lel.

* **Alapértelmezett ACL**: A mappához társított ACL „sablon”, amely meghatározza az adott mappában létrehozott gyermekelemek hozzáférési ACL-jeit. A fájlok nem rendelkeznek alapértelmezett ACL-ekkel.


A hozzáférési ACL-ek és alapértelmezett ACL-ek ugyanazzal a struktúrával rendelkeznek.



> [!NOTE]
> Ha megváltoztatja az alapértelmezett ACL-eket egy szülő objektumon, akkor az nem módosítja a már létező gyermekelemek hozzáférési ACL-jeit és alapértelmezett ACL-jeit.
>
>

## <a name="users-and-identities"></a>Felhasználók és identitások

Minden fájl és mappa külön engedélyekkel rendelkezik az alábbi identitásokhoz:

* A tulajdonos felhasználó
* A tulajdonoscsoport
* Nevesített felhasználók
* Nevesített csoportok
* Minden egyéb felhasználó

A felhasználók és csoportok identitása Azure Active Directory- (Azure AD-) indentitás. Így ha nincs másképp jelölve, a "felhasználó" Data Lake Storage Gen1 környezetében is jelenthet egy Azure AD-felhasználó vagy az Azure AD biztonsági csoportot.

## <a name="permissions"></a>Engedélyek

A fájlrendszer objektumaira vonatkozó engedélyek a következők: **Olvasás**, **Írás**, és **Végrehajtás**. Ezek az engedélyek használhatók fájlok és mappák esetében a következő táblázatban látható módon:

|            |    Fájl     |   Mappa |
|------------|-------------|----------|
| **Olvasás (R)** | Olvashatja a fájl tartalmát | **Olvasás** és **Végrehajtás** szükséges a mappa tartalmának listázásához|
| **Írás (W)** | Írhatja a fájlt vagy hozzáfűzhet a fájlhoz | **Írás** és **Végrehajtás** szükséges gyermekelemek létrehozásához a mappában |
| **Végrehajtás (X)** | Nem jelent semmit a Data Lake Storage Gen1 környezetében | Szükséges egy mappa gyermekelemeinek bejárásához |

### <a name="short-forms-for-permissions"></a>Az engedélyek rövid alakjai

**RWX** az **Olvasás + Írás + Végrehajtás** engedélyt jelöli. Egy tömörebb, numerikus formátum is létezik, amelyben **Olvasás=4**, **Írás=2** és **Végrehajtás=1**, és az összegük jelöli az engedélyeket. Az alábbiakban néhány példa látható.

| Numerikus alak | Rövid alak |      Jelentés     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Olvasás + Írás + Végrehajtás |
| 5            | `R-X`        | Olvasás + Végrehajtás         |
| 4            | `R--`        | Olvasás                   |
| 0            | `---`        | Nincs engedély         |


### <a name="permissions-do-not-inherit"></a>Az engedélyek nem öröklődnek

A Data Lake Storage Gen1 által használt POSIX-stílusú modellben az elemhez tartozó engedélyek magában az elemben tárolódnak. Vagyis egy elem az engedélyeit nem örökölheti a szülőelemektől.

## <a name="common-scenarios-related-to-permissions"></a>Az engedélyekhez kapcsolódó gyakori helyzetek

Az alábbiakban néhány gyakori helyzet segítenek megérteni, milyen engedélyek szükségesek a Data Lake Storage Gen1-fiók bizonyos műveletek végrehajtásához.

|    Művelet             |    /    | Seattle / | Portland / | Data.txt     |
|--------------------------|---------|----------|-----------|--------------|
| Olvassa el a Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Data.txt hozzáfűzése       |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Data.txt törlése          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Data.txt létrehozása          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Listája /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| Lista /Seattle/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| Lista /Seattle/Portland /  |   `--X`   |   `--X`    |  `R-X`      | `---`          |


> [!NOTE]
> A fájlra vonatkozó írási engedélyek nem szükségesek a fájl törléséhez, amíg az előbbi két feltétel teljesül.
>
>


## <a name="the-super-user"></a>A felügyelő

A felügyelő rendelkezik a felhasználók a legtöbb joggal a Data Lake Storage Gen1 fiókban. A felügyelő:

* RWX-engedéllyel (olvasás, írás és végrehajtás) rendelkezik az **összes** fájlhoz és mappához.
* Bármely fájl vagy mappa engedélyeit megváltoztathatja.
* Bármely fájl vagy mappa tulajdonosát vagy tulajdonoscsoportját megváltoztathatja.

Minden felhasználó részét képező a **tulajdonosok** szerepkört egy Data Lake Storage Gen1 a fiókhoz a program automatikusan a felügyelők sem.

Ha létre szeretne hozni egy egyéni, szerepköralapú hozzáférés-vezérlési (RBAC) szerepkört, amely felügyelői engedélyekkel rendelkezik, akkor annak a következő engedélyekkel kell rendelkeznie:
- Microsoft.DataLakeStore/accounts/Superuser/action
- Microsoft.Authorization/roleAssignments/write


## <a name="the-owning-user"></a>A tulajdonos felhasználó

Automatikusan az elem tulajdonosa lesz az a felhasználó, aki létrehozta az elemet. A tulajdonos felhasználó:

* Megváltoztathatja a tulajdonában lévő fájl engedélyeit.
* megváltoztathatja a tulajdonában lévő fájl tulajdonos csoportját, ha a tulajdonos felhasználó szintén tagja ennek a csoportnak.

> [!NOTE]
> A tulajdonos *nem* változtathatja meg egy fájl vagy mappa tulajdonos felhasználóját. Csak a felügyelők változtathatják meg egy fájl vagy mappa tulajdonosát vagy tulajdonoscsoportját.
>
>

## <a name="the-owning-group"></a>A tulajdonoscsoport

**Háttér**

A POSIX ACL-ekben minden felhasználó társítva van egy „elsődleges csoporttal”. Például az „alice” nevű felhasználó a „finance” csoportba tartozhat. Alice több csoporthoz is tartozhat, de egy csoport mindig ki van jelölve elsődleges csoportjaként. A POSIX-ben ha Alice létrehoz egy fájlt, a fájl tulajdonoscsoportja Alice elsődleges csoportja lesz, ami ebben az esetben a „finance”. A tulajdonos csoport egyéb esetben egyéb felhasználókhoz/csoportokhoz hozzárendelt engedélyekhez hasonlóan viselkedik.

**Egy új fájl vagy mappa esetében a tulajdonoscsoport Assiging**

* **1. eset**: A gyökérmappa „/”. Ez a mappa a Data Lake Storage Gen1 fiók létrehozásakor jön létre. Ebben az esetben a tulajdonoscsoport azon felhasználó szerint lesz beállítva, aki létrehozta a fiókot.
* **2. eset** (minden egyéb eset): Egy új elem létrehozásakor a tulajdonoscsoport a szülőmappából másolódik át.

**A tulajdonoscsoport módosítása**

A tulajdonoscsoportot megváltoztathatja:
* Bármely felügyelő.
* a tulajdonos, ha szintén tagja ennek a csoportnak.

> [!NOTE]
> A tulajdonoscsoport *nem* változtathatja meg egy fájl vagy mappa ACL-eit.  Bár a gyökérmappa esetében a tulajdonoscsoport azon felhasználó szerint lesz beállítva, aki létrehozta a fiókot (**1. eset**, fent), egyetlen felhasználói fiók nem jogosult engedélyeket kiadni a tulajdonoscsoporton keresztül.  Az engedélyt hozzárendelheti egy érvényes felhasználócsoporthoz, ha van ilyen.


## <a name="access-check-algorithm"></a>Hozzáférés-ellenőrzési algoritmus

A következő pseudocode Data Lake Storage Gen1 fiókok esetében a hozzáférés-ellenőrzési algoritmus jelöli.

```
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or folder
  # Note: the "sticky bit" is not illustrated in this algorithm
  
# Handle super users.
  if (is_superuser(user)) :
    return True

  # Handle the owning user. Note that mask IS NOT used.
  entry = get_acl_entry( path, OWNER )
  if (user == entry.identity)
      return ( (desired_perms & e.permissions) == desired_perms )

  # Handle the named users. Note that mask IS used.
  entries = get_acl_entries( path, NAMED_USERS )
  for entry in entries:
      if (user == entry.identity ) :
          mask = get_mask( path )
          return ( (desired_perms & entry.permmissions & mask) == desired_perms)

  # Handle groups (named groups and owning group)
  member_count = 0
  perms = 0
  for g in get_groups(path) :
    if (user_is_member_of_group(user, g)) :
      member_count += 1
      perms | =  get_perms_for_group(path,g)
  if (member_count>0) :
    return ((desired_perms & perms & mask ) == desired_perms)
 
  # Handle other
  perms = get_perms_for_other(path)
  mask = get_mask( path )
  return ( (desired_perms & perms & mask ) == desired_perms)
```

### <a name="the-mask"></a>A maszk

A maszk, ahogyan a hozzáférés-ellenőrzési algoritmus, korlátozza a hozzáférést **nevesített felhasználókhoz**, a **tulajdonoscsoport**, és **nevesített csoportokhoz**.  

> [!NOTE]
> Az új Data Lake Storage Gen1-fiókoknál a gyökérmappa ("/") hozzáférési ACL-JÉNEK maszk alapértelmezés szerint RWX-re.
>
>

### <a name="the-sticky-bit"></a>Ragadós bit

A ragadós (sticky) bit a POSIX-fájlrendszer egy speciális funkciója. Data Lake Storage Gen1 összefüggésben nem valószínű, hogy a ragadós bit lesz szükség. Összefoglalva Ha a ragadós bit engedélyezve van egy mappába, a gyermekelem is csak kell törölték vagy átnevezték a gyermek-konfigurációelem tulajdonosa felhasználó által.

A ragadós bit nem látható az Azure Portalon.

## <a name="default-permissions-on-new-files-and-folders"></a>Új fájlok és mappák alapértelmezett engedélyek

Az új fájlok vagy mappák meglévő mappában történő létrehozásakor a szülőmappára vonatkozó alapértelmezett ACL a következőket határozza meg:

- Gyermekmappa alapértelmezett ACL-jei és hozzáférési ACL-jei.
- Gyermekfájl hozzáférési ACL-je (a fájloknak nincs alapértelmezett ACL-je).

### <a name="umask"></a>umask

Egy fájl vagy mappa létrehozásakor umask segítségével módosíthatja az alapértelmezett ACL-ek az alárendelt elem beállításának módját. umask egy 9 bites egy 9 bites érték, amely tartalmaz egy RWX-érték a fölérendelt mappák **tulajdonos**, **tulajdonoscsoport**, és **más**.

Az Azure Data Lake Storage Gen1 állandó érték, amely az umask 007 beállítása. Ezt az értéket a rendszer lefordítja arra

| umask összetevő     | Numerikus alak | Rövid alak | Jelentés |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | A tulajdonos felhasználó, másolja a szülő alapértelmezett ACL-je a gyermek hozzáférési ACL-je | 
| umask.owning_group  |    0         |   `---`      | A tulajdonoscsoport, másolja a szülő alapértelmezett ACL-je a gyermek hozzáférési ACL-je | 
| umask.Other         |    7         |   `RWX`      | Más a gyermek hozzáférési ACL-je a minden engedély eltávolítása |

Az Azure Data Lake Storage Gen1 hatékonyan használható umask érték azt jelenti, hogy az érték más soha nem továbbított alapértelmezés szerint az új gyermek - függetlenül az alapértelmezett ACL-t jelzi. 

A következő pseudocode bemutatja, hogyan kell alkalmazni az umask a hozzáférés-vezérlési egy gyermek-konfigurációelem létrehozása során.

```
def set_default_acls_for_new_child(parent, child):
    child.acls = []
    foreach entry in parent.acls :
        new_entry = None
        if (entry.type == OWNING_USER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_user))
        elif (entry.type == OWNING_GROUP) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_group))
        elif (entry.type == OTHER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.other))
        else :
            new_entry = entry.clone(perms = entry.perms )
        child_acls.add( new_entry )
```

## <a name="common-questions-about-acls-in-data-lake-storage-gen1"></a>A Data Lake Storage Gen1 ACL-lel kapcsolatos gyakori kérdésekre

### <a name="do-i-have-to-enable-support-for-acls"></a>Engedélyeznem kell az ACL támogatását?

Nem. Az ACL-alapú hozzáférés-vezérlés mindig be van kapcsolva egy Data Lake Storage Gen1 fiókot.

### <a name="which-permissions-are-required-to-recursively-delete-a-folder-and-its-contents"></a>Milyen engedélyek szükségesek egy mappa és tartalma rekurzív törléséhez?

* A szülőmappának rendelkeznie kell **Írás + Végrehajtás** engedélyekkel.
* A törölni kívánt mappához és a benne található összes mappához **Olvasás + Írás + Végrehajtás** engedélyre van szükség.

> [!NOTE]
> A mappákban található fájlok törléséhez nem szükséges írási engedély. Emellett a „/” gyökérmappa **soha** nem törölhető.
>
>

### <a name="who-is-the-owner-of-a-file-or-folder"></a>Ki a fájl vagy mappa tulajdonosa?

A fájl vagy mappa létrehozója lesz a tulajdonos.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-folder-at-creation"></a>Melyik csoport lett a fájl vagy mappa tulajdonoscsoportjaként beállítva a létrehozáskor?

A tulajdonoscsoport annak a szülőmappának a tulajdonoscsoportjából lesz kimásolva, amelyben az új fájlt vagy mappát létrehozzák.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Én vagyok egy fájl tulajdonosa, de nem rendelkezem a szükséges RWX-engedéllyel. Mit tegyek?

A tulajdonos módosíthatja a fájlhoz tartozó engedélyeket, így bármilyen szükséges RWX-engedélyt megadhat saját magának.

### <a name="when-i-look-at-acls-in-the-azure-portal-i-see-user-names-but-through-apis-i-see-guids-why-is-that"></a>Amikor megtekintem az ACL-eket az Azure Portalon, felhasználóneveket látok, az API-kon keresztül azonban GUID azonosítókat. Ez miért van?

Az ACL-ek bejegyzéseinek tárolása olyan GUID azonosítókként történik, amelyek az Azure AD-ban található felhasználóknak felelnek meg. Az API-k az aktuális állapotukban adják vissza a GUID azonosítókat. Az Azure Portal megpróbálja könnyebbé tenni az ACL-ek használatát a GUID azonosítóknak rövid nevekre történő lefordításával, amikor csak lehetséges.

### <a name="why-do-i-sometimes-see-guids-in-the-acls-when-im-using-the-azure-portal"></a>Miért látok néha GUID azonosítókat az ACL-eken, amikor az Azure Portalt használom?

Ha a felhasználó már nem létezik az Azure AD-ben, egy GUID lesz látható. Ez általában akkor történik, ha a felhasználó elhagyta a vállalatot, vagy törölve lett a fiókja az Azure AD-ben.

### <a name="does-data-lake-storage-gen1-support-inheritance-of-acls"></a>Támogatja a Data Lake Storage Gen1 ACL-ek öröklése?

Nem, az alapértelmezett ACL-ek azonban használhatók a szülő mappán belül újonnan létrehozott gyermekfájlok és mappák ACL-jeinek beállításához.  

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Hol tudhatok meg többet a POSIX hozzáférés-vezérlési modellről?

* [POSIX hozzáférés-vezérlési listák Linux rendszeren](https://www.linux.com/news/posix-acls-linux)
* [HDFS-engedélyek útmutatója](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)
* [POSIX – gyakori kérdések](http://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](http://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](http://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](http://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [POSIX ACL Ubuntu rendszeren](https://help.ubuntu.com/community/FilePermissionsACLs)
* [Hozzáférés-vezérlési listákat használó ACL Linux rendszeren](http://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Lásd még

* [Az Azure Data Lake Storage Gen1 áttekintése](data-lake-store-overview.md)
