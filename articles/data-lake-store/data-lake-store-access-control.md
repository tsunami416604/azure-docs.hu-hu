---
title: A Data Lake Store szolgáltatásban található hozzáférés-vezérlés áttekintése | Microsoft Docs
description: A hozzáférés-vezérlés működésének megismerése az Azure Data Lake Store szolgáltatásban
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: d16f8c09-c954-40d3-afab-c86ffa8c353d
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: a2e29fd6f2dbd4bd573b780a14bd09c0cd03395f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="access-control-in-azure-data-lake-store"></a>Az Azure Data Lake Store szolgáltatásban található hozzáférés-vezérlés

Az Azure Data Lake Store egy HDFS-ből, valamint a POSIX hozzáférés-vezérlési modellből származó hozzáférés-vezérlési modellt biztosít. Ez a cikk a Data Lake Store hozzáférés-vezérlési modelljének alapjait foglalja össze. A HDFS hozzáférés-vezérlési modelljéről további információt a [HDFS Permissions Guide](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html) (HDFS-engedélyek útmutatója) oldalon talál.

## <a name="access-control-lists-on-files-and-folders"></a>Fájlokra és mappákra vonatkozó hozzáférés-vezérlési listák

Kétféle hozzáférés-vezérlési lista (ACL) létezik – a **hozzáférési ACL** és az **alapértelmezett ACL**.

* **Hozzáférési ACL-ek**: Egy objektum hozzáféréseit vezérlik. A fájlok és mappák egyaránt rendelkeznek hozzáférési ACL-lel.

* **Alapértelmezett ACL**: A mappához társított ACL „sablon”, amely meghatározza az adott mappában létrehozott gyermekelemek hozzáférési ACL-jeit. A fájlok nem rendelkeznek alapértelmezett ACL-ekkel.

![Data Lake Store ACL-ek](./media/data-lake-store-access-control/data-lake-store-acls-1.png)

A hozzáférési ACL-ek és alapértelmezett ACL-ek ugyanazzal a struktúrával rendelkeznek.

![Data Lake Store ACL-ek](./media/data-lake-store-access-control/data-lake-store-acls-2.png)



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

A felhasználók és csoportok identitása Azure Active Directory- (Azure AD-) indentitás. Így ha nincs másképp jelölve, a „felhasználó” – a Data Lake Store kontextusában – egy Azure AD-felhasználót vagy Azure AD biztonsági csoportot is jelenthet.

## <a name="permissions"></a>Engedélyek

A fájlrendszer objektumaira vonatkozó engedélyek a következők: **Olvasás**, **Írás**, és **Végrehajtás**. Ezek az engedélyek használhatók fájlok és mappák esetében a következő táblázatban látható módon:

|            |    Fájl     |   Mappa |
|------------|-------------|----------|
| **Olvasás (R)** | Olvashatja a fájl tartalmát | **Olvasás** és **Végrehajtás** szükséges a mappa tartalmának listázásához|
| **Írás (W)** | Írhatja a fájlt vagy hozzáfűzhet a fájlhoz | **Írás** és **Végrehajtás** szükséges gyermekelemek létrehozásához a mappában |
| **Végrehajtás (X)** | Nem jelent semmit a Data Lake Store környezetében | Szükséges egy mappa gyermekelemeinek bejárásához |

### <a name="short-forms-for-permissions"></a>Az engedélyek rövid alakjai

**RWX** az **Olvasás + Írás + Végrehajtás** engedélyt jelöli. Egy tömörebb, numerikus formátum is létezik, amelyben **Olvasás=4**, **Írás=2** és **Végrehajtás=1**, és az összegük jelöli az engedélyeket. Az alábbiakban néhány példa látható.

| Numerikus alak | Rövid alak |      Jelentés     |
|--------------|------------|------------------------|
| 7            | RWX        | Olvasás + Írás + Végrehajtás |
| 5            | R-X        | Olvasás + Végrehajtás         |
| 4            | R--        | Olvasás                   |
| 0            | ---        | Nincs engedély         |


### <a name="permissions-do-not-inherit"></a>Az engedélyek nem öröklődnek

A Data Lake Store által használt POSIX-stílusú modellben az elemhez tartozó engedélyek magában az elemben tárolódnak. Vagyis egy elem az engedélyeit nem örökölheti a szülőelemektől.

## <a name="common-scenarios-related-to-permissions"></a>Az engedélyekhez kapcsolódó gyakori helyzetek

Az alábbiakban néhány gyakori alkalmazási helyzet szerepel, amelyekkel megismerhető, hogy bizonyos műveletek elvégzéséhez milyen engedélyek szükségesek a Data Lake Store-fiókban.

### <a name="permissions-needed-to-read-a-file"></a>Fájl olvasásához szükséges engedélyek

![Data Lake Store ACL-ek](./media/data-lake-store-access-control/data-lake-store-acls-3.png)

* Az olvasni kívánt fájlhoz a hívónak **Olvasás** engedéllyel kell rendelkeznie.
* A hívónak a fájlt tartalmazó mappastruktúra összes mappájához **Végrehajtás** engedéllyel kell rendelkeznie.

### <a name="permissions-needed-to-append-to-a-file"></a>Fájlhoz való hozzáfűzéshez szükséges engedélyek

![Data Lake Store ACL-ek](./media/data-lake-store-access-control/data-lake-store-acls-4.png)

* A hozzáfűzni kívánt fájlhoz a hívónak **Írás** engedéllyel kell rendelkeznie.
* A hívónak a fájlt tartalmazó összes mappához **Végrehajtás** engedéllyel kell rendelkeznie.

### <a name="permissions-needed-to-delete-a-file"></a>Fájl törléséhez szükséges engedélyek

![Data Lake Store ACL-ek](./media/data-lake-store-access-control/data-lake-store-acls-5.png)

* A hívónak a szülőmappához **Olvasás + Végrehajtás** engedéllyel kell rendelkeznie.
* A hívónak a fájl elérési útján található összes mappához **Végrehajtás** engedéllyel kell rendelkeznie.



> [!NOTE]
> A fájlra vonatkozó írási engedélyek nem szükségesek a fájl törléséhez, amíg az előbbi két feltétel teljesül.
>
>

### <a name="permissions-needed-to-enumerate-a-folder"></a>Mappa felsorolásához szükséges engedélyek

![Data Lake Store ACL-ek](./media/data-lake-store-access-control/data-lake-store-acls-6.png)

* A hívónak a felsorolandó mappához **Olvasás + Végrehajtás** engedéllyel kell rendelkeznie.
* A hívónak az összes elődmappához **Végrehajtás** engedéllyel kell rendelkeznie.

## <a name="viewing-permissions-in-the-azure-portal"></a>Engedélyek megtekintése az Azure Portalon

A Data Lake Store-fiók **Adatkezelő** panelén kattintson a **Hozzáférés** elemre az Adatkezelőben megtekintett fájlhoz vagy mappához tartozó ACL-ek megtekintéséhez. Kattintson a **Hozzáférés** lehetőségre a **mydatastore** fiókban lévő **catalog** mappához tartozó ACL-ek megtekintéséhez.

![Data Lake Store ACL-ek](./media/data-lake-store-access-control/data-lake-store-show-acls-1.png)

E panel felső részén a tulajdonos engedélyei láthatók. (A képernyőképen a tulajdonos felhasználó Bob.) Alatta a hozzárendelt hozzáférési ACL-ek láthatók. 

![Data Lake Store ACL-ek](./media/data-lake-store-access-control/data-lake-store-show-acls-simple-view.png)

Kattintson a **Speciális nézet** elemre egy részletesebb nézet megtekintéséhez, ahol az alapértelmezett ACL-ek, a maszk és a felügyelők leírása látható.  A panel ezen kívül lehetőséget biztosít a gyermekfájlokhoz és mappákhoz tartozó hozzáférési és alapértelmezett ACL-ek rekurzív módon történő beállításához az aktuális mappa engedélyei alapján.

![Data Lake Store ACL-ek](./media/data-lake-store-access-control/data-lake-store-show-acls-advance-view.png)

## <a name="the-super-user"></a>A felügyelő

A felügyelő rendelkezik a legtöbb joggal a Data Lake Store felhasználói közül. A felügyelő:

* RWX-engedéllyel (olvasás, írás és végrehajtás) rendelkezik az **összes** fájlhoz és mappához.
* Bármely fájl vagy mappa engedélyeit megváltoztathatja.
* Bármely fájl vagy mappa tulajdonosát vagy tulajdonoscsoportját megváltoztathatja.

A Data Lake Store-fiók számos szerepkörrel rendelkezik az Azure-ban, többek között:

* Tulajdonosok
* Közreműködők
* Olvasók

Egy Data Lake Store-fiók **Tulajdonosok** szerepkörében mindenki automatikusan a fiók felügyelője lesz. További tudnivalókért lásd a [szerepköralapú hozzáférés-vezérlést](../active-directory/role-based-access-control-configure.md) bemutató szakaszt.
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

Egy új fájlrendszerbeli elem létrehozásakor a Data Lake Store értéket rendel hozzá a tulajdonoscsoporthoz.

* **1. eset**: A gyökérmappa „/”. A mappa a Data Lake Store-fiók létrehozásakor jön létre. Ebben az esetben a tulajdonoscsoport azon felhasználó szerint lesz beállítva, aki létrehozta a fiókot.
* **2. eset** (minden egyéb eset): Egy új elem létrehozásakor a tulajdonoscsoport a szülőmappából másolódik át.

A tulajdonos csoport egyéb esetben egyéb felhasználókhoz/csoportokhoz hozzárendelt engedélyekhez hasonlóan viselkedik.

A tulajdonoscsoportot megváltoztathatja:
* Bármely felügyelő.
* a tulajdonos, ha szintén tagja ennek a csoportnak.

> [!NOTE]
> A tulajdonoscsoport *nem* változtathatja meg egy fájl vagy mappa ACL-eit.

## <a name="access-check-algorithm"></a>Hozzáférés-ellenőrzési algoritmus

Az alábbi ábra a Data Lake Store-fiókhoz tartozó hozzáférés-ellenőrzési algoritmust mutatja be.

![Data Lake Store ACL-ek algoritmusa](./media/data-lake-store-access-control/data-lake-store-acls-algorithm.png)


## <a name="the-mask-and-effective-permissions"></a>A maszk és „hatályos engedélyek”

A **maszk** egy olyan RWX-érték, amely a **nevesített felhasználókhoz**, a **tulajdonoscsoporthoz** és a **nevesített csoportokhoz** történő hozzáférést korlátozza a hozzáférés-ellenőrzési algoritmus futtatása közben. Itt ismertetjük a maszk legfontosabb fogalmait.

* A maszk „hatályos engedélyeket” hoz létre. Ez azt jelenti, hogy módosítja az engedélyeket a hozzáférés-ellenőrzés során.
* A maszkot közvetlenül szerkesztheti a fájl tulajdonosa vagy bármely felügyelő.
* A maszk eltávolíthat engedélyeket a hatályos engedélyek létrehozása érdekében. A maszk *nem* adhat hozzá engedélyeket a hatályos engedélyekhez.

Lássunk néhány példát. Az alábbi példában a maszk **RWX** értékre van állítva, ami azt jelenti, hogy a maszk nem távolít el engedélyeket. A nevesített felhasználóhoz, tulajdonoscsoporthoz és nevesített csoporthoz tartozó hatályos engedélyek nem változtak meg a hozzáférés-ellenőrzés során.

![Data Lake Store ACL-ek](./media/data-lake-store-access-control/data-lake-store-acls-mask-1.png)

Az alábbi példában a maszk a következőre van állítva: **R-X**. Ez azt jelenti, hogy a maszk **kikapcsolja az Írás engedélyt** a **nevesített felhasználó**, a **tulajdonoscsoport** és a **nevesített csoport** számára a hozzáférés-ellenőrzés idejére.

![Data Lake Store ACL-ek](./media/data-lake-store-access-control/data-lake-store-acls-mask-2.png)

Hivatkozásként itt megtalálja, hogy hol jelenik meg egy fájlhoz vagy mappához tartozó maszk az Azure Portalon.

![Data Lake Store ACL-ek](./media/data-lake-store-access-control/data-lake-store-show-acls-mask-view.png)

> [!NOTE]
> Az új Data Lake Store-fiókoknál a gyökérmappa („/”) hozzáférési ACL-jéhez tartozó maszk alapértelmezés szerint RWX-re van állítva.
>
>

## <a name="permissions-on-new-files-and-folders"></a>Új fájlok és mappák engedélyei

Az új fájlok vagy mappák meglévő mappában történő létrehozásakor a szülőmappára vonatkozó alapértelmezett ACL a következőket határozza meg:

- Gyermekmappa alapértelmezett ACL-jei és hozzáférési ACL-jei.
- Gyermekfájl hozzáférési ACL-je (a fájloknak nincs alapértelmezett ACL-je).

### <a name="the-access-acl-of-a-child-file-or-folder"></a>Gyermekfájl vagy mappa hozzáférési ACL-je

Gyermekfájl vagy -mappa létrehozásakor a szülő alapértelmezett ACL-jét a rendszer a gyermekfájl vagy -mappa alapértelmezett hozzáférési ACL-jeként másolja. Emellett ha egy **másik** felhasználó RWX-engedélyekkel rendelkezik a szülő alapértelmezett ACL-jében, akkor a rendszer eltávolítja azt a gyermekelem hozzáférési ACL-jéből.

![Data Lake Store ACL-ek](./media/data-lake-store-access-control/data-lake-store-acls-child-items-1.png)

A legtöbb helyzetben a gyermekelemek hozzáférési ACL-jének meghatározásával kapcsolatos itt szereplő információ elegendő. Ha ismeri a POSIX-rendszert és szeretné jobban megérteni az átalakítás menetét, tekintse meg [Az umask szerepe hozzáférési az új fájlok és mappák ACL-jének létrehozásakor](#umasks-role-in-creating-the-access-acl-for-new-files-and-folders) részt a cikk későbbi részében.


### <a name="a-child-folders-default-acl"></a>Gyermekmappa alapértelmezett ACL-je

Amikor egy szülőmappán belül gyermekmappát hoz létre, a szülőmappa alapértelmezett ACL-jét a rendszer változtatás nélkül átmásolja a gyermekmappa alapértelmezett ACL-jébe.

![Data Lake Store ACL-ek](./media/data-lake-store-access-control/data-lake-store-acls-child-items-2.png)

## <a name="advanced-topics-for-understanding-acls-in-data-lake-store"></a>Haladó témakörök a Data Lake Store-ban található ACL-ek megismeréséhez

A következőben néhány speciális témakör található, amely segíti a Data Lake Store fájljaihoz és mappáihoz tartozó ACL-ek meghatározásának megismerését.

### <a name="umasks-role-in-creating-the-access-acl-for-new-files-and-folders"></a>Az umask szerepe hozzáférési az új fájlok és mappák ACL-jének létrehozásakor

A POSIX-kompatibilis rendszerekben az általános elképzelés az, hogy az umask egy 9 bites érték a szülőmappára vonatkozóan, amely a **tulajdonos**, a **tulajdonoscsoport** és az **egyéb** felhasználók engedélyeinek átalakítására használatos az új gyermekfájlok vagy mappák hozzáférési ACL-jén. Az umask bitjei határozzák meg, hogy mely bitek legyenek kikapcsolva a gyermekelem hozzáférési ACL-jében. Ennek megfelelően arra használható, hogy a **tulajdonos**, a **tulajdonoscsoport** és az **egyéb** felhasználók engedélyének terjesztését külön-külön akadályozza meg.

A HDFS-rendszerekben, az umask általában a teljes helyre vonatkozó konfigurációs beállítás, amelyet a rendszergazdák kezelnek. A Data Lake Store **egész fiókra kiterjedő umaskot** használ, amelyet nem lehet megváltoztatni. Az alábbi táblázatban a Data Lake Store umask beállítása szerepel.

| Felhasználói csoport  | Beállítás | Hatás az új gyermekelemek hozzáférési ACL-jére |
|------------ |---------|---------------------------------------|
| Tulajdonos felhasználó | ---     | Nincs hatás                             |
| Tulajdonoscsoport| ---     | Nincs hatás                             |
| Egyéb       | RWX     | Olvasás + Írás + Végrehajtás eltávolítása         |

Az alábbi ábrán az umask működése látható. Az eredő hatás az **Olvasás + Írás + Végrehajtás** eltávolítása az **egyéb** felhasználóról. Mivel az umask nem határoz meg biteket a **tulajdonos felhasználóhoz** és a **tulajdonoscsoporthoz**, a rendszer ezeket az engedélyeket nem alakítja át.

![Data Lake Store ACL-ek](./media/data-lake-store-access-control/data-lake-store-acls-umask.png)

### <a name="the-sticky-bit"></a>Ragadós bit

A ragadós (sticky) bit a POSIX-fájlrendszer egy speciális funkciója. A Data Lake Store-ral összefüggésben nem valószínű, hogy szükség lesz ragadós bitre.

A következő táblázat a ragadós bitek működését mutatja a Data Lake Store-ban.

| Felhasználói csoport         | Fájl    | Mappa |
|--------------------|---------|-------------------------|
| Ragadós bit **KI** | Nincs hatás   | Nincs hatás.           |
| Ragadós bit **BE**  | Nincs hatás   | A gyermekelem **felügyelőjét** és **tulajdonosát** kivéve mindenkit meggátol a gyermekelem törlésében vagy átnevezésében.               |

A ragadós bit nem látható az Azure Portalon.

## <a name="common-questions-about-acls-in-data-lake-store"></a>A Data Lake Store-ban található ACL-lel kapcsolatos gyakori kérdések

Az alábbiakban néhány gyakori kérdést talál, amelyek a Data Lake Store-ban található ACL-ekkel kapcsolatban merülnek fel.

### <a name="do-i-have-to-enable-support-for-acls"></a>Engedélyeznem kell az ACL támogatását?

Nem. Az ACL-alapú hozzáférés-vezérlés mindig be van kapcsolva egy Data Lake Store-fióknál.

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

### <a name="does-data-lake-store-support-inheritance-of-acls"></a>Támogatja a Data Lake Store az ACL-ek öröklését?

Nem, az alapértelmezett ACL-ek azonban használhatók a szülő mappán belül újonnan létrehozott gyermekfájlok és mappák ACL-jeinek beállításához.  

### <a name="what-is-the-difference-between-mask-and-umask"></a>Mi a különbség a mask és az umask között?

| mask | umask|
|------|------|
| A **mask** tulajdonság minden fájl és mappa esetében elérhető. | Az **umask** a Data Lake Store-fiók egyik tulajdonsága. Tehát a Data Lake Store csak egyetlen umask tulajdonságot tartalmaz.    |
| Egy fájl vagy mappa mask tulajdonságát a fájl tulajdonosa vagy tulajdonoscsoportja, illetve egy felügyelő változtathatja meg. | Az umask tulajdonságot semmilyen felhasználó nem módosíthatja, még a felügyelők sem. Ez egy megváltoztathatatlan, állandó érték.|
| A mask tulajdonság a hozzáférés-ellenőrzési algoritmus futásakor használható annak megállapítására, hogy egy felhasználó jogosult-e műveletek elvégzésére egy fájlon vagy mappán. A mask szerepe a „hatályos engedélyek létrehozása” a hozzáférés-ellenőrzés során. | Az umask a hozzáférés-ellenőrzés során egyáltalán nincs használatban. Az umask egy mappa új gyermekelemei hozzáférési ACL-jeinek meghatározására használható. |
| A mask egy 3 bites RWX-érték, amely a nevesített felhasználóra, a tulajdonoscsoportra és a nevesített csoportra lesz alkalmazva a hozzáférés-ellenőrzés időpontjában.| Az umask egy 9 bites érték, amely egy új gyermek tulajdonosára, tulajdonoscsoportjára vagy **egyéb** jellemzőjére lesz alkalmazva.|

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Hol tudhatok meg többet a POSIX hozzáférés-vezérlési modellről?

* [POSIX hozzáférés-vezérlési listák Linux rendszeren](http://www.vanemery.com/Linux/ACL/POSIX_ACL_on_Linux.html)

* [HDFS-engedélyek útmutatója](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)

* [POSIX – gyakori kérdések](http://www.opengroup.org/austin/papers/posix_faq.html)

* [POSIX 1003.1 2008](http://standards.ieee.org/findstds/standard/1003.1-2008.html)

* [POSIX 1003.1 2013](http://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)

* [POSIX 1003.1 2016](http://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)

* [POSIX ACL Ubuntu rendszeren](https://help.ubuntu.com/community/FilePermissionsACLs)

* [Hozzáférés-vezérlési listákat használó ACL Linux rendszeren](http://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Lásd még

* [Az Azure Data Lake Store áttekintése](data-lake-store-overview.md)
