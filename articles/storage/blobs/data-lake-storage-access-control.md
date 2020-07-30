---
title: A Azure Data Lake Storage Gen2 hozzáférés-vezérlésének áttekintése | Microsoft Docs
description: Ismerje meg, hogyan működik a hozzáférés-vezérlés Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 4b52fe22e455f5b0ebce6960b40bcc80c46079c3
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87421348"
---
# <a name="access-control-in-azure-data-lake-storage-gen2"></a>Hozzáférés-vezérlés a 2. generációs Azure Data Lake Storage-ben

Azure Data Lake Storage Gen2 olyan hozzáférés-vezérlési modellt valósít meg, amely támogatja az Azure szerepköralapú hozzáférés-vezérlést (RBAC) és a POSIX-hez hasonló hozzáférés-vezérlési listákat (ACL-eket). Ez a cikk a Data Lake Storage Gen2 hozzáférés-vezérlési modelljének alapjait összegzi.

<a id="azure-role-based-access-control-rbac"></a>

## <a name="role-based-access-control"></a>Szerepkör alapú hozzáférés-vezérlés

A RBAC szerepkör-hozzárendelésekkel hatékonyan alkalmazza a *rendszerbiztonsági tag*engedélyeinek készleteit. A *rendszerbiztonsági tag* egy olyan objektum, amely az Azure-erőforrásokhoz való hozzáférést kérő Azure Active Directory (ad) által meghatározott felhasználó, csoport, szolgáltatásnév vagy felügyelt identitást jelöl.

Ezek az Azure-erőforrások jellemzően legfelső szintű erőforrásokra vannak korlátozva (például: Azure Storage-fiókok). Az Azure Storage esetében, és ennek következtében Azure Data Lake Storage Gen2 ezt a mechanizmust a tároló (fájlrendszer) erőforrásra kiterjesztették.

Ha szeretné megtudni, hogyan rendeljen hozzá szerepköröket rendszerbiztonsági tag számára a Storage-fiók hatókörében, tekintse meg [Az Azure Blob-és üzenetsor-kezelés hozzáférésének biztosítása a Azure Portal RBAC](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)című témakört.

> [!NOTE]
> A vendég felhasználó nem hozhat létre szerepkör-hozzárendelést.

### <a name="the-impact-of-role-assignments-on-file-and-directory-level-access-control-lists"></a>A szerepkör-hozzárendelések hatása a fájl-és könyvtár szintű hozzáférés-vezérlési listára

Az Azure szerepkör-hozzárendelések használata egy hatékony mechanizmus a hozzáférési engedélyek szabályozására, és ez egy nagyon durva módszer a hozzáférés-vezérlési listákhoz képest. A RBAC legkisebb részletessége a tároló szintjén van, ezért a rendszer az ACL-ekkel magasabb prioritással értékeli. Ezért ha egy szerepkört egy rendszerbiztonsági tag számára rendel hozzá egy tároló hatókörében, a rendszerbiztonsági tag az adott szerepkörhöz tartozó összes könyvtárra és fájlra vonatkozó engedélyezési szinttel rendelkezik, függetlenül az ACL-hozzárendeléstől.

Ha egy rendszerbiztonsági tag egy [beépített szerepkörön](https://docs.microsoft.com/azure/storage/common/storage-auth-aad?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#built-in-rbac-roles-for-blobs-and-queues)vagy egy egyéni szerepkörön keresztül kap RBAC-adatengedélyeket, akkor ezeket az engedélyeket először a kérelem engedélyezése után értékeli ki a rendszer. Ha a kért műveletet a rendszerbiztonsági tag Azure szerepkör-hozzárendelései engedélyezik, akkor az engedélyezés azonnal megoldódik, és a rendszer nem végez további ACL-ellenőrzéseket. Ha a rendszerbiztonsági tag nem rendelkezik Azure-szerepkör-hozzárendeléssel, vagy a kérelem művelete nem felel meg a hozzárendelt engedélynek, akkor az ACL-ellenőrzések végrehajtásával megállapíthatja, hogy a rendszerbiztonsági tag jogosult-e a kért művelet végrehajtására.

> [!NOTE]
> Ha a rendszerbiztonsági tag a Storage blob-adatok tulajdonosának beépített szerepkör-hozzárendelését rendelte hozzá, akkor a rendszerbiztonsági tag egy *felügyelőnek* tekintendő, és teljes hozzáférést kap az összes mutációs művelethez, beleértve a címtár vagy fájl tulajdonosának beállítását, valamint a könyvtárak és fájlok hozzáférés-vezérlési listáihoz tartozó ACL-eket, amelyekhez nem a tulajdonos. A felügyelői hozzáférés csak az erőforrás tulajdonosának módosítására jogosult.

## <a name="shared-key-and-shared-access-signature-sas-authentication"></a>Megosztott kulcs és közös hozzáférésű aláírás (SAS) hitelesítése

Azure Data Lake Storage Gen2 támogatja a megosztott kulcs-és SAS-metódusokat a hitelesítéshez. Ezen hitelesítési módszerek egyik jellemzője, hogy a hívóhoz nem tartozik identitás, ezért a rendszerbiztonsági tag engedély-alapú engedélyezése nem hajtható végre.

Megosztott kulcs esetén a hívó gyakorlatilag "felügyelői" hozzáférést kap, ami teljes hozzáférést biztosít az összes erőforrás összes műveletéhez, beleértve a tulajdonos beállítását és a hozzáférés-vezérlési listák módosítását.

Az SAS-tokenek a jogkivonat részeként tartalmazzák az engedélyezett engedélyeket. Az SAS-jogkivonatban található engedélyeket a rendszer hatékonyan alkalmazza az összes hitelesítési döntésre, de nem végez további ACL-ellenőrzéseket.

## <a name="access-control-lists-on-files-and-directories"></a>Fájlok és könyvtárak hozzáférés-vezérlési listája

Fájlok és könyvtárak hozzáférési szintjén társíthatja a rendszerbiztonsági tag-t. Ezek a társítások egy *hozzáférés-vezérlési listán (ACL)* vannak rögzítve. A Storage-fiók minden fájlja és könyvtára rendelkezik egy hozzáférés-vezérlési listával.

> [!NOTE]
> Az ACL-ek csak az ugyanabban a bérlőben lévő rendszerbiztonsági tag esetében érvényesek. 

Ha a szerepkört egy rendszerbiztonsági tag számára rendelte hozzá a Storage-fiók szintjén, a hozzáférés-vezérlési listában megadhatja, hogy a rendszerbiztonsági tag emelt szintű hozzáférést adott fájlokhoz és címtárakhoz.

A hozzáférés-vezérlési listán nem használható olyan hozzáférési szint, amely alacsonyabb, mint a szerepkör-hozzárendelés által megadott szint. Ha például a [tárolási blob adatközreműködői](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) szerepkört egy rendszerbiztonsági tag számára rendeli hozzá, akkor nem használhatja a hozzáférés-vezérlési listát, hogy megakadályozza a rendszerbiztonsági tag írását egy könyvtárba.


### <a name="set-file-and-directory-level-permissions-by-using-access-control-lists"></a>Fájl-és könyvtár-szintű engedélyek beállítása hozzáférés-vezérlési lista használatával

A fájl-és könyvtári szintű engedélyek megadásához tekintse meg a következő cikkek bármelyikét:

| Környezet | Cikk |
|--------|-----------|
|Azure Storage Explorer |[ kezelése az Azure Storage Explorerrel az Azure Data Lake Storage Gen2-ben](data-lake-storage-explorer.md#managing-access)|
|.NET |[A .NET használatával kezelheti a címtárakat, a fájlokat és a hozzáférés-vezérlési listákat Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-dotnet.md)|
|Java|[A Java segítségével kezelheti a címtárakat, a fájlokat és a hozzáférés-vezérlési listákat Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-java.md)|
|Python|[A Python használatával kezelheti a címtárakat, a fájlokat és a hozzáférés-vezérlési listákat Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-python.md)|
|PowerShell|[A PowerShell használatával kezelheti a címtárakat, a fájlokat és a hozzáférés-vezérlési listákat Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-powershell.md)|
|Azure CLI|[Könyvtárak, fájlok és ACL-ek kezelése az Azure CLI használatával Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-cli.md)|
|REST API |[Elérési út – frissítés](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> Ha a rendszerbiztonsági tag egy egyszerű *szolgáltatásnév* , fontos, hogy használja az egyszerű szolgáltatásnév objektum-azonosítóját, nem pedig a kapcsolódó alkalmazás regisztrációs OBJEKTUMának azonosítóját. Az egyszerű szolgáltatásnév objektum-AZONOSÍTÓjának lekéréséhez nyissa meg az Azure CLI-t, majd használja a következő parancsot: `az ad sp show --id <Your App ID> --query objectId` . Ügyeljen arra, hogy a `<Your App ID>` helyőrzőt cserélje le az alkalmazás-regisztrációhoz tartozó alkalmazás-azonosítóra.

### <a name="types-of-access-control-lists"></a>Hozzáférés-vezérlési listák típusai

A hozzáférés-vezérlési listáknak két típusa létezik: *hozzáférés-ACL* -EK és *alapértelmezett ACL*-EK.

Hozzáférési ACL-ek szabályozzák az objektumokhoz való hozzáférést. A fájlok és könyvtárak egyaránt rendelkeznek hozzáférési ACL-ekkel.

Az alapértelmezett ACL-ek olyan címtárhoz tartozó ACL-sablonok, amelyek meghatározzák az adott címtárban létrehozott alárendelt elemek hozzáférési ACL-jeit. A fájlok nem rendelkeznek alapértelmezett ACL-ekkel.

A hozzáférési ACL-ek és az alapértelmezett ACL-ek azonos struktúrával rendelkeznek.

> [!NOTE]
> A szülő alapértelmezett ACL-értékének módosítása nincs hatással a már létező alárendelt elemek hozzáférési ACL-jéhez vagy alapértelmezett ACL-jéhez.

### <a name="levels-of-permission"></a>Engedélyek szintjei

A Container objektumokra vonatkozó engedélyek a következők: **olvasás**, **írás**és **végrehajtás**, és a fájlokon és könyvtárakon is használhatók az alábbi táblázatban látható módon:

|            |    Fájl     |   Címtár |
|------------|-------------|----------|
| **Olvasás (R)** | Olvashatja a fájl tartalmát | **Olvasás** és **végrehajtás** szükséges a könyvtár tartalmának listázásához |
| **Írás (W)** | Írhatja a fájlt vagy hozzáfűzhet a fájlhoz | **Írás** és **végrehajtás** szükséges az alárendelt elemek létrehozásához egy címtárban |
| **Végrehajtás (X)** | Nem jelent semmit a Data Lake Storage Gen2 kontextusában | Egy könyvtár alárendelt elemeinek bejárásához szükséges. |

> [!NOTE]
> Ha csak ACL-ek (nem RBAC) használatával ad meg engedélyeket, a rendszerbiztonsági tag számára olvasási vagy írási hozzáférést kell adnia egy fájlhoz, meg kell adnia a rendszerbiztonsági tag számára a tárolóhoz **tartozó engedélyeket,** valamint a fájlhoz vezető mappák hierarchiájában lévő összes mappát.

#### <a name="short-forms-for-permissions"></a>Az engedélyek rövid alakjai

**RWX** az **Olvasás + Írás + Végrehajtás** engedélyt jelöli. Egy tömörebb, numerikus formátum is létezik, amelyben **Olvasás=4**, **Írás=2** és **Végrehajtás=1**, és az összegük jelöli az engedélyeket. Az alábbiakban néhány példa látható.

| Numerikus alak | Rövid alak |      Jelentés     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Olvasás + Írás + Végrehajtás |
| 5            | `R-X`        | Olvasás + Végrehajtás         |
| 4            | `R--`        | Olvasás                   |
| 0            | `---`        | Nincs engedély         |

#### <a name="permissions-inheritance"></a>Engedélyek öröklése

Az Data Lake Storage Gen2 által használt POSIX stílusú modellben az elemhez tartozó engedélyeket maga az elem tárolja. Ez azt jelenti, hogy egy elem engedélyei nem öröklődnek a fölérendelt elemből, ha az engedélyek a gyermek elem már létrejötte után vannak beállítva. Az engedélyek csak akkor lesznek örökölve, ha a fölérendelt elemekhez az alárendelt elemek létrehozása előtt alapértelmezett engedélyek vannak beállítva.

### <a name="common-scenarios-related-to-permissions"></a>Az engedélyekhez kapcsolódó gyakori helyzetek

A következő táblázat felsorolja azokat a gyakori forgatókönyveket, amelyekkel megtudhatja, hogy mely engedélyek szükségesek bizonyos műveletek elvégzéséhez egy Storage-fiókon.

|    Művelet             |    /    | Oregon | Portland | Data.txt     |
|--------------------------|---------|----------|-----------|--------------|
| Olvasási Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Hozzáfűzés Data.txt       |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Data.txt törlése          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Data.txt létrehozása          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Listáját                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| /Oregon/listázása           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| /Oregon/Portland/listázása  |   `--X`   |   `--X`    |  `R-X`      | `---`          |

> [!NOTE]
> A fájlra vonatkozó írási engedélyek nem szükségesek a törléséhez, feltéve, hogy az előző két feltétel igaz.

### <a name="users-and-identities"></a>Felhasználók és identitások

Minden fájl és könyvtár külön engedélyekkel rendelkezik ezekhez az identitásokhoz:

- A tulajdonos felhasználó
- A tulajdonoscsoport
- Nevesített felhasználók
- Nevesített csoportok
- Névvel ellátott egyszerű szolgáltatások
- Nevesített felügyelt identitások
- Minden egyéb felhasználó

A felhasználók és csoportok identitása Azure Active Directory- (Azure AD-) indentitás. Így ha nincs másképp jelölve, a *felhasználó*Data Lake Storage Gen2 kontextusában hivatkozhat egy Azure ad-felhasználóra, egyszerű szolgáltatásnévre, felügyelt identitásra vagy biztonsági csoportra.

#### <a name="the-owning-user"></a>A tulajdonos felhasználó

Automatikusan az elem tulajdonosa lesz az a felhasználó, aki létrehozta az elemet. A tulajdonos felhasználó:

* Megváltoztathatja a tulajdonában lévő fájl engedélyeit.
* megváltoztathatja a tulajdonában lévő fájl tulajdonos csoportját, ha a tulajdonos felhasználó szintén tagja ennek a csoportnak.

> [!NOTE]
> A tulajdonos felhasználó *nem* változtathatja meg egy fájl vagy könyvtár tulajdonos felhasználóját. Csak a felügyelők módosíthatják egy fájl vagy könyvtár tulajdonos felhasználóját.

#### <a name="the-owning-group"></a>A tulajdonoscsoport

A POSIX ACL-ekben minden felhasználó egy *elsődleges csoporttal*van társítva. Például az "Alice" nevű felhasználó a "Finance" csoportba tartozhat. Alice több csoporthoz is tartozhat, de egy csoport mindig ki van jelölve elsődleges csoportjaként. A POSIX-ben ha Alice létrehoz egy fájlt, a fájl tulajdonoscsoportja Alice elsődleges csoportja lesz, ami ebben az esetben a „finance”. A tulajdonos csoport egyéb esetben egyéb felhasználókhoz/csoportokhoz hozzárendelt engedélyekhez hasonlóan viselkedik.

##### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>A tulajdonos csoport kiosztása új fájlhoz vagy könyvtárhoz

* **1. eset**: a gyökérkönyvtár "/". Ez a könyvtár akkor jön létre, amikor létrejön egy Data Lake Storage Gen2 tároló. Ebben az esetben a tulajdonos csoport arra a felhasználóra van beállítva, aki létrehozta a tárolót, ha az a OAuth használatával történt. Ha a tároló megosztott kulccsal, a fiók SAS-vel vagy egy szolgáltatás SAS-vel lett létrehozva, akkor a tulajdonos és a tulajdonos csoport **$superuserra**van állítva.
* **2. eset** (minden egyéb eset): új elem létrehozásakor a tulajdonos csoportot a rendszer a szülő könyvtárából másolja.

##### <a name="changing-the-owning-group"></a>A tulajdonos csoport módosítása

A tulajdonoscsoportot megváltoztathatja:
* Bármely felügyelő.
* a tulajdonos, ha szintén tagja ennek a csoportnak.

> [!NOTE]
> A tulajdonos csoport nem változtathatja meg egy fájl vagy könyvtár ACL-jeit.  Míg a tulajdonos csoport arra a felhasználóra van beállítva, aki a gyökérkönyvtár esetében létrehozta a fiókot, az **1. eset esetében** egyetlen felhasználói fiók nem érvényes a tulajdonos csoporton keresztüli engedélyek biztosításához. Az engedélyt hozzárendelheti egy érvényes felhasználócsoporthoz, ha van ilyen.

### <a name="access-check-algorithm"></a>Hozzáférés-ellenőrzési algoritmus

A következő pseudocode a Storage-fiókok hozzáférés-ellenőrzési algoritmusát képviselik.

```console
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or directory
  # Note: the "sticky bit" isn't illustrated in this algorithm
  
# Handle super users.
  if (is_superuser(user)) :
    return True

# Handle the owning user. Note that mask isn't used.
entry = get_acl_entry( path, OWNER )
if (user == entry.identity)
    return ( (desired_perms & entry.permissions) == desired_perms )

# Handle the named users. Note that mask IS used.
entries = get_acl_entries( path, NAMED_USER )
for entry in entries:
    if (user == entry.identity ) :
        mask = get_mask( path )
        return ( (desired_perms & entry.permissions & mask) == desired_perms)

# Handle named groups and owning group
member_count = 0
perms = 0
entries = get_acl_entries( path, NAMED_GROUP | OWNING_GROUP )
for entry in entries:
if (user_is_member_of_group(user, entry.identity)) :
    member_count += 1
    perms | =  entry.permissions
if (member_count>0) :
return ((desired_perms & perms & mask ) == desired_perms)

# Handle other
perms = get_perms_for_other(path)
mask = get_mask( path )
return ( (desired_perms & perms & mask ) == desired_perms)
```

#### <a name="the-mask"></a>A maszk

Ahogy az a hozzáférés-ellenőrzési algoritmusban is látható, a maszk korlátozza a megnevezett felhasználók, a tulajdonos csoport és a nevesített csoportok hozzáférését.  

> [!NOTE]
> Új Data Lake Storage Gen2 tároló esetén a gyökérkönyvtár ("/") hozzáférési ACL-jéhez tartozó maszk alapértelmezés szerint 750 a könyvtárakhoz és a 640 fájlokhoz. A fájlok nem kapják meg az X bitet, mert nem relevánsak a csak tárolót tartalmazó rendszer fájljaiban.
>
> A maszk hívásos alapon adható meg. Ez lehetővé teszi, hogy a különböző fogyasztási rendszerek, például a fürtök különböző hatékony maszkokkal rendelkezzenek a fájl műveleteihez. Ha egy adott kérelemhez maszk van megadva, az teljesen felülbírálja az alapértelmezett maszkot.

#### <a name="the-sticky-bit"></a>Ragadós bit

A ragadós bit egy POSIX-tároló fejlettebb funkciója. Data Lake Storage Gen2 kontextusában nem valószínű, hogy a Sticky bit szükséges. Összefoglalva, ha a ragadós bit engedélyezve van egy címtárban, a gyermek elem csak törölhető vagy átnevezhető az alárendelt elem tulajdonos felhasználója számára.

A Sticky bit nem jelenik meg a Azure Portalban.

### <a name="default-permissions-on-new-files-and-directories"></a>Új fájlok és könyvtárak alapértelmezett engedélyei

Ha új fájlt vagy könyvtárat hoz létre egy meglévő címtárban, a szülő könyvtár alapértelmezett ACL-je határozza meg a következőket:

- Egy alárendelt könyvtár alapértelmezett ACL-je és hozzáférési ACL-je.
- Egy alárendelt fájl hozzáférési ACL-je (a fájloknak nincs alapértelmezett ACL-je).

#### <a name="umask"></a>umask

Fájl vagy könyvtár létrehozásakor a rendszer a umask használatával módosítja az alapértelmezett ACL-ek beállítását az alárendelt elemen. a umask egy 9 bites érték a szülő címtárakban, amelyek RWX értéket tartalmaznak a **tulajdonos felhasználó**, a **tulajdonos csoport**és a **többi**számára.

A umask, amely a 007-re beállított állandó érték Azure Data Lake Storage Gen2. Ez az érték a következőre van lefordítva:

| umask-összetevő     | Numerikus alak | Rövid alak | Értelmezés |
|---------------------|--------------|------------|---------|
| umask. owning_user   |    0         |   `---`      | A tulajdonos felhasználó számára másolja a szülő alapértelmezett ACL-t a gyermek hozzáférési ACL-jéhez. | 
| umask. owning_group  |    0         |   `---`      | A tulajdonos csoport esetében másolja a szülő alapértelmezett ACL-t a gyermek hozzáférési ACL-jéhez. | 
| umask. other         |    7         |   `RWX`      | Egyéb esetben távolítsa el az összes engedélyt a gyermek hozzáférési ACL-jéhez |

Az Azure Data Lake Storage Gen2 által használt umask-érték azt jelenti, hogy a **többi** esetében az érték soha nem kerül be alapértelmezés szerint az új gyermekekre, függetlenül attól, hogy az alapértelmezett ACL mit jelez. 

Az alábbi pseudocode azt szemlélteti, hogyan történik a umask alkalmazása az ACL-ek egy alárendelt tételhez való létrehozásakor.

```console
def set_default_acls_for_new_child(parent, child):
    child.acls = []
    for entry in parent.acls :
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

## <a name="common-questions-about-acls-in-data-lake-storage-gen2"></a>Gyakori kérdések a Data Lake Storage Gen2 ACL-ekkel kapcsolatban

### <a name="do-i-have-to-enable-support-for-acls"></a>Engedélyeznem kell az ACL támogatását?

Nem. Az ACL-eken keresztüli hozzáférés-vezérlés engedélyezve van egy Storage-fiókhoz, ha a hierarchikus névtér (HNS) funkció be van kapcsolva.

Ha a HNS ki van kapcsolva, az Azure RBAC engedélyezési szabályai továbbra is érvényben vannak.

### <a name="what-is-the-best-way-to-apply-acls"></a>Mi a legjobb módszer az ACL-ek alkalmazására?

Mindig az Azure AD-beli biztonsági csoportokat használja a hozzárendelt rendszerbiztonsági csoportokként az ACL-ekben. Lehetősége van arra, hogy közvetlenül rendeljen hozzá egyéni felhasználókat vagy egyszerű szolgáltatásokat. Ezzel a struktúrával felhasználókat vagy egyszerű szolgáltatásokat adhat hozzá és távolíthat el anélkül, hogy újra kellene alkalmaznia az ACL-eket egy teljes címtár-struktúrára. Ehelyett egyszerűen fel kell vennie vagy el kell távolítania azokat a megfelelő Azure AD biztonsági csoportból. Ne feledje, hogy az ACL-ek nem öröklődnek, ezért az ACL-ek újraalkalmazása az ACL-eket minden fájlra és alkönyvtárra vonatkozóan frissítenie kell. 

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>Mely engedélyek szükségesek a címtár és annak tartalmának rekurzív törléséhez?

- A hívó "felügyelői" engedéllyel rendelkezik,

Vagy

- A szülő könyvtárnak írási és végrehajtási engedélyekkel kell rendelkeznie.
- A törlendő könyvtár és a benne található összes címtár olvasás + írás + végrehajtás engedélyre van szükség.

> [!NOTE]
> A címtárakban található fájlok törléséhez nincs szükség írási engedélyekre. Emellett a "/" gyökérkönyvtár nem törölhető.

### <a name="who-is-the-owner-of-a-file-or-directory"></a>Ki a fájl vagy könyvtár tulajdonosa?

Egy fájl vagy könyvtár létrehozója lesz a tulajdonos. A gyökérkönyvtár esetében ez annak a felhasználónak az identitása, aki létrehozta a tárolót.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>Melyik csoport van beállítva egy fájl vagy könyvtár tulajdonosi csoportjaként a létrehozáskor?

A tulajdonos csoportot a szülő könyvtár tulajdonos csoportjából másolja a rendszer, amelyben az új fájlt vagy könyvtárat létrehozták.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Egy fájl tulajdonos felhasználója vagyok, de nincs szükségem a RWX engedélyeire. Mit tegyek?

A tulajdonos módosíthatja a fájlhoz tartozó engedélyeket, így bármilyen szükséges RWX-engedélyt megadhat saját magának.

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>Miért látok időnként GUID azonosítókat az ACL-eken?

A GUID azonosító akkor jelenik meg, ha a bejegyzés egy felhasználót jelöl, és a felhasználó már nem létezik az Azure AD-ben. Ez általában akkor történik, ha a felhasználó elhagyta a vállalatot, vagy törölve lett a fiókja az Azure AD-ben. Emellett az egyszerű szolgáltatások és a biztonsági csoportok nem rendelkeznek egyszerű felhasználónévvel (UPN), hogy azonosítsák őket, és hogy azok az OID-attribútum (GUID) szerint legyenek megjelenítve.

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>Hogyan helyesen beállítani az ACL-eket egy egyszerű szolgáltatásnév számára?

Ha az egyszerű szolgáltatások hozzáférés-vezérlési listáit definiálja, fontos, hogy az *egyszerű szolgáltatás* OBJEKTUMAZONOSÍTÓ (OID) használatával használja a létrehozott alkalmazás-regisztrációt. Fontos megjegyezni, hogy a regisztrált alkalmazások külön szolgáltatásnevet biztosítanak az adott Azure AD-bérlőben. A regisztrált alkalmazások egy OID-vel rendelkeznek, amely látható a Azure Portalban, de az *egyszerű szolgáltatásnév* egy másik (eltérő) OID-vel rendelkezik.

Az alkalmazás regisztrálásához használt szolgáltatásnév OID-azonosítójának lekéréséhez használhatja az `az ad sp show` parancsot. Határozza meg az alkalmazás AZONOSÍTÓját paraméterként. Íme egy példa arra, hogyan kérheti le az OID-t az App ID = 18218b12-1895-43e9-ad80-6e8fc1ea88ce azonosítójú alkalmazás-regisztrációnak megfelelő egyszerű szolgáltatásnév számára. Futtassa az alábbi parancsot az Azure CLI-ben:

```azurecli
az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
```

A rendszer az OID-t jeleníti meg.

Ha a szolgáltatásnév helyes OID-je van, lépjen a Storage Explorer a **hozzáférés kezelése** lapra az OID hozzáadásához, és rendelje hozzá a megfelelő engedélyeket az OID-hez. Győződjön meg róla, hogy a **Mentés**gombra kattint.

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>Támogatja Data Lake Storage Gen2 az ACL-ek öröklését?

Az Azure szerepkör-hozzárendelések öröklik. A hozzárendelések az előfizetés, az erőforráscsoport és a Storage-fiók erőforrásaiból származnak a tároló-erőforráshoz.

Az ACL-ek nem öröklik az öröklést. Az alapértelmezett ACL-ek azonban az alárendelt alkönyvtárakhoz és a szülő könyvtár alatt létrehozott fájlokhoz tartozó ACL-ek beállítására is használhatók. 

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Hol tudhatok meg többet a POSIX hozzáférés-vezérlési modellről?

* [POSIX hozzáférés-vezérlési listák Linux rendszeren](https://www.linux.com/news/posix-acls-linux)
* [HDFS-engedély útmutatója](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)
* [POSIX – gyakori kérdések](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [POSIX ACL Ubuntu rendszeren](https://help.ubuntu.com/community/FilePermissionsACLs)
* [Hozzáférés-vezérlési listákat használó ACL Linux rendszeren](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Lásd még

* [A Azure Data Lake Storage Gen2 áttekintése](../blobs/data-lake-storage-introduction.md)
