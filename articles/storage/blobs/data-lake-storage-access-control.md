---
title: Hozzáférés-vezérlés áttekintése az Azure Data Lake Storage Gen2 szolgáltatásban | Microsoft dokumentumok
description: A hozzáférés-vezérlés működésének ismertetése az Azure Data Lake Storage Gen2 szolgáltatásban
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 93c21656a768ae458572e0b4917412c8103b2f2d
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992215"
---
# <a name="access-control-in-azure-data-lake-storage-gen2"></a>Hozzáférés-vezérlés a 2. generációs Azure Data Lake Storage-ben

Az Azure Data Lake Storage Gen2 olyan hozzáférés-vezérlési modellt valósít meg, amely támogatja az Azure szerepköralapú hozzáférés-vezérlési (RBAC) és a POSIX-szerű hozzáférés-vezérlési listákat (ACL-ek). Ez a cikk a Data Lake Storage Gen2 hozzáférés-vezérlési modelljének alapjait foglalja össze.

<a id="azure-role-based-access-control-rbac" />

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

Az RBAC szerepkör-hozzárendeléseket használ a rendszerbiztonsági tagok engedélykészleteinek hatékony *alkalmazására.* A *rendszerbiztonsági tag* egy olyan objektum, amely egy felhasználót, csoportot, egyszerű szolgáltatást vagy felügyelt identitást jelöl, amely az Azure Active Directoryban (AD) van definiálva, és hozzáférést kér az Azure-erőforrásokhoz.

Ezek az Azure-erőforrások általában a legfelső szintű erőforrásokra vannak korlátozva (például: Azure Storage-fiókok). Az Azure Storage, és ebből következően az Azure Data Lake Storage Gen2 esetében ez a mechanizmus ki lett terjesztve a tároló (fájlrendszer) erőforrásra.

Ha meg szeretné tudni, hogyan rendelhet szerepköröket a rendszerbiztonsági tagokhoz a tárfiók hatókörében, olvassa el [a Hozzáférés megadása az Azure blob- és várólista-adatokhoz az RBAC-mal az Azure Portalon című témakört.](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

> [!NOTE]
> Egy vendégfelhasználó nem hozhat létre szerepkör-hozzárendelést.

### <a name="the-impact-of-role-assignments-on-file-and-directory-level-access-control-lists"></a>A szerepkör-hozzárendelések hatása a fájl- és címtárszintű hozzáférés-vezérlési listákra

Az RBAC szerepkör-hozzárendelések használata egy hatékony mechanizmus a hozzáférési engedélyek szabályozására, ez egy nagyon durva szemcsés mechanizmus az ACL-ekhez képest. Az RBAC legkisebb granularitása a tároló szintjén van, és ezt a rendszer magasabb prioritással értékeli ki, mint az ACL-eket. Ezért ha egy tároló hatókörében szerepkört rendel egy rendszerbiztonsági taghoz, akkor a rendszerbiztonsági tag rendelkezik az adott szerepkörhöz társított engedélyezési szinttel az adott tárolóban lévő összes könyvtárhoz és fájlhoz, függetlenül az ACL-hozzárendelésektől.

Ha egy rendszerbiztonsági tag rbac-adatengedélyeket kap egy [beépített szerepkörön](https://docs.microsoft.com/azure/storage/common/storage-auth-aad?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#built-in-rbac-roles-for-blobs-and-queues)keresztül, vagy egy egyéni szerepkörön keresztül, ezeket az engedélyeket a rendszer először egy kérelem engedélyezésekor értékeli ki. Ha a kért műveletet a rendszerbiztonsági tag RBAC-hozzárendelései engedélyezik, az engedélyezés azonnal feloldódik, és nem történik további ACL-ellenőrzés. Másik lehetőségként, ha a rendszerbiztonsági tag nem rendelkezik RBAC-hozzárendeléssel, vagy a kérelem művelete nem felel meg a hozzárendelt engedélynek, akkor az ACL-ellenőrzések et hajt végre annak megállapítására, hogy a rendszerbiztonsági tag jogosult-e a kért művelet végrehajtására.

> [!NOTE]
> Ha a rendszerbiztonsági tag a Storage Blob Data Owner beépített szerepkör-hozzárendelés, majd a rendszerbiztonsági tag tekinthető *egy szuper-felhasználó,* és teljes hozzáférést kap az összes mutáns műveletek, beleértve a beállítás a tulajdonos egy könyvtár vagy fájl, valamint a hozzáférés-kezelők a könyvtárak és fájlok, amelyek nem a tulajdonos. A rendszergazdai hozzáférés az egyetlen engedélyezett módja az erőforrás tulajdonosának módosításához.

## <a name="shared-key-and-shared-access-signature-sas-authentication"></a>Megosztott kulcs és megosztott hozzáférésű aláírás (SAS) hitelesítése

Az Azure Data Lake Storage Gen2 támogatja a megosztott kulcs és a SAS hitelesítési módszereket. A hitelesítési módszerek egyik jellemzője, hogy nincs identitás társítva a hívóhoz, ezért a rendszerbiztonsági rendszer engedélyalapú engedélyenem hajtható végre.

A megosztott kulcs esetében a hívó ténylegesen "szuper-felhasználó" hozzáférést kap, ami teljes hozzáférést jelent az összes erőforrás összes műveletéhez, beleértve a tulajdonos beállítását és az ACL-k módosítását.

A SAS-jogkivonatok a jogkivonat részeként tartalmazzák a megengedett engedélyeket. A SAS-jogkivonatban szereplő engedélyek hatékonyan vonatkoznak az összes engedélyezési döntésre, de nem végeznek további ACL-ellenőrzéseket.

## <a name="access-control-lists-on-files-and-directories"></a>Hozzáférés-vezérlési listák fájlokon és könyvtárakon

A rendszerbiztonsági tagokat a fájlok és könyvtárak hozzáférési szintjéhez társíthatja. Ezeket a társításokat *egy hozzáférés-vezérlési listában (ACL)* rögzítik. A tárfiók minden fájlja és könyvtára rendelkezik hozzáférés-vezérlési listával.

> [!NOTE]
> Az ACL-ok csak az azonos bérlő rendszerbiztonsági tagokra vonatkoznak. 

Ha a tárfiók szintjén szerepkört rendelt egy rendszerbiztonsági taghoz, hozzáférés-vezérlési listák használatával rendszergazdai jogosultsággal biztosíthatja az adott rendszerbiztonsági tag számára az adott fájlokhoz és könyvtárakhoz való hozzáférést.

Hozzáférés-vezérlési listák nem használhatók olyan hozzáférési szint biztosításához, amely alacsonyabb, mint egy szerepkör-hozzárendelés által megadott szint. Ha például a [Storage Blob Data Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) szerepkört egy rendszerbiztonsági taghoz rendeli hozzá, akkor nem használhatja a hozzáférés-vezérlési listákat annak megakadályozására, hogy a rendszerbiztonsági tag egy könyvtárba írjon.


### <a name="set-file-and-directory-level-permissions-by-using-access-control-lists"></a>Fájl- és könyvtárszintű engedélyek beállítása hozzáférés-vezérlési listák használatával

A fájl- és könyvtárszintű engedélyek beállításához tekintse meg az alábbi cikkek egyikét:

|||
|--------|-----------|
|Azure Storage Explorer |[Az Azure Storage Explorer használatával kezelheti a könyvtárakat, fájlokat és ACL-eket az Azure Data Lake Storage Gen2 szolgáltatásban](data-lake-storage-explorer.md#managing-access)|
|.NET |[A .NET használatával kezelheti a könyvtárakat, fájlokat és ACL-okat az Azure Data Lake Storage Gen2 szolgáltatásban](data-lake-storage-directory-file-acl-dotnet.md)|
|Java|[Könyvtárak, fájlok és AC-k kezelése a Java használatával az Azure Data Lake Storage Gen2 szolgáltatásban](data-lake-storage-directory-file-acl-java.md)|
|Python|[A Python használatával kezelheti a könyvtárakat, fájlokat és ACL-okat az Azure Data Lake Storage Gen2-ben](data-lake-storage-directory-file-acl-python.md)|
|PowerShell|[Könyvtárak, fájlok és AC-k kezelése a PowerShell használatával az Azure Data Lake Storage Gen2 szolgáltatásban](data-lake-storage-directory-file-acl-powershell.md)|
|Azure CLI|[Az Azure CLI használatával kezelheti a könyvtárakat, fájlokat és ACL-eket az Azure Data Lake Storage Gen2 szolgáltatásban](data-lake-storage-directory-file-acl-cli.md)|
|REST API |[Elérési út - frissítés](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> Ha a rendszerbiztonsági tag *egy egyszerű szolgáltatás,* fontos, hogy a szolgáltatásnév objektumazonosítóját használja, és nem a kapcsolódó alkalmazásregisztráció objektumazonosítóját. Az egyszerű szolgáltatás objektumazonosítójának lekértje az Azure CLI-t, majd használja ezt a parancsot: `az ad sp show --id <Your App ID> --query objectId`. Győződjön meg `<Your App ID>` róla, hogy lecseréli a helyőrzőt az alkalmazásregisztráció alkalmazásazonosítójára.

### <a name="types-of-access-control-lists"></a>A hozzáférés-vezérlési listák típusai

Kétféle hozzáférés-vezérlési lista létezik: *hozzáférés-hozzáférés-vezérlési listák* és *alapértelmezett Hozzáférés-vezérlési listák*.

Az Access Hozzáférés-vezérlési adatok szabályozzák az objektumhoz való hozzáférést. Files and directories both have access ACLs.

Default ACLs are templates of ACLs associated with a directory that determine the access ACLs for any child items that are created under that directory. A fájlok nem rendelkeznek alapértelmezett ACL-okkal.

Both access ACLs and default ACLs have the same structure.

> [!NOTE]
> A szülő alapértelmezett hozzáférés-kulcszatának módosítása nincs hatással a már meglévő gyermekelemek hozzáférési hozzáférés-hozzáférés-hozzáférés-hozzáférési kulcsára vagy alapértelmezett hozzáférési kulcsára.

### <a name="levels-of-permission"></a>Az engedély szintjei

A tárolóobjektumok engedélyei: **Olvasás**, **Írás**és **Végrehajtás**, és fájlokon és könyvtárakon használhatók az alábbi táblázatban látható módon:

|            |    Fájl     |   Címtár |
|------------|-------------|----------|
| **Olvasás (R)** | Olvashatja a fájl tartalmát | **Olvasás és** **végrehajtás** szükséges a könyvtár tartalmának listázásához |
| **Írás (W)** | Írhatja a fájlt vagy hozzáfűzhet a fájlhoz | **Írás** és **végrehajtás** szükséges gyermekelemek létrehozásához egy könyvtárban |
| **Végrehajtás (X)** | Nem jelent semmit a Data Lake Storage Gen2 | Könyvtár gyermekelemeinek bejárásához szükséges |

> [!NOTE]
> Ha csak ACL-ek használatával ad meg engedélyeket (nincs RBAC), akkor a rendszerbiztonsági tag olvasási vagy írási hozzáférésének biztosításához meg kell adnia a **rendszerbiztonsági** tag végrehajtási engedélyeket a tárolónak és a fájlhoz vezető mappák hierarchiájának minden mappájának.

#### <a name="short-forms-for-permissions"></a>Az engedélyek rövid alakjai

**RWX** az **Olvasás + Írás + Végrehajtás** engedélyt jelöli. Egy tömörebb, numerikus formátum is létezik, amelyben **Olvasás=4**, **Írás=2** és **Végrehajtás=1**, és az összegük jelöli az engedélyeket. Az alábbiakban néhány példa látható.

| Numerikus alak | Rövid alak |      Jelentés     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Olvasás + Írás + Végrehajtás |
| 5            | `R-X`        | Olvasás + Végrehajtás         |
| 4            | `R--`        | Olvasás                   |
| 0            | `---`        | Nincs engedély         |

#### <a name="permissions-inheritance"></a>Engedélyek öröklése

A Data Lake Storage Gen2 által használt POSIX-stílusú modellben az elem engedélyei magán az elemen tárolódnak. Más szóval egy elem engedélyei nem örökölhetők a szülőelemektől, ha az engedélyek a gyermekelem létrehozása után vannak beállítva. Az engedélyek csak akkor öröklődnek, ha a gyermekelemek létrehozása előtt alapértelmezett engedélyek vannak beállítva a szülőelemekre.

### <a name="common-scenarios-related-to-permissions"></a>Az engedélyekhez kapcsolódó gyakori helyzetek

Az alábbi táblázat néhány gyakori forgatókönyvet sorol fel, amelyek segítenek megérteni, hogy milyen engedélyek szükségesek bizonyos műveletek végrehajtásához egy tárfiókban.

|    Művelet             |    /    | Oregon/ | Portland/ | Data.txt fájl     |
|--------------------------|---------|----------|-----------|--------------|
| Data.txt fájl olvasása            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Hozzáfűzés a Data.txt fájlhoz       |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Data.txt fájl törlése          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Data.txt fájl létrehozása          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Lista /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| Lista /Oregon/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| Lista /Oregon/Portland/  |   `--X`   |   `--X`    |  `R-X`      | `---`          |

> [!NOTE]
> A fájl írási engedélyei nem szükségesek a fájl törléséhez, feltéve, hogy az előző két feltétel teljesül.

### <a name="users-and-identities"></a>Felhasználók és identitások

Minden fájl és könyvtár külön engedélyekkel rendelkezik ezekhez az identitásokhoz:

- A tulajdonos felhasználó
- A tulajdonoscsoport
- Nevesített felhasználók
- Nevesített csoportok
- Elnevezett egyszerű szolgáltatás
- Elnevezett felügyelt identitások
- Minden egyéb felhasználó

A felhasználók és csoportok identitása Azure Active Directory- (Azure AD-) indentitás. Így eltérő módon, ha a *felhasználó*, a Data Lake Storage Gen2 környezetben, hivatkozhat egy Azure AD-felhasználó, egyszerű szolgáltatás, felügyelt identitás vagy biztonsági csoport.

#### <a name="the-owning-user"></a>A tulajdonos felhasználó

Automatikusan az elem tulajdonosa lesz az a felhasználó, aki létrehozta az elemet. A tulajdonos felhasználó:

* Megváltoztathatja a tulajdonában lévő fájl engedélyeit.
* megváltoztathatja a tulajdonában lévő fájl tulajdonos csoportját, ha a tulajdonos felhasználó szintén tagja ennek a csoportnak.

> [!NOTE]
> A tulajdonában lévő felhasználó *nem módosíthatja* egy fájl vagy könyvtár tulajdonában lévő felhasználót. Csak a szuperfelhasználók módosíthatják egy fájl vagy könyvtár tulajdonában lévő felhasználót.

#### <a name="the-owning-group"></a>A tulajdonoscsoport

A POSIX ACL-ekben minden felhasználó egy *elsődleges csoporthoz*van társítva. Előfordulhat például, hogy az "Alice" felhasználó a "finance" csoporthoz tartozik. Alice is több csoporthoz is tartozhat, de egy csoport mindig elsődleges csoportként van kijelölve. A POSIX-ben ha Alice létrehoz egy fájlt, a fájl tulajdonoscsoportja Alice elsődleges csoportja lesz, ami ebben az esetben a „finance”. A tulajdonos csoport egyéb esetben egyéb felhasználókhoz/csoportokhoz hozzárendelt engedélyekhez hasonlóan viselkedik.

##### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>A birtokló csoport hozzárendelése új fájlhoz vagy könyvtárhoz

* **1. eset**: A gyökérkönyvtár "/". Ez a könyvtár a Data Lake Storage Gen2 tároló létrehozásakor jön létre. Ebben az esetben a tulajdonában lévő csoport a tárolót létrehozó felhasználóra van beállítva, ha az OAuth használatával történt. Ha a tároló megosztott kulcs, fiókSAS vagy szolgáltatás SAS használatával jön létre, akkor a tulajdonos és a tulajdonos csoport **$superuser**lesz.
* **2. eset** (Minden második eset): Új elem létrehozásakor a tulajdonos csoport a szülőkönyvtárból lesz másolva.

##### <a name="changing-the-owning-group"></a>A birtokló csoport módosítása

A tulajdonoscsoportot megváltoztathatja:
* Bármely felügyelő.
* a tulajdonos, ha szintén tagja ennek a csoportnak.

> [!NOTE]
> A tulajdonában lévő csoport nem módosíthatja egy fájl vagy könyvtár AC-jeit.  Míg a tulajdonában lévő csoport a felhasználói fiókot létrehozó felhasználóra van beállítva a gyökérkönyvtár esetében, a fenti **1.** Az engedélyt hozzárendelheti egy érvényes felhasználócsoporthoz, ha van ilyen.

### <a name="access-check-algorithm"></a>Hozzáférés-ellenőrzési algoritmus

A következő pszeudokód a tárfiókok hozzáférés-ellenőrzési algoritmusát jelöli.

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

Ahogy azt az Access Check Algorithm szemlélteti, a maszk korlátozza a hozzáférést a megnevezett felhasználók, a tulajdonában lévő csoport és a megnevezett csoportok számára.  

> [!NOTE]
> For a new Data Lake Storage Gen2 container, the mask for the access ACL of the root directory ("/") defaults to 750 for directories and 640 for files. A fájlok nem kapják meg az X bitet, mivel az nem releváns a csak tárolóban lévő rendszer fájljai számára.
>
> A maszk hívásonként is megadható. Ez lehetővé teszi, hogy a különböző fogyasztó rendszerek, például a fürtök, különböző hatékony maszkokkal rendelkeznek a fájlműveletekhez. Ha egy maszk meg van adva egy adott kérelemben, az teljesen felülírja az alapértelmezett maszkot.

#### <a name="the-sticky-bit"></a>Ragadós bit

A ragadós bit a POSIX tároló fejlettebb jellemzője. A Data Lake Storage Gen2 környezetében nem valószínű, hogy a ragadós bitre lesz szükség. Összefoglalva, ha a ragadós bit engedélyezve van egy könyvtárban, a gyermekelemet csak a gyermekelem tulajdonában lévő felhasználó törölheti vagy nevezheti át.

A ragadós bit nem jelenik meg az Azure Portalon.

### <a name="default-permissions-on-new-files-and-directories"></a>Alapértelmezett engedélyek az új fájlokhoz és könyvtárakhoz

Ha egy meglévő könyvtár alatt új fájlt vagy könyvtárat hoz létre, a szülőkönyvtár alapértelmezett acl-je a következőket határozza meg:

- A child directory's default ACL and access ACL.
- A child file's access ACL (files do not have a default ACL).

#### <a name="umask"></a>umask

Fájl vagy könyvtár létrehozásakor az umask segítségével módosíthatja, hogy az alapértelmezett ACL-k hogyan vannak beállítva a gyermekelemen. Az umask a szülőkönyvtárak 9 bites értéke, amely RWX-értéket tartalmaz a felhasználó , **a tulajdonos csoport**és az **egyéb**könyvtárak **tulajdonában.**

Az Azure Data Lake Storage Gen2 umask egy 007-es állandó érték. Ez az érték a következőkre fordítva:

| umask összetevő     | Numerikus alak | Rövid alak | Jelentés |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | Felhasználó esetén másolja a szülő alapértelmezett hozzáférés-hozzáférés-kulcsát a gyermek hozzáférési kulcsára | 
| umask.owning_group  |    0         |   `---`      | For owning group, copy the parent's default ACL to the child's access ACL | 
| umask.más         |    7         |   `RWX`      | Más esetén távolítsa el a gyermek hozzáférési engedélyére vonatkozó összes engedélyt |

Az Azure Data Lake Storage Gen2 által használt umask érték hatékonyan azt jelenti, hogy a **többi érték** alapértelmezés szerint soha nem kerül továbbításra az új gyermekek, függetlenül attól, hogy az alapértelmezett ACL jelzi. 

A következő pszeudokód bemutatja, hogyan alkalmazza az umask-ot az ACL-ek létrehozásakor egy gyermekelemhez.

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

## <a name="common-questions-about-acls-in-data-lake-storage-gen2"></a>Gyakori kérdések a Data Lake Storage Gen2 ACL-jével kapcsolatban

### <a name="do-i-have-to-enable-support-for-acls"></a>Engedélyeznem kell az ACL támogatását?

Nem. Az ACL-eken keresztüli hozzáférés-vezérlés engedélyezve van egy tárfiókhoz, amíg a Hierarchikus névtér (HNS) szolgáltatás be van kapcsolva.

Ha a HNS ki van kapcsolva, az Azure RBAC engedélyezési szabályok továbbra is érvényesek.

### <a name="what-is-the-best-way-to-apply-acls"></a>Mi a legjobb módja az ACL-k alkalmazásának?

Mindig használja az Azure AD biztonsági csoportok, mint a hozzárendelt egyszerű a cl-k. Ellenálljon az egyes felhasználók vagy egyszerű szolgáltatástagok közvetlen hozzárendelésének lehetőségének. Ezzel a struktúrával felhasználókat vagy egyszerű szolgáltatást kell hozzáadnia és eltávolítania anélkül, hogy újra alkalmaznia kellene az ACL-eket egy teljes könyvtárstruktúrára. ) Ehelyett egyszerűen hozzá kell adnia vagy el kell távolítania őket a megfelelő Azure AD biztonsági csoportból. Ne feledje, hogy az ACL-ek nem öröklődnek, ezért az ACL-ek újbóli alkalmazása minden fájlés alkönyvtár rendszerében frissíteni kell az ACL-t. 

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>Milyen engedélyek szükségesek a könyvtár és annak tartalmának rekurzív törléséhez?

- A hívó "szuper-felhasználó" engedélyekkel rendelkezik,

Vagy

- A szülőkönyvtárnak írási + végrehajtási engedéllyel kell rendelkeznie.
- A törölni a könyvtárhoz és a benne lévő összes könyvtárhoz olvasási + írási + végrehajtási engedélyek szükségesek.

> [!NOTE]
> A könyvtárakban lévő fájlok törléséhez nincs szükség írási engedélyre. A gyökérkönyvtár "/" soha nem törölhető.

### <a name="who-is-the-owner-of-a-file-or-directory"></a>Ki a tulajdonosa egy fájlnak vagy könyvtárnak?

A fájl vagy könyvtár létrehozója lesz a tulajdonos. A gyökérkönyvtár esetében ez a tárolót létrehozó felhasználó identitása.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>Melyik csoport van beállítva egy fájl vagy könyvtár tulajdonában lévő csoportként a létrehozáskor?

A tulajdonos csoport annak a szülőkönyvtárnak a tulajdonos csoportjából lesz másolva, amely alatt az új fájl vagy könyvtár létrejön.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Én vagyok a tulajdonában lévő felhasználó egy fájlt, de nem rendelkezem a RWX engedélyeket van szükségem. Mit tegyek?

A tulajdonos módosíthatja a fájlhoz tartozó engedélyeket, így bármilyen szükséges RWX-engedélyt megadhat saját magának.

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>Miért jelennek meg néha GUID-ok az ACL-kben?

A GUID akkor jelenik meg, ha a bejegyzés egy felhasználót jelöl, és a felhasználó már nem létezik az Azure AD-ben. Ez általában akkor történik, ha a felhasználó elhagyta a vállalatot, vagy törölve lett a fiókja az Azure AD-ben. Ezenkívül a szolgáltatásnevek és a biztonsági csoportok nem rendelkeznek egyszerű felhasználónévvel (UPN) az azonosításukhoz, ezért az OID-attribútumuk (guid) képviseli őket.

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>Hogyan állíthatom be helyesen az ACL-okat egy egyszerű szolgáltatáshoz?

Amikor a rendszernévi elvekhez a cl-ket definiálja, fontos, hogy a létrehozott alkalmazásregisztrációhoz a *szolgáltatásnév* objektumazonosítóját (OID) használja. Fontos megjegyezni, hogy a regisztrált alkalmazások egy külön egyszerű szolgáltatásaz adott Azure AD-bérlőben rendelkezik. A regisztrált alkalmazások rendelkeznek egy OID, amely látható az Azure Portalon, de a szolgáltatás egyszerű egy másik (más) OID.Registered apps have a OID that's visible in the Azure portal but the *service principal* has another (different) OID.

Az alkalmazásregisztrációnak megfelelő egyszerű szolgáltatásazonosító lekérni, használhatja `az ad sp show` a parancsot. Adja meg az alkalmazásazonosítót paraméterként. Íme egy példa az OID beszerzésére a szolgáltatásnévhez, amely megfelel egy alkalmazásregisztrációnak az Alkalmazásazonosítóval = 18218b12-1895-43e9-ad80-6e8fc1ea88ce. Futtassa a következő parancsot az Azure CLI-ben:

```azurecli
az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
```

az OID jelenik meg.

Ha rendelkezik a megfelelő OID a szolgáltatás egyszerű, menj a Storage Explorer **Manage Access** lap hozzá az OID, és rendelje hozzá a megfelelő engedélyeket az OID. Győződjön meg arról, hogy a Mentés lehetőséget **választja.**

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>A Data Lake Storage Gen2 támogatja az ACL-ek öröklődését?

Az Azure RBAC-hozzárendelések örökölnek. A hozzárendelések az előfizetési, erőforráscsoport- és tárfiók-erőforrásoktól a tárolóerőforrásig áramlanak.

Az ACL-ok nem örökölnek. Az alapértelmezett ACL-k azonban a szülőkönyvtár alatt létrehozott gyermekalkönyvtárak és fájlok ACL-jainak beállítására használhatók. 

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Hol tudhatok meg többet a POSIX hozzáférés-vezérlési modellről?

* [POSIX hozzáférés-vezérlési listák Linux rendszeren](https://www.linux.com/news/posix-acls-linux)
* [HDFS-engedélyútmutató](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)
* [POSIX – gyakori kérdések](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [POSIX ACL Ubuntu rendszeren](https://help.ubuntu.com/community/FilePermissionsACLs)
* [Hozzáférés-vezérlési listák használatával Az ACL Linuxon](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Lásd még

* [Az Azure Data Lake Storage Gen2 áttekintése](../blobs/data-lake-storage-introduction.md)
