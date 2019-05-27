---
title: Hozzáférés-vezérlés az Azure Data Lake Storage Gen2 áttekintése |} A Microsoft Docs
description: Hozzáférés-vezérlés működésének megismerése az Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 5ad7ef714147616fe55a9b978d501b974323e251
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65949577"
---
# <a name="access-control-in-azure-data-lake-storage-gen2"></a>Hozzáférés-vezérlés az Azure Data Lake Storage Gen2

Az Azure Data Lake Storage Gen2-hozzáférés-vezérlési modellt, amely támogatja az Azure szerepköralapú hozzáférés-vezérlés (RBAC) és a POSIX-hoz hasonló hozzáférés-vezérlési listák (ACL) valósítja meg. Ez a cikk a Data Lake Storage Gen2 a a hozzáférés-vezérlési modelljének alapjait foglalja össze.

<a id="azure-role-based-access-control-rbac" />

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

RBAC szerepkör-hozzárendelések használ jogosultságkészletek a alkalmazni *rendszerbiztonsági*. A *rendszerbiztonsági tag* olyan objektum, amely egy felhasználó, csoport, egyszerű szolgáltatás vagy az Azure Active Directory (AD), amely az Azure-erőforrásokhoz való hozzáférést kér a meghatározott felügyelt identitás jelöli.

Általában ezek az Azure előrébb a legfelső szintű erőforrásokhoz (például: Az Azure Storage-fiókok). Azure Storage, és ennek megfelelően az Azure Data Lake Storage Gen2 esetében ez a mechanizmus most már elérhető a fájl rendszererőforrás.

A storage-fiók hatókörében rendszerbiztonsági szerepköröket hozzárendelni kezelésével kapcsolatos információkért lásd: [hitelesítés hozzáférés az Azure-blobok és üzenetsorok az Azure Active Directoryval](https://docs.microsoft.com/azure/storage/common/storage-auth-aad?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="the-impact-of-role-assignments-on-file-and-directory-level-access-control-lists"></a>A fájl- és fájlszintű hozzáférés-vezérlési listák a szerepkör-hozzárendelések hatása

Bár az RBAC szerepkör-hozzárendelést a következő egy hatékony mechanizmus, amellyel szabályozhatja a hozzáférési engedélyek, egy hozzáférés-vezérlési listák viszonyítva szemcsézettségű nagyon részletes mechanizmust. A legkisebb részletességgel RBAC a fájlrendszer szintjén, és ez magasabb prioritással, mint ACL-ek kiértékelendő. Ezért egy szerepkört rendel egy rendszerbiztonsági tag hatókörébe tartozó operációs rendszer, ha a rendszerbiztonsági tagot a jogosultsági szinteket, a fájlrendszer ACL hozzárendelések függetlenül minden könyvtárnak és fájlnak a szerepkörhöz kapcsolódó is.

Ha egy rendszerbiztonsági tag jogosultságot kap az RBAC adatok keresztül egy [beépített szerepkör](https://docs.microsoft.com/azure/storage/common/storage-auth-aad?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#built-in-rbac-roles-for-blobs-and-queues), vagy egy egyéni szerepkör segítségével ezeket az engedélyeket értékeli ki a először engedélyezési kérés esetén. Ha a rendszerbiztonsági tagot az RBAC-hozzárendelések által jogosult a kért művelet, akkor azonnal megoldódott, és nincs további engedélyezési ACL-ellenőrzés történik. Azt is megteheti, ha a rendszerbiztonsági tag nem rendelkezik az RBAC-hozzárendelés, vagy a kért művelet nem egyezik meg a hozzárendelt engedély, majd ACL ellenőrzések elvégzése, határozza meg, hogy a rendszerbiztonsági tagot a kért művelet végrehajtására jogosult.

> [!NOTE]
> Ha a rendszerbiztonsági tagot a tárolási Blob adatok tulajdonosa beépített szerepkör-hozzárendelés hozzá lett rendelve, akkor a rendszerbiztonsági tag számít egy *felügyelő* mutating műveleteket, köztük a beállítás a teljes hozzáférést kapnak, és a tulajdonosa egy könyvtárat vagy fájlt, valamint a hozzáférés-vezérlési listák a könyvtárak és fájlok, amelynek azok nem a tulajdonosa. Felügyelő hozzáférés csak engedélyezett módon módosítani az erőforrás tulajdonosa.

## <a name="shared-key-and-shared-access-signature-sas-authentication"></a>Megosztott kulcs és a közös hozzáférésű Jogosultságkód (SAS) hitelesítési

Az Azure Data Lake Storage Gen2 megosztott kulcsos és a SAS módszert támogat a hitelesítéshez. Egy ezek a hitelesítési módszerek jellemzője, hogy nincs identitás nem tartozik a hívó, és ezért nem hajtható végre biztonsági egyszerű engedély-alapú hitelesítést.

Megosztott kulcs esetén a hívó hatékonyan "felügyelő" hozzáférést kap, ami azt jelenti, az összes erőforrás, beleértve a tulajdonosi beállítás és ACL-ek módosítása az összes művelet teljes hozzáférést.

SAS-tokeneket használható engedélyek a token részeként tartalmazza. Az engedélyeket a SAS-jogkivonat szereplő összes felhasználását engedélyezési döntésekhez hatékonyan érvényesek, de nincs további ACL-ellenőrzés történik.

## <a name="access-control-lists-on-files-and-directories"></a>Hozzáférés-vezérlési listák a fájlok és könyvtárak

A szolgáltatásnevek társíthatja egy hozzáférési szintet a fájlok és könyvtárak. Ezeket a hozzárendeléseket a rendszer rögzített egy *hozzáférés-vezérlési lista (ACL)*. Minden fájl- és a storage-fiókban van a hozzáférés-vezérlési lista.

Ha egy szerepkörhöz rendelt a tárolási fiók szintjén a rendszerbiztonsági tag, hozzáférés-vezérlési listák használatával adja meg a rendszerbiztonsági tag egyedi fájlokat és könyvtárakat az emelt szintű hozzáférés.

Adja meg a hozzáférési szintet, amelyet alacsonyabb, mint a szerepkör-hozzárendelés által nyújtott szintű hozzáférés-vezérlési listák nem használható. Például, ha a [Storage-Blobadatok Közreműködője](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor-preview) szerepkör és a egy egyszerű, nem használhatja a hozzáférés-vezérlés security megakadályozza, hogy a rendszerbiztonsági tag-címtárhoz való írása sorolja fel.

### <a name="set-file-and-directory-level-permissions-by-using-access-control-lists"></a>Fájl- és szintű engedélyek beállítása a hozzáférés-vezérlési listák

Fájl- és oszlopszintű engedélyek beállításához jelennek meg a következő cikkeket:

|Ha szeretne ezzel az eszközzel:    |Ebben a cikkben talál:    |
|--------|-----------|
|Azure Storage Explorer    |[Azure Storage Explorer használata az Azure Data Lake Storage Gen2 fájl- és oszlopszintű engedélyek beállítása](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)|
|REST API    |[Elérési út – a frissítés](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> Ha a rendszerbiztonsági tag egy *szolgáltatás* egyszerű, fontos, hogy a szolgáltatásnév Objektumazonosítóját és nem a kapcsolódó alkalmazásregisztráció Objektumazonosítóját. A szolgáltatásnév Objektumazonosítóját nyissa meg az Azure CLI, és ezután használja a következő parancsot: `az ad sp show --id <Your App ID> --query objectId`. cserélje le a `<Your App ID>` az Alkalmazásazonosító az alkalmazás regisztrációját a helyőrzőt.

### <a name="types-of-access-control-lists"></a>Hozzáférés-vezérlési listák típusai

Kétféle hozzáférés-vezérlési listákkal van: *hozzáférési ACL-ek* és *alapértelmezett ACL-ek*.

Hozzáférési ACL-objektumhoz való hozzáférés szabályozása. Fájlok és mappák egyaránt rendelkeznek hozzáférési ACL-ek.

Alapértelmezett ACL-ek olyan sablonok, az ACL-ek társított címtár, amely a hozzáférési ACL-ek minden könyvtár alatt létrehozott gyermekelemek határozza meg. Fájlok nem rendelkeznek alapértelmezett ACL-ek.

Mindkét hozzáférési ACL-EK és alapértelmezett ACL-ek ugyanazzal a struktúrával rendelkeznek.

Mindkét hozzáférési ACL-EK és alapértelmezett ACL-ek ugyanazzal a struktúrával rendelkeznek.

> [!NOTE]
> Módosítja az alapértelmezett szülő ACL nem befolyásolja a hozzáférési ACL és alapértelmezett ACL-jéhez már létező gyermekelemek.

### <a name="levels-of-permission"></a>Engedélyszintek

A Fájlrendszerobjektum engedélyei vannak **olvasási**, **írási**, és **Execute**, és azok a fájlok és könyvtárak az alábbi táblázatban látható módon:

|            |    Fájl     |   Könyvtár |
|------------|-------------|----------|
| **Olvasás (R)** | Olvashatja a fájl tartalmát | Szükséges **olvasási** és **Execute** a könyvtár tartalmának listázásához |
| **Írás (W)** | Írhatja a fájlt vagy hozzáfűzhet a fájlhoz | Szükséges **írási** és **Execute** szükséges gyermekelemek létrehozásához a címtárban |
| **Végrehajtás (X)** | Nem jelent semmit a Data Lake Storage Gen2 környezetében | Szükséges gyermekelemeinek bejárásához könyvtár |

#### <a name="short-forms-for-permissions"></a>Az engedélyek rövid alakjai

**RWX** az **Olvasás + Írás + Végrehajtás** engedélyt jelöli. Egy tömörebb, numerikus formátum is létezik, amelyben **Olvasás=4**, **Írás=2** és **Végrehajtás=1**, és az összegük jelöli az engedélyeket. Az alábbiakban néhány példa látható.

| Numerikus alak | Rövid alak |      Jelentés     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Olvasás + Írás + Végrehajtás |
| 5            | `R-X`        | Olvasás + Végrehajtás         |
| 4            | `R--`        | Olvasás                   |
| 0            | `---`        | Nincsenek engedélyek         |

#### <a name="permissions-inheritance"></a>Engedélyöröklés

A Data Lake Storage Gen2 által használt POSIX-stílusú modellben az elemhez tartozó engedélyek magában az elemben tárolódnak. Más szóval az elemhez tartozó engedélyek nem örökölheti a szülőelemektől. Ha az engedélyek beállítása után a gyermek elem már létre van hozva. Ha az alapértelmezett engedélyek beállítása a szülő elemek előtt létrehozott bejárásához csak örökölt engedélyekkel.

### <a name="common-scenarios-related-to-permissions"></a>Az engedélyekhez kapcsolódó gyakori helyzetek

Az alábbi táblázat néhány gyakori helyzet segítenek megérteni, hogy a storage-fiók bizonyos műveletek elvégzéséhez milyen engedélyek szükségesek.

|    Művelet             |    /    | Oregon / | Portland / | Data.txt     |
|--------------------------|---------|----------|-----------|--------------|
| Olvassa el a Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Data.txt hozzáfűzése       |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Data.txt törlése          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Data.txt létrehozása          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Listája /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| Lista /Oregon/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| Lista /Oregon/Portland /  |   `--X`   |   `--X`    |  `R-X`      | `---`          |

> [!NOTE]
> Az írási engedéllyel a fájl nem szükségesek törölni, mindaddig, amíg az előbbi két feltétel teljesül.

### <a name="users-and-identities"></a>Felhasználók és identitások

Minden fájl és könyvtár ezeket az identitásokat külön engedélyekkel rendelkezik:

- A tulajdonos felhasználó
- A tulajdonoscsoport
- Nevesített felhasználók
- Nevesített csoportok
- Elnevezett szolgáltatásnevek
- Felügyelt identitások nevű
- Minden egyéb felhasználó

A felhasználók és csoportok identitása Azure Active Directory- (Azure AD-) indentitás. Igen, ha nincs másképp jelölve, egy *felhasználói*, a Data Lake Storage Gen2 környezetében, tekintse meg az Azure AD-felhasználó is, egyszerű, felügyelt identitás vagy biztonsági csoportot.

#### <a name="the-owning-user"></a>A tulajdonos felhasználó

Automatikusan az elem tulajdonosa lesz az a felhasználó, aki létrehozta az elemet. A tulajdonos felhasználó:

* Megváltoztathatja a tulajdonában lévő fájl engedélyeit.
* megváltoztathatja a tulajdonában lévő fájl tulajdonos csoportját, ha a tulajdonos felhasználó szintén tagja ennek a csoportnak.

> [!NOTE]
> A tulajdonos felhasználó *nem* változtathatja tulajdonos a fájl vagy könyvtár. Csak a felügyelők változtathatják a tulajdonos a fájl vagy könyvtár.

#### <a name="the-owning-group"></a>A tulajdonoscsoport

A POSIX ACL-ekben minden felhasználó társítva van egy *elsődleges csoportos*. Például "Alice nevű" felhasználó a "finance" csoportba tartozhat. Alice több csoporthoz is tartozhat, de egy csoport mindig ki van jelölve az elsődleges csoportjaként. A POSIX-ben ha Alice létrehoz egy fájlt, a fájl tulajdonoscsoportja Alice elsődleges csoportja lesz, ami ebben az esetben a „finance”. A tulajdonos csoport egyéb esetben egyéb felhasználókhoz/csoportokhoz hozzárendelt engedélyekhez hasonlóan viselkedik.

##### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>A tulajdonoscsoport egy új fájl vagy címtár hozzárendelése

* **1. eset**: A gyökérkönyvtár "/". Ez a könyvtár jön létre egy Data Lake Storage Gen2 fájlrendszer létrehozásakor. Ebben az esetben a tulajdonoscsoport van beállítva, a felhasználó, aki létrehozta a fájlrendszer, ha történt az OAuth használatával. Ha a fájlrendszer jön létre a megosztott kulcs, a fiók SAS vagy a szolgáltatás SAS, akkor a tulajdonos és a tulajdonoscsoport beállítása **$superuser**.
* **2. eset** (minden egyéb eset): Új elem létrehozásakor a tulajdonoscsoport átmásolva a szülőkönyvtárat.

##### <a name="changing-the-owning-group"></a>A tulajdonoscsoport módosítása

A tulajdonoscsoportot megváltoztathatja:
* Bármely felügyelő.
* a tulajdonos, ha szintén tagja ennek a csoportnak.

> [!NOTE]
> A tulajdonoscsoport a hozzáférés-vezérlési listák a fájl vagy könyvtár nem módosítható.  Miközben a felhasználó, aki létrehozta a fiókot a gyökérkönyvtár esetén beállítása a tulajdonoscsoport **1. eset** fent, egyetlen felhasználói fiók nem jogosult engedélyeket kiadni a tulajdonoscsoporton keresztül érvényes. Az engedélyt hozzárendelheti egy érvényes felhasználócsoporthoz, ha van ilyen.

### <a name="access-check-algorithm"></a>Hozzáférés-ellenőrzési algoritmus

A következő pseudocode tárfiókok esetében a hozzáférés-ellenőrzési algoritmus jelöli.

```
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

Ahogyan a hozzáférés-ellenőrzési algoritmus, a maszk a nevesített felhasználók, tulajdonoscsoporthoz és nevesített csoportok hozzáférést korlátozza.  

> [!NOTE]
> Egy új Data Lake Storage Gen2-fájlrendszer a hozzáférési ACL-t a gyökérkönyvtár ("/") maszk 750 könyvtárak és fájlok 640 alapértelmezés szerint. Fájlok nem jelenik meg az X bit, mert nem számít, a fájlok csak áruház rendszerben.
>
> A maszk hívás alapon adható meg. Ez lehetővé teszi a különböző fogyasztó rendszert, például a fürtökben, így azok fájlműveletek különböző hatékony maszkok rendelkezik. Ha egy adott kérelem maszk van megadva, teljesen felülbírálja az alapértelmezett maszkot.

#### <a name="the-sticky-bit"></a>Ragadós bit

A ragadós bit a POSIX-fájlrendszer egy speciális funkció. Data Lake Storage Gen2 összefüggésben nem valószínű, hogy a ragadós bit lesz szükség. Összefoglalva a ragadós bit be van kapcsolva egy könyvtárat, ha egy gyermek-konfigurációelem csak törölhető vagy átnevezték a gyermek-konfigurációelem tulajdonosa felhasználó.

A ragadós bit nem látható az Azure Portalon.

### <a name="default-permissions-on-new-files-and-directories"></a>Új fájlok és könyvtárak alapértelmezett engedélyek

Ha egy új fájl vagy könyvtár jön létre a meglévő címtárhoz, az alapértelmezett ACL a szülőkönyvtárban határozza meg:

- Egy alárendelt könyvtár alapértelmezett ACL-t és hozzáférési ACL-t.
- Gyermekfájl hozzáférési ACL-t (fájloknak nincs alapértelmezett ACL-t).

#### <a name="umask"></a>umask

Egy fájl vagy könyvtár létrehozásakor umask segítségével módosíthatja az alapértelmezett ACL-ek az alárendelt elem beállításának módját. umask egy 9 bites érték, amely tartalmaz egy RWX-érték a szülőkönyvtárak **tulajdonos**, **tulajdonoscsoport**, és **más**.

Az Azure Data Lake Storage Gen2 állandó érték, amely az umask 007 beállítása. Ezt az értéket a rendszer lefordítja arra:

| umask összetevő     | Numerikus alak | Rövid alak | Jelentés |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | A tulajdonos felhasználó, másolja a szülő alapértelmezett ACL-t a gyermek hozzáférési ACL-t | 
| umask.owning_group  |    0         |   `---`      | A tulajdonoscsoport, másolja a szülő alapértelmezett ACL-t a gyermek hozzáférési ACL-t | 
| umask.Other         |    7         |   `RWX`      | Más a gyermek hozzáférési ACL-t minden engedély eltávolítása |

Az Azure Data Lake Storage Gen2 hatékonyan használható umask érték azt jelenti, hogy értéke **más** soha nem továbbított alapértelmezés szerint az új típusú gyermekei, függetlenül attól, mely az alapértelmezett ACL-t jelzi. 

A következő pseudocode bemutatja, hogyan kell alkalmazni az umask a hozzáférés-vezérlési egy gyermek-konfigurációelem létrehozása során.

```
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

## <a name="common-questions-about-acls-in-data-lake-storage-gen2"></a>A Data Lake Storage Gen2 ACL-lel kapcsolatos gyakori kérdésekre

### <a name="do-i-have-to-enable-support-for-acls"></a>Engedélyeznem kell az ACL támogatását?

Nem. Az ACL-alapú hozzáférés-vezérlés engedélyezve van egy storage-fiókot, amíg a hierarchikus Namespace (HNS) funkció be van kapcsolva.

Ha HNS ki van kapcsolva, az Azure RBAC-engedélyezési szabályok továbbra is a alkalmazni.

### <a name="what-is-the-best-way-to-apply-acls"></a>Mi az a legjobb módszer az ACL-ek alkalmazása?

Az Azure AD biztonsági csoportok használata a hozzárendelt rendszerbiztonsági tag ACL-ek. A lehetőség közvetlenül hozzárendelése az egyes felhasználókat vagy szolgáltatásnevek ellenállni. Ez a struktúra használatával lehetővé teszi, hogy hozzá és távolíthat el felhasználókat vagy szolgáltatásnevek szeretne újból alkalmazni a hozzáférés-vezérlési listák, egy teljes könyvtárstruktúrát nélkül. ) Ehelyett kíván hozzáadni, vagy távolítsa el a megfelelő Azure AD biztonsági csoportot. Ne feledje, hogy a hozzáférés-vezérlési listák nem öröklődnek, és így újbóli az ACL-eket frissíteni kell az az ACL-t minden fájl és alkönyvtárat. 

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>Milyen engedélyek szükségesek és tartalma rekurzív törléséhez egy könyvtár?

- A hívó jogosult "felügyelő",

Vagy

- A szülőkönyvtárhoz kell rendelkezhetnek írási és végrehajtási engedélyeket.
- A törlendő, és minden könyvtárat benne található igényel olvasás + írás + végrehajtás engedéllyel.

> [!NOTE]
> Ön nem szükséges írási engedély könyvtárakban található fájlok törléséhez. Emellett a gyökérkönyvtár "/" soha nem lehet törölni.

### <a name="who-is-the-owner-of-a-file-or-directory"></a>Ki az egyik fájl vagy könyvtár tulajdonosa?

Egy fájl vagy könyvtár létrehozója lesz a tulajdonosa. A gyökérkönyvtár esetén ez az a felhasználó, aki létrehozta a fájlrendszer identitását.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>Melyik csoporthoz egy fájl vagy könyvtár tulajdonoscsoportjaként van beállítva a létrehozáskor?

A tulajdonoscsoport átmásolja a tulajdonoscsoport a szülőkönyvtár, amelyben az új fájl vagy könyvtár jön létre.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Én vagyok egy fájl tulajdonosa, de nem rendelkezem a szükséges RWX-engedéllyel. Mit tegyek?

A tulajdonos módosíthatja a fájlhoz tartozó engedélyeket, így bármilyen szükséges RWX-engedélyt megadhat saját magának.

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>Miért látok néha GUID azonosítókat az ACL-EK?

Egy GUID lesz látható, ha a bejegyzés felhasználót jelöl, és, hogy a felhasználó már az Azure AD-ben nem létezik. Ez általában akkor történik, ha a felhasználó elhagyta a vállalatot, vagy törölve lett a fiókja az Azure AD-ben. Emellett egyszerű szolgáltatásnevekről és biztonsági csoportok nem rendelkezik egy egyszerű felhasználónév (UPN) azonosíthatja azokat, és így ezeket az OID-attribútum (guid) jelöli.

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>Hogyan állíthatok be ACL-ek megfelelően szolgáltatás egyszerű?

Az egyszerű szolgáltatások hozzáférés-vezérlési listákat ad meg, ha fontos a objektumazonosító (OID), használja a *szolgáltatásnév* a létrehozott alkalmazás regisztráció. Fontos megjegyezni, hogy rendelkezik-e regisztrált alkalmazások külön szolgáltatásnévvel az adott Azure AD-bérlővel. Regisztrált alkalmazások az Azure Portalon látható OID rendelkezik, de a *szolgáltatásnév* rendelkezik egy másik (eltérő) Objektumazonosítóját.

Az Objektumazonosító, amely megfelel az alkalmazás regisztrációját a szolgáltatásnévhez tartozó lekéréséhez használhatja a `az ad sp show` parancsot. Paraméterként adja meg az Alkalmazásazonosítót. Íme egy példa, amely megfelel az alkalmazás regisztrációs azonosítót a szolgáltatásnévhez tartozó Objektumazonosítót beszerzésével 18218b12-1895-43e9-ad80-6e8fc1ea88ce =. Az Azure CLI-ben futtassa a következő parancsot:

`az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
<<OID will be displayed>>`

Ha a helyes Objektumazonosítóját a szolgáltatásnévhez tartozó, nyissa meg a Storage Explorer **hozzáférés kezelése** lap az Objektumazonosító hozzáadását és hozzárendelését az Objektumazonosító a megfelelő engedélyeket. Győződjön meg arról, hogy ki **mentése**.

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>Támogatja a Data Lake Storage Gen2 ACL-ek öröklése?

Az Azure RBAC-hozzárendelések öröklik. Hozzárendelések folyamatot az előfizetés, erőforráscsoport és tárfiók erőforrásainak le a fájl rendszererőforrás.

Hozzáférés-vezérlési listák nem öröklődnek. Azonban alapértelmezett ACL-ek hozzáférés-vezérlési listák beállítása a szülő könyvtára alatt létrehozott fájlok és a gyermek alkönyvtárakat használható. 

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Hol tudhatok meg többet a POSIX hozzáférés-vezérlési modellről?

* [POSIX hozzáférés-vezérlési listák Linux rendszeren](https://www.linux.com/news/posix-acls-linux)
* [HDFS-engedélyek útmutatója](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)
* [POSIX – gyakori kérdések](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [POSIX ACL Ubuntu rendszeren](https://help.ubuntu.com/community/FilePermissionsACLs)
* [Hozzáférés-vezérlési listákat használó ACL Linux rendszeren](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Lásd még

* [Az Azure Data Lake Storage Gen2 áttekintése](../blobs/data-lake-storage-introduction.md)
