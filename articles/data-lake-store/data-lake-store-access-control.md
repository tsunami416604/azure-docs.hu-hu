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
ms.openlocfilehash: 8680a8fa9c460983b88aa4845adcbe72d3a43abf
ms.sourcegitcommit: 465ae78cc22eeafb5dfafe4da4b8b2138daf5082
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44325515"
---
# <a name="access-control-in-azure-data-lake-storage-gen1"></a>Hozzáférés-vezérlés az Azure Data Lake Storage Gen1

Az Azure Data Lake Storage Gen1 egy hozzáférés-vezérlési modellből származó HDFS, a POSIX hozzáférés-vezérlési modellből származó valamint valósítja meg. Ez a cikk a Data Lake Storage Gen1 foglalja össze a hozzáférés-vezérlési modelljének alapjait. A HDFS hozzáférés-vezérlési modelljéről további információt a [HDFS Permissions Guide](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html) (HDFS-engedélyek útmutatója) oldalon talál.

## <a name="access-control-lists-on-files-and-folders"></a>Fájlokra és mappákra vonatkozó hozzáférés-vezérlési listák

Kétféle hozzáférés-vezérlési lista (ACL) létezik – a **hozzáférési ACL** és az **alapértelmezett ACL**.

* **Hozzáférési ACL-ek**: Egy objektum hozzáféréseit vezérlik. A fájlok és mappák egyaránt rendelkeznek hozzáférési ACL-lel.

* **Alapértelmezett ACL**: A mappához társított ACL „sablon”, amely meghatározza az adott mappában létrehozott gyermekelemek hozzáférési ACL-jeit. A fájlok nem rendelkeznek alapértelmezett ACL-ekkel.

![Data Lake Storage Gen1 ACL-EK](./media/data-lake-store-access-control/data-lake-store-acls-1.png)

A hozzáférési ACL-ek és alapértelmezett ACL-ek ugyanazzal a struktúrával rendelkeznek.

![Data Lake Storage Gen1 ACL-EK](./media/data-lake-store-access-control/data-lake-store-acls-2.png)



> [!NOTE]
> Ha megváltoztatja az alapértelmezett ACL-eket egy szülő objektumon, akkor az nem módosítja a már létező gyermekelemek hozzáférési ACL-jeit és alapértelmezett ACL-jeit.
>
>

## <a name="users-and-identities"></a>Felhasználók és identitások

Minden fájl és mappa külön engedélyekkel rendelkezik az alábbi identitásokhoz:

* A fájl tulajdonos felhasználója
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
| 7            | RWX        | Olvasás + Írás + Végrehajtás |
| 5            | R-X        | Olvasás + Végrehajtás         |
| 4            | R--        | Olvasás                   |
| 0            | ---        | Nincs engedély         |


### <a name="permissions-do-not-inherit"></a>Az engedélyek nem öröklődnek

A Data Lake Storage Gen1 által használt POSIX-stílusú modellben az elemhez tartozó engedélyek magában az elemben tárolódnak. Vagyis egy elem az engedélyeit nem örökölheti a szülőelemektől.

## <a name="common-scenarios-related-to-permissions"></a>Az engedélyekhez kapcsolódó gyakori helyzetek

Az alábbiakban néhány gyakori helyzet segítenek megérteni, milyen engedélyek szükségesek a Data Lake Storage Gen1-fiók bizonyos műveletek végrehajtásához.

### <a name="permissions-needed-to-read-a-file"></a>Fájl olvasásához szükséges engedélyek

![Data Lake Storage Gen1 ACL-EK](./media/data-lake-store-access-control/data-lake-store-acls-3.png)

* Az olvasni kívánt fájlhoz a hívónak **Olvasás** engedéllyel kell rendelkeznie.
* A hívónak a fájlt tartalmazó mappastruktúra összes mappájához **Végrehajtás** engedéllyel kell rendelkeznie.

### <a name="permissions-needed-to-append-to-a-file"></a>Fájlhoz való hozzáfűzéshez szükséges engedélyek

![Data Lake Storage Gen1 ACL-EK](./media/data-lake-store-access-control/data-lake-store-acls-4.png)

* A hozzáfűzni kívánt fájlhoz a hívónak **Írás** engedéllyel kell rendelkeznie.
* A hívónak a fájlt tartalmazó összes mappához **Végrehajtás** engedéllyel kell rendelkeznie.

### <a name="permissions-needed-to-delete-a-file"></a>Fájl törléséhez szükséges engedélyek

![Data Lake Storage Gen1 ACL-EK](./media/data-lake-store-access-control/data-lake-store-acls-5.png)

* A hívónak a szülőmappához **Olvasás + Végrehajtás** engedéllyel kell rendelkeznie.
* A hívónak a fájl elérési útján található összes mappához **Végrehajtás** engedéllyel kell rendelkeznie.



> [!NOTE]
> A fájlra vonatkozó írási engedélyek nem szükségesek a fájl törléséhez, amíg az előbbi két feltétel teljesül.
>
>

### <a name="permissions-needed-to-enumerate-a-folder"></a>Mappa felsorolásához szükséges engedélyek

![Data Lake Storage Gen1 ACL-EK](./media/data-lake-store-access-control/data-lake-store-acls-6.png)

* A hívónak a felsorolandó mappához **Olvasás + Végrehajtás** engedéllyel kell rendelkeznie.
* A hívónak az összes elődmappához **Végrehajtás** engedéllyel kell rendelkeznie.



## <a name="the-super-user"></a>A felügyelő

A felügyelő rendelkezik a legtöbb joggal a Data Lake Store felhasználói közül. A felügyelő:

* RWX-engedéllyel (olvasás, írás és végrehajtás) rendelkezik az **összes** fájlhoz és mappához.
* Bármely fájl vagy mappa engedélyeit megváltoztathatja.
* Bármely fájl vagy mappa tulajdonosát vagy tulajdonoscsoportját megváltoztathatja.

Az Azure-ban egy Data Lake Storage Gen1 fiók több Azure-szerepkörök, beleértve a rendelkezik:

* Tulajdonosok
* Közreműködők
* Olvasók

Mindenki a **tulajdonosok** szerepkör egy Data Lake Storage Gen1 fiók automatikusan, a fiók felügyelője. További tudnivalókért lásd a [szerepköralapú hozzáférés-vezérlést](../role-based-access-control/role-assignments-portal.md) bemutató szakaszt.
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

A POSIX ACL-ekben minden felhasználó társítva van egy „elsődleges csoporttal”. Például az „alice” nevű felhasználó a „finance” csoportba tartozhat. Alice több csoporthoz is tartozhat, de egy csoport mindig ki van jelölve elsődleges csoportjaként. A POSIX-ben ha Alice létrehoz egy fájlt, a fájl tulajdonoscsoportja Alice elsődleges csoportja lesz, ami ebben az esetben a „finance”.

Egy új fájlrendszerbeli elem létrehozásakor a Data Lake Storage Gen1 értéket rendel hozzá a tulajdonoscsoporthoz.

* **1. eset**: A gyökérmappa „/”. Ez a mappa a Data Lake Storage Gen1 fiók létrehozásakor jön létre. Ebben az esetben a tulajdonoscsoport azon felhasználó szerint lesz beállítva, aki létrehozta a fiókot.
* **2. eset** (minden egyéb eset): Egy új elem létrehozásakor a tulajdonoscsoport a szülőmappából másolódik át.

A tulajdonos csoport egyéb esetben egyéb felhasználókhoz/csoportokhoz hozzárendelt engedélyekhez hasonlóan viselkedik.

A tulajdonoscsoportot megváltoztathatja:
* Bármely felügyelő.
* a tulajdonos, ha szintén tagja ennek a csoportnak.

> [!NOTE]
> A tulajdonoscsoport *nem* változtathatja meg egy fájl vagy mappa ACL-eit.  Bár a gyökérmappa esetében a tulajdonoscsoport azon felhasználó szerint lesz beállítva, aki létrehozta a fiókot (**1. eset**, fent), egyetlen felhasználói fiók nem jogosult engedélyeket kiadni a tulajdonoscsoporton keresztül.  Az engedélyt hozzárendelheti egy érvényes felhasználócsoporthoz, ha van ilyen.

## <a name="access-check-algorithm"></a>Hozzáférés-ellenőrzési algoritmus

A következő psuedocode Data Lake Storage Gen1 fiókok esetében a hozzáférés-ellenőrzési algoritmus jelöli.

```
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or folder
  # Note: the "sticky bit" is not illustrated in this algorithm
  
# Handle super users
    if (is_superuser(user)) :
      return True

  # Handle the owning user. Note that mask is not used.
    if (is_owning_user(path, user))
      perms = get_perms_for_owning_user(path)
      return ( (desired_perms & perms) == desired_perms )

  # Handle the named user. Note that mask is used.
  if (user in get_named_users( path )) :
      perms = get_perms_for_named_user(path, user)
      mask = get_mask( path )
      return ( (desired_perms & perms & mask ) == desired_perms)

  # Handle groups (named groups and owning group)
  belongs_to_groups = [g for g in get_groups(path) if is_member_of(user, g) ]
  if (len(belongs_to_groups)>0) :
    group_perms = [get_perms_for_group(path,g) for g in belongs_to_groups]
    perms = 0
    for p in group_perms : perms = perms | p # bitwise OR all the perms together
    mask = get_mask( path )
    return ( (desired_perms & perms & mask ) == desired_perms)

  # Handle other
  perms = get_perms_for_other(path)
  mask = get_mask( path )
  return ( (desired_perms & perms & mask ) == desired_perms)
```

## <a name="the-mask"></a>A maszk

A maszk, ahogyan a hozzáférés-ellenőrzési algoritmus, korlátozza a hozzáférést **nevesített felhasználókhoz**, a **tulajdonoscsoport**, és **nevesített csoportokhoz**.  

> [!NOTE]
> Az új Data Lake Storage Gen1-fiókoknál a gyökérmappa ("/") hozzáférési ACL-JÉNEK maszk alapértelmezés szerint RWX-re.
>
>

### <a name="the-sticky-bit"></a>Ragadós bit

A ragadós (sticky) bit a POSIX-fájlrendszer egy speciális funkciója. Data Lake Storage Gen1 összefüggésben nem valószínű, hogy a ragadós bit lesz szükség.

Az alábbi táblázat a ragadós bit működését mutatja a Data Lake Storage Gen1.

| Felhasználói csoport         | Fájl    | Mappa |
|--------------------|---------|-------------------------|
| Ragadós bit **KI** | Nincs hatás   | Nincs hatás.           |
| Ragadós bit **BE**  | Nincs hatás   | A gyermekelem **felügyelőjét** és **tulajdonosát** kivéve mindenkit meggátol a gyermekelem törlésében vagy átnevezésében.               |

A ragadós bit nem látható az Azure Portalon.

## <a name="permissions-on-new-files-and-folders"></a>Új fájlok és mappák engedélyei

Az új fájlok vagy mappák meglévő mappában történő létrehozásakor a szülőmappára vonatkozó alapértelmezett ACL a következőket határozza meg:

- Gyermekmappa alapértelmezett ACL-jei és hozzáférési ACL-jei.
- Gyermekfájl hozzáférési ACL-je (a fájloknak nincs alapértelmezett ACL-je).

### <a name="the-access-acl-of-a-child-file-or-folder"></a>Gyermekfájl vagy mappa hozzáférési ACL-je

Gyermekfájl vagy -mappa létrehozásakor a szülő alapértelmezett ACL-jét a rendszer a gyermekfájl vagy -mappa alapértelmezett hozzáférési ACL-jeként másolja. Emellett ha egy **másik** felhasználó RWX-engedélyekkel rendelkezik a szülő alapértelmezett ACL-jében, akkor a rendszer eltávolítja azt a gyermekelem hozzáférési ACL-jéből.

![Data Lake Storage Gen1 ACL-EK](./media/data-lake-store-access-control/data-lake-store-acls-child-items-1.png)

A legtöbb helyzetben a gyermekelemek hozzáférési ACL-jének meghatározásával kapcsolatos itt szereplő információ elegendő. Ha ismeri a POSIX-rendszert és szeretné jobban megérteni az átalakítás menetét, tekintse meg [Az umask szerepe hozzáférési az új fájlok és mappák ACL-jének létrehozásakor](#umasks-role-in-creating-the-access-acl-for-new-files-and-folders) részt a cikk későbbi részében.


### <a name="a-child-folders-default-acl"></a>Gyermekmappa alapértelmezett ACL-je

Amikor egy szülőmappán belül gyermekmappát hoz létre, a szülőmappa alapértelmezett ACL-jét a rendszer változtatás nélkül átmásolja a gyermekmappa alapértelmezett ACL-jébe.

![Data Lake Storage Gen1 ACL-EK](./media/data-lake-store-access-control/data-lake-store-acls-child-items-2.png)

## <a name="advanced-topics-for-understanding-acls-in-data-lake-storage-gen1"></a>Haladó témakörök a Data Lake Storage Gen1 az ACL-ek megismeréséhez

Az alábbiakban néhány speciális témakört annak megértéséhez, hogyan határozzák meg a Data Lake Storage Gen1 fájlok és mappák ACL-ek.

### <a name="umask"></a>umask

Egy fájl vagy mappa létrehozásakor umask segítségével módosíthatja az alapértelmezett ACL-ek az alárendelt elem beállításának módját. umask egy 9 bites egy 9 bites érték, amely tartalmaz egy RWX-érték a fölérendelt mappák **tulajdonos**, **tulajdonoscsoport**, és **más**.

Az Azure Data Lake Storage Gen1 állandó érték, amely az umask 007 beállítása. Ezt az értéket a rendszer lefordítja arra

* umask.owning_user = 0 #---
* umask.owning_group = 0 #---
* umask.Other = 7 # RWX

Az umask érték hatékonyan azt jelenti, hogy az érték más soha nem továbbított alapértelmezés szerint az új gyermek - függetlenül az alapértelmezett ACL-t jelzi. 

A következő psuedocode bemutatja, hogyan kell alkalmazni az umask a hozzáférés-vezérlési egy gyermek-konfigurációelem létrehozása során.

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

Az alábbiakban merülnek fel a Data Lake Storage Gen1 ACL-lel kapcsolatos kérdésekre kaphat választ.

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
