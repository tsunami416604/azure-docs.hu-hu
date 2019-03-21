---
title: Az Azure Data Lake Storage Gen2 Storage biztonsági útmutatóját |} A Microsoft Docs
description: A sok védelmének biztosítása az Azure Storage, beleértve többek között az rbac-RÓL és a Storage Service Encryption részletei
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 02/07/2019
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: 22b070e6d70208057c85ad6a2322cc440d12a0fa
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58008210"
---
# <a name="azure-data-lake-storage-gen2-security-guide"></a>Az Azure Data Lake Storage Gen2 biztonsági útmutató

Az Azure Data Lake Storage Gen2 olyan készlete, az Azure Storage-fiókok beépített funkcióinak előnyeit. Mint ilyen ebben a cikkben minden hivatkozást úgy van kialakítva, az Azure Storage-fiókhoz tartozó hierarchikus névtér engedélyezve (Data Lake Storage Gen2 funkciók).

- Azure Storage tárterületre írt összes adat automatikusan titkosítva lesznek használatával [Storage Service Encryption (SSE)](storage-service-encryption.md). További információkért lásd: [bejelentése alapértelmezés titkosításának Azure-Blobok, fájlok, táblák és a Queue Storage](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).
- Az Azure Active Directory (Azure AD) és a szerepköralapú hozzáférés-vezérlés (RBAC) támogatottak az Azure Storage erőforrás-felügyeleti műveletek és a műveletekhez, a következő:
    - Hatóköre a storage-fiók erőforrás felügyeleti műveleteket, mint a kulcskezelés engedélyezésére rendszerbiztonsági tagok és az Azure AD RBAC-szerepkörök rendelhet hozzá.
    - Az Azure AD-integráció az Azure Storage műveletekhez használata támogatott. RBAC-szerepkör hatóköre egy előfizetés, erőforráscsoport, tárfiók vagy egy egyéni fájlrendszer egy rendszerbiztonsági tag vagy egy felügyelt identitás, az Azure-erőforrások rendelhet hozzá. További információkért lásd: [hitelesíti a hozzáférést az Azure Storage, Azure Active Directory használatával](storage-auth-aad.md).
- Az Azure Storage az adatobjektumok való delegált hozzáférés adható használatával [közös hozzáférési aláírások](../storage-dotnet-shared-access-signature-part-1.md).

Ez a cikk az egyes biztonsági funkció, amely használható az Azure Storage áttekintése. Hivatkozások olyan cikkeket, amely az egyes szolgáltatások részletei így könnyedén megteheti a megadott további vizsgálatra minden témában.

Az alábbiakban az ebben a cikkben tárgyalt témaköröket:

* [Felügyeleti sík biztonsági](#management-plane-security) – biztonságossá a Tárfiókhoz

  A felügyeleti sík áll, a storage-fiók kezeléséhez használt erőforrásokat. Ez a szakasz ismerteti az Azure Resource Manager üzemi modell és a szerepköralapú hozzáférés-vezérlés (RBAC) szabályozható a tárfiókok használatával. Címek is kezelése a tárfiók kulcsait és újragenerálásával őket.
* [Adatok biztonsági Adatsík](#data-plane-security) – az adathozzáférés biztonságossá tétele

  Ebben a szakaszban megnézzük, amely hozzáférést biztosít a tényleges adatok objektumok a tárfiókja, azaz a fájlok és könyvtárak, közös hozzáférési aláírások és a tárolt hozzáférési szabályzatok segítségével. Ismertetjük a szolgáltatásszintű SAS és a fiókszintű SAS. Azt is láthatja, hogyan korlátozhatja a hozzáférést egy adott IP-cím (vagy IP-címtartományát), hogy hogyan legyen korlátozva használt HTTPS protokollt és lépésekben vonhatja vissza egy közös hozzáférésű Jogosultságkód lejár, várakozás nélkül.
* [Titkosítás az átvitel során](#encryption-in-transit)

Ez a szakasz bemutatja, hogyan védi az adatait, átvitel során, vagy onnan máshová egy storage-fiókot a Data Lake Storage Gen2 engedélyezve van. Az ajánlott a HTTPS használatával tárgyaljuk.

## <a name="management-plane-security"></a>Felügyeleti sík biztonsági

A felügyeleti sík olyan műveleteket tartalmaz, amelyek befolyásolják a magának a tárfióknak. Például, is létrehozása vagy törölheti a tárfiókokat, tárfiókok listájának lekérése az előfizetéshez, a storage-fiók kulcsainak lekérése vagy a tárfiókkulcsok újragenerálása.

Ez az útmutató a Resource Manager-modellt a központi telepítés, amely az azt jelenti, hogy a storage-fiókok létrehozása a Data Lake Storage Gen2 képességekkel rendelkező összpontosít. A Resource Manager-tárfiókokra helyett a teljes előfizetés, mert így hozzáférést, és szabályozhatja a hozzáférést, a felügyeleti sík szerepköralapú hozzáférés-vezérlés (RBAC) használatával több véges szinten.

### <a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>Hogyan tegye biztonságossá tárfiókját, a szerepköralapú hozzáférés-vezérlés (RBAC)

Beszéljünk RBAC mi, és hogyan használhatja azt. Minden Azure-előfizetés Azure Active Directoryval rendelkezik. Felhasználók, csoportok és alkalmazások tartalmazza a hozzáférési engedélyt kapnak az Azure-előfizetést a Resource Manager-alapú üzemi modellt használó erőforrásainak kezeléséhez. Az ilyen típusú biztonsági szerepkör alapú hozzáférés-vezérlés (RBAC) nevezzük. Ez a hozzáférés kezeléséhez a **hozzáférés-vezérlés (IAM)** az Azure Portalon.

A Resource Manager-modell helyezi a tárfiók egy erőforrás-csoport és hozzáférés a felügyeleti sík, hogy adott storage-fiók az Azure Active Directoryval való. Például adott felhasználóknak biztosíthat hozzáférést a tárfiók kulcsaihoz, más felhasználók megtekintheti a storage-fiók adatait, de nem tudja elérni a tárfiók kulcsait.

#### <a name="granting-access"></a>Hozzáférés biztosítása

A hozzáférést a megfelelő RBAC-szerepkört rendelhet a felhasználók, csoportok és alkalmazások, a megfelelő hatókörben. A teljes előfizetés hozzáférést, rendelje hozzá egy szerepkört az előfizetés szintjén. Is hozzáférést biztosít az összes olyan erőforrást egy erőforráscsoportban magát az erőforráscsoportot számára biztosított engedélyekkel. Egyes szerepkörök is rendelhet adott erőforrásokat, például a storage-fiókok.

Az alábbiakban a fő pontokat kell tudni az RBAC használatával el az Azure Storage-fiók felügyeleti műveleteit:

* Rendelkezik hozzáféréssel a storage-fiókban lévő objektumok számára engedélyt adhat a azokat, olvassa el a tárfiók kulcsaihoz, és, hogy a felhasználó használhatja ezeket a kulcsokat a fájlok és könyvtárak eléréséhez.
* Szerepkörök rendelhetők, egy adott felhasználói fiók, a felhasználók egy csoportjára, vagy egy adott alkalmazást.
* Minden egyes szerepkörhöz műveletek, és nem a műveletek listáját. Például a virtuális gépek Közreműködője szerepkör rendelkezik a "listkeys műveletének" művelettel, amely lehetővé teszi, hogy a tárfiók kulcsaihoz kell olvasni. A közreműködői "Nem műveletek" rendelkezik, például frissítése a felhasználók az Active Directory hozzáférését.
* Storage szerepkörök tartalmazzák (de nem korlátozódnak) a következő szerepkörök:

  * Tulajdonos – ezek mindent felügyelhetnek, beleértve a hozzáférést.
  * Közreműködő – ez nem semmit a tulajdonosi hozzáférés hozzárendelése is kivételével. Ezzel a szerepkörrel rendelkező személyek tekinthetik meg és a tárfiókkulcsok újragenerálása. A tárfiók kulcsaihoz az adatobjektumok elérhetik azt.
  * Olvasó – akkor megtekintheti a titkos kulcsok kivételével a tárfiók adatait. Például ha valaki a storage-fiók olvasói szerepköre rendel, akkor megtekintheti a storage-fiók tulajdonságait, de nem módosítja a tulajdonságokat és a storage-fiók kulcsainak megtekintéséhez.
  * Tárfiók-közreműködő – felügyelheti a storage-fiók – követően elolvashatják az előfizetéshez tartozó erőforráscsoportokat és erőforrásokat, és hozzon létre és kezelheti az előfizetést erőforráscsoportok üzemelő példányainak. A tárfiók kulcsaihoz, amelyek viszont azt jelenti, hozzáférhet az adatsík is eléréséhez.
  * Felhasználói hozzáférés rendszergazdája –, kezelheti a felhasználói hozzáférést a tárfiókhoz. Például akkor is hozzáférést olvasó egy adott felhasználó számára.
  * Virtuális gépek Közreműködője – felügyelheti a virtuális gépek, de nem a tárfiókot, amelyhez csatlakoznak. Ez a szerepkör listázhatja a tárfiók kulcsaihoz, ami azt jelenti, hogy a szerepkör hozzárendelése a felhasználó frissítheti az adatsíkhoz.

    Ahhoz, hogy a felhasználót, hogy hozzon létre egy virtuális gépet azokat kell a megfelelő VHD-fájlt létrehozni a storage-fiókban. Ehhez szükségük fogja tudni lekérni a tárfiók kulcsát, és adja át azt az API-t a virtuális gép létrehozását. Ezért, listázhatja a tárfiók kulcsaihoz, ezzel az engedéllyel kell rendelkezniük.
* Egyéni szerepkörök definiálhat funkciója, amely lehetővé teszi, hogy az elérhető Azure-erőforrásokon végrehajtott műveletek listája a műveletek.
* A felhasználó lehet előtt be kell állítania az Azure Active Directoryban hozzájuk rendelhet egy szerepkört.
* Létrehozhat egy jelentést, akik nyújtott/visszavont milyen típusú hozzáférés és-tárolókról akikkel, és milyen hatókörben, a PowerShell vagy az Azure CLI használatával.

#### <a name="resources"></a>További források

* [Azure Active Directory szerepköralapú hozzáférés-vezérlése](../../role-based-access-control/role-assignments-portal.md)

  Ez a cikk az Azure Active Directory szerepkörön alapuló hozzáférés-vezérlését és annak működési módját ismerteti.
* [RBAC: Beépített szerepkörök](../../role-based-access-control/built-in-roles.md)

  Ez a cikk részletesen összes az RBAC-ben elérhető beépített szerepkört.
* [A Resource Manager-alapú és a klasszikus üzembe helyezés ismertetése](../../azure-resource-manager/resource-manager-deployment-model.md)

### <a name="managing-your-storage-account-keys"></a>A Storage-fiók kulcsok kezelése

A tárfiókkulcsok 512 bites karakterláncok hozott létre Azure-és a tárfiók nevét, például a storage-fiókban tárolt adatok objektumok elérése is használható, -blobokkal, az entitások egy táblát, üzenetsorbeli üzenetek és a egy Azure-beli megosztáson található fájlok. A tárolási fiók kulcsok szabályozza a hozzáférést az adatsík a tárfiókhoz való hozzáférés szabályozása.

Minden tárfióknak a "Kulcs 1" és "2. kulcsba" említett két kulcs van a [az Azure portal](https://portal.azure.com/) és a PowerShell-parancsmagokban. Ezek helyreállíthatja segítségével manuálisan többféle módszer áll rendelkezésre, többek között, de nem csak az egyik a [az Azure portal](https://portal.azure.com/), PowerShell, az Azure CLI- vagy programozott módon, a Storage .NET ügyféloldali kódtár vagy az Azure Storage szolgáltatások segítségével REST API-T.

Nincsenek újragenerálni a tárfiókkulcsokat számtalan.

* Előfordulhat, hogy újragenerálja őket, rendszeres biztonsági okokból.
* A tárfiók kulcsait szeretne generálni, ha valaki felügyelt alkalmazás feltörni és lekérni a kulcsot, szoftveresen kötött vagy egy konfigurációs fájlba menti a tárfiókhoz való teljes hozzáférés engedélyezése.
* A kulcs újragenerálása egy másik eset, ha a csapata használ a Storage Explorer alkalmazás, amely megtartja a tárfiók kulcsát, és a csapat tagjainak egyike. Az alkalmazás továbbra is működik, hozzáférést ad nekik a tárfiókhoz után szűnt zajlik. Hozza létre őket fiókszintű közös hozzáférési aláírások elsődleges emiatt ténylegesen – használhatja a fiókszintű SAS helyett a hozzáférési kulcsok tárolásához egy konfigurációs fájlban.

#### <a name="key-regeneration-plan"></a>Kulcs újragenerálása terv

Nem szeretne csak újragenerálja a kulcsot, néhány tervezés nélkül használja. Ha így tesz, akkor tárfiók, amely jelentős zavart okozhat levágja sikerült elérését. Ezért két kulcs létezi. Újból létre kell hoznia egy kulcsot egyszerre.

Előtt hozza létre újra a kulcsokat, lehet, hogy az összes függő a storage-fiók alkalmazás, valamint más szolgáltatásokat használ az Azure-ban listáját. Például az Azure Media Services a tárfiók függő használja, ha meg kell a hívóbetűt a media Services-után újragenerálja a kulcsot. Ha minden olyan alkalmazások, például a storage Explorert használja, szüksége lesz a ezeket az alkalmazásokat, valamint az új kulcsokkal. Ha virtuális gépeket, amelynek VHD-fájlokat a storage-fiókban vannak tárolva, azokat nem érinti a storage-fiók kulcsainak újragenerálása.

A kulcsok az Azure Portal segítségével generálja újra. Kulcsok újbóli, miután azok tárolási szolgáltatások közötti szinkronizálását akár 10 percet is igénybe vehet.

Amikor készen áll, Íme az általános folyamat részletező, hogyan kell módosítani a kulcsot. Ebben az esetben a feltételezi, hogy jelenleg használt kulcs 1, és szeretné módosítani, minden kulcs 2 használja helyette.

1. Újragenerálja a kulcsot a 2. Győződjön meg arról, hogy biztonságos. Ez az Azure Portalon teheti meg.
2. Mind a tárfiók kulcsát tároló alkalmazások módosítsa a tárfiók-kulcsot az új kulcs a 2 értéket használja. Tesztelése, és tegye közzé az alkalmazást.
3. Ha minden, az alkalmazások és szolgáltatások működik, és sikeresen fut, 1. kulcs újragenerálása. Ez biztosítja, hogy bárki, akinek meg nem kifejezetten adott az új kulcs többé nem lesz a tárfiókhoz való hozzáférést.

Ha 2 kulcs jelenleg használ, ugyanazzal az eljárással, de fordított a fájlneveket.

Minden alkalmazást, hogy használja az új kulcs módosítása, és közzéteheti keresztül néhány nap alatt, áttelepítheti. Ezek mindegyike minden kész, miután kell majd lépjen vissza és generálja újra a régi kulcsot, annak érdekében, hogy már nem.

Egy másik lehetőség az, hogy a tárfiók kulcsát helyezni egy [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) egy titkos kulcsot, és rendelkezik az alkalmazások a kulcs lekérése onnan. Ezután amikor újragenerálja a kulcsot, és frissítse az Azure Key Vault, a kérelmek nem kell kell helyeznie, mert azok kiesik az új kulcsot az Azure Key vault automatikusan. Olvassa el a kulcsot minden alkalommal, amikor szüksége lesz rá az alkalmazás is van, vagy azt gyorsítótárazza a memóriában, és ha ez nem sikerül, használatakor lekérni a kulcsot újra az Azure Key vault.

Az Azure Key Vaulttal hozzáadja a tárelérési kulcsok biztonsági egy másik szint is. Ezt a módszert használja, ha soha nem kell a storage-kulcs szoftveresen kötött a konfigurációs fájlban, amely eltávolítja a sérülésre valaki hozzáférni az adott engedély nélkül a kulcsokat a.

Az Azure Key Vault használatának másik előnye is szabályozhatja a hozzáférést a kulcsokhoz, Azure Active Directory használatával. Ez azt jelenti, hogy is hozzáférést biztosít az alkalmazások, amelyeket az Azure Key Vaulttal a kulcsok lekéréséhez, és tudja, hogy más alkalmazások nem tudják a hívóbetűk adva nekik kifejezetten engedély nélkül kell néhány.

> [!NOTE]
> A Microsoft javasolja, hogy egyszerre az összes alkalmazás használata csak az egyik a kulcsokat. Az egyes helyek kulcs 1 és mások a kulcs 2 használja, ha nem lesz képes a kulcsok rotálására néhány alkalmazás-hozzáférés elvesztése nélkül.

#### <a name="resources"></a>További források

* [Az Azure Portal tárfiók-beállítások kezelése](storage-account-manage.md)
* [Az Azure Storage erőforrás-szolgáltató REST API-ja – referencia](https://msdn.microsoft.com/library/mt163683.aspx)

## <a name="data-plane-security"></a>Adatsík Adatbiztonság
Adatsík adatbiztonság hivatkozik az Azure Storage-ban tárolt objektumok védelmére szolgáló módszerek. Úgy tapasztaltuk módszerek az adatok átvitel során az adatokat és biztonsági titkosításához, de hogyan továbblép hozzáféréssel az objektumokhoz szabályozásával kapcsolatban?

Három lehetőség közül választhat, amelyek engedélyezik a hozzáférést az adatobjektumokhoz, az Azure Storage, többek között:

- Az Azure AD-vel történő hitelesítéséhez fájlrendszereit és üzenetsorokhoz való hozzáférést. Az Azure AD engedélyezési, beleértve a kód a titkos kulcsok tárolására szükségtelenné más módjaival előnyöket biztosít. További információkért lásd: [hitelesíti a hozzáférést az Azure Storage, Azure Active Directory használatával](storage-auth-aad.md). 
- A tárfiókkulcsok-vel történő elérését megosztott kulcsos hitelesítéséhez. A storage-fiók kulcsok tárolására az alkalmazás, a Microsoft azt javasolja, hogy inkább az Azure AD, ha lehetséges keresztül megosztott kulcsos engedélyezése szükséges. Az éles környezetben, vagy Azure-táblák és fájlok való hozzáférésre folytassa a megosztott kulcs használatával, amíg az Azure AD-integráció előzetes verzióként.
- Közös hozzáférési aláírások használatával bizonyos objektumokhoz szabályzott engedélyekkel az egy adott időtartam megadását.

Engedélyezési keresztüli hozzáférés korlátozása, mellett is használhatja [tűzfalak és virtuális hálózatok](storage-network-security.md) hálózati szabályok alapján a tárfiókhoz való hozzáférés korlátozásához.  Ez a megközelítés lehetővé teszi, hogy megtagadja a hozzáférést a nyilvános internetes forgalmat, és adja meg hozzáférési csak meghatározott Azure virtuális hálózatok vagy nyilvános IP-címtartományok.

### <a name="storage-account-keys"></a>Tárfiókkulcsok

A tárfiókkulcsok 512 bites karakterláncokat, a storage-fiók nevével együtt használható a storage-fiókban tárolt adatok objektumok elérése az Azure által létrehozott.

Ha például fájlok olvashat. Ezek a műveletek számos elvégezhető az Azure Portalon vagy a Storage Explorer számos alkalmazás használatával. Ezek a műveletek végrehajtásához használja a REST API-kódot is írhat.

Az a szakaszban leírt módon a [felügyeleti sík biztonsági](#management-plane-security), a tárelérési kulcsok elérhető szerepköralapú hozzáférés-vezérlés (RBAC) szabályozható az Azure Resource Manager modellt használó tárfiókot.

### <a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>Hogyan delegálása közös hozzáférési aláírások és a tárolt hozzáférési szabályzatok segítségével-fiókjában található objektumok elérése

Egy közös hozzáférésű Jogosultságkód: egy URI, amely lehetővé teszi, hogy a tárolási objektum való hozzáférés delegálására, és adja meg például az engedélyeket és a dátum/idő tartomány hozzáférési korlátozások csatolható biztonsági jogkivonatot tartalmazó karakterlánc.

Is hozzáférést biztosít a fájlok vagy könyvtárak.

Egy ügyfél egy SAS-token, képek feltöltése a Blob Storage-ban egy fájlrendszert használja, és adjon meg egy webes alkalmazás olvasásához szükséges engedély a képek nem ad. Mindkét esetben a kockázatok elkülönítésének van – minden alkalmazáshoz is hozzáférést kell biztosítani csak a feladataik elvégzéséhez szükséges. Ez a közös hozzáférési aláírások használatával lehetséges.

#### <a name="why-you-want-to-use-shared-access-signatures"></a>Mi indokolhatja közös hozzáférési aláírások használata

Indokolhatja a beállítás csak a tárfiók kulcsára, ami sokkal egyszerűbb bármiféle helyett SAS használandó? A tárfiók kulcsának bármiféle olyan, mint a storage Királyság kulcsainak megosztása. Teljes hozzáférést van. Valaki esetleg használja a kulcsok és azok teljes Zenetár feltölteni a tárfiókba. Ezek sikerült is a fájlok cserélje vírusos verziók, vagy az adatok ellopására. A tárfiók azonnal korlátlan hozzáférés megadását is olyasmi, nem kell venni enyhén.

Közös hozzáférésű jogosultságkódokkal biztosíthat egy ügyfél csak korlátozott mennyiségű időt szükséges engedélyekkel. Például ha valaki van a fájl feltöltése a fiókjába is hozzáférést biztosít számukra írási elegendő időt kell töltenie a fájlt (attól függően, a fájl mérete természetesen) számára. És Ha meggondolja magát, visszavonhatja a hozzáférést.

Ezenkívül azt is megadhatja, hogy SAS használatával kérelmek egy adott IP-cím vagy IP-címtartományt az Azure-bA külső korlátozódnak. Is megkövetelheti, hogy kérések egy adott protokollt (HTTPS vagy HTTP/HTTPS) használatával. Ez azt jelenti, ha csak szeretné lehetővé tenni a HTTPS-forgalom számára, beállíthatja a szükséges protokoll csak HTTPS és HTTP-forgalom le lesz tiltva.

#### <a name="definition-of-a-shared-access-signature"></a>Közös hozzáférésű Jogosultságkód meghatározása

Egy közös hozzáférésű Jogosultságkód olyan lekérdezési paraméterek hozzáfűzésével mutat az erőforrás URL-címre

amely ismerteti a hozzáférés engedélyezett, és mennyi ideig, amelyhez a hozzáférés engedélyezett. Íme egy példa; Ez az URI blob írásvédett hozzáférést biztosít az öt perc alatt. SAS lekérdezési paraméterek lehet URL-kódolású, például a % 3A a kettőspont (:) vagy szóközzel 20 %.

```
http://mystorage.dfs.core.windows.net/myfilesystem/myfile.txt (URL to the file)
?sv=2015-04-05 (storage service version)
&st=2015-12-10T22%3A18%3A26Z (start time, in UTC time and URL encoded)
&se=2015-12-10T22%3A23%3A26Z (end time, in UTC time and URL encoded)
&sr=b (resource is a blob)
&sp=r (read access)
&sip=168.1.5.60-168.1.5.70 (requests can only come from this range of IP addresses)
&spr=https (only allow HTTPS requests)
&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D (signature used for the authentication of the SAS)
```

#### <a name="how-the-shared-access-signature-is-authorized-by-the-azure-storage-service"></a>Hogyan a közös hozzáférésű Jogosultságkód engedélyezve van az Azure Storage szolgáltatás

A storage szolgáltatás a kérelmet kap, amikor a bemeneti lekérdezési paramétereket fogadja, és létrehoz egy ugyanazzal a módszerrel, a hívó program aláírást. Ezután a két aláírások hasonlítja össze. Ha az engedélyt megadják, majd a storage szolgáltatás ellenőrizheti a storage szolgáltatás verzió róla, hogy érvényes, ellenőrizze, hogy az aktuális dátumot és időt a megadott időszakon belül, ellenőrizze, hogy a hozzáférést a kért felel meg a kezdeményezett kérelem stb.

Például a fenti URL-Ha az URL-cím helyett egy blob, fájl lett mutató a kérelem sikertelen lesz, mert ez azt jelenti, hogy a közös hozzáférésű Jogosultságkód egy BLOB. Ha a hívott REST-parancs sikeresen frissíteni a blob, meghiúsul, mert a közös hozzáférésű Jogosultságkód Megadja, hogy a csak olvasási hozzáférést számára engedélyezett-e.

#### <a name="types-of-shared-access-signatures"></a>Közös hozzáférésű Jogosultságkódok típusai

* Egy szolgáltatásszintű SAS egy storage-fiókot az adott erőforrások eléréséhez használható. Néhány példa erre egy fájlrendszer a fájlok listájának beolvasása, vagy a fájl letöltése.
* Egy fiókszintű SAS, amelyeket egy szolgáltatásszintű SAS használható eléréséhez használható. Ezenkívül adhat a beállítások olyan szolgáltatásszintű SAS, például fájlrendszerekre létrehozása nem engedélyezettek.

#### <a name="creating-a-sas-uri"></a>SAS URI létrehozása

1. Hozhat létre egy URI-t igény szerinti, minden alkalommal, amikor meghatározása a lekérdezési paraméterek mindegyikét.

   Ez a megközelítés akkor rugalmas, de ha egy olyan logikai készlete, amelyek hasonló minden alkalommal, amikor paraméterek, egy tárolt hozzáférési szabályzat segítségével jobb képet.
2. Létrehozhat egy tárolt hozzáférési szabályzatot egy teljes fájlrendszer, megosztott fájl, tábla vagy üzenetsor. Ezután ezzel alapjaként a létrehozott SAS URI-k esetében. A tárolt hozzáférési szabályzatok alapján engedélyek könnyedén visszavonhatja. Minden egyes filesystem, várólista, tábla vagy fájlmegosztás definiált legfeljebb öt házirendek is rendelkezhet.

   Például ha akkor is rendelkezik egy adott fájlrendszer a blobok olvasási sokan, létrehozhat egy tárolt hozzáférési szabályzatot, amely szerint a "olvasási hozzáférést biztosíthat" és a további beállításokat, amelyek azonos lesz minden alkalommal, amikor. Ezután létrehozhat egy SAS URI-t a tárolt hozzáférési szabályzat beállításainak használatával, és adja meg a lejárati dátum/idő. Ennek előnye, hogy nem kell minden alkalommal adja meg az összes a lekérdezési paramétereket.

#### <a name="revocation"></a>Visszavont tanúsítványok

Tegyük fel, hogy a SAS biztonsága sérült, vagy szeretné módosítani, vállalati biztonsági és jogszabályi követelmények miatt. Hogyan visszavonja a SAS használatával erőforrásokhoz való hozzáférést? Attól függ, hogyan hozott létre az SAS URI-t.

Ha az ad-hoc URI-kat használja, három lehetősége van. SAS-jogkivonatokat kibocsátani, rövid lejárati szabályzatokkal, és várjon, amíg lejár az SAS. Nevezze át vagy törölje az erőforrást (feltéve, hogy a jogkivonat hatóköre egyetlen objektum lett) is. A tárfiók kulcsaihoz módosíthatja. Ez a beállítás utolsó lehet jelentős hatást, attól függően, hogyan számos szolgáltatás használ a tárfiók, és valószínűleg nem valami néhány tervezés nélkül szeretné.

Egy tárolt hozzáférési szabályzat származó SAS használ, eltávolíthatja a hozzáférés visszavonása a tárolt hozzáférési szabályzat által – csak módosíthatja, hogy már lejárt, vagy teljesen távolíthatja el. Ez azonnal érvénybe lép, és minden a tárolt hozzáférési szabályzat használatával létrehozott SAS érvényteleníti. Frissítése vagy eltávolítása a tárolt hozzáférési szabályzat előfordulhat, hogy hatás felhasználó, hogy adott fájlrendszer, a fájlmegosztást, tábla vagy üzenetsor SAS-n keresztül, de ha az ügyfelek számára készültek, így a régit érvénytelenné, amikor egy új SAS kérnek a címkevezérlőt.

Mivel egy tárolt hozzáférési szabályzat származó SAS használatával lehetővé teszi, hogy a SAS azonnal visszavonása, az ajánlott mindig használja a tárolt hozzáférési szabályzatok lehetőség.

#### <a name="resources"></a>További források

További információt a közös hozzáférési aláírások és a tárolt hozzáférési szabályzatok, példák, teljes körű használatához tekintse meg a következő cikkeket:

* Ezek azok a referencia-cikkek.

  * [Service SAS](https://msdn.microsoft.com/library/dn140256.aspx)

    Ez a cikk példákat blobok, az üzenetsorbeli üzenetek számára, a tábla tartományokkal és a fájlok szolgáltatásszintű SAS használatával.
  * [A szolgáltatásalapú SAS létrehozása](https://msdn.microsoft.com/library/dn140255.aspx)
  * [Egy fiók SAS létrehozása](https://msdn.microsoft.com/library/mt584140.aspx)
* Ezek a közös hozzáférési aláírások és a tárolt hozzáférési szabályzatok létrehozása a .NET ügyféloldali kódtár használatával kapcsolatos.

  * [Közös hozzáférésű Jogosultságkódok (SAS) használata](../storage-dotnet-shared-access-signature-part-1.md)
  * [A közös hozzáférésű Jogosultságkódot, 2. rész: Hozzon létre, és használhatja az SAS-a Blob Service-szel](../blobs/storage-dotnet-shared-access-signature-part-2.md)

    Ez a cikk a SAS-modell példák a közös hozzáférésű Jogosultságkódokat egy leírását tartalmazza, és az ajánlott eljárás javaslatok SAS használja. Azt is ismertetjük, a megadott engedélyeket visszavonását.

* Authentication

  * [Az Azure Storage szolgáltatásainak hitelesítése](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* A közös hozzáférésű Jogosultságkódot első lépéseit ismertető Oktatóanyaga

  * [SAS első lépéseket ismertető oktatóanyag](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

## <a name="encryption-in-transit"></a>Titkosítás az átvitel során

### <a name="transport-level-encryption--using-https"></a>Átviteli szintű titkosítást – HTTPS-en keresztül

Egy újabb lépést kell tennie az Azure Storage-adatok védelme érdekében, hogy az ügyfél és az Azure Storage között az adatok titkosításához. Az első javaslat, hogy mindig a [HTTPS](https://en.wikipedia.org/wiki/HTTPS) protokoll, amely biztosítja, hogy a biztonságos kommunikáció a nyilvános interneten keresztül.

Ahhoz, hogy biztonságos kommunikációs csatornát, mindig használjon HTTPS, ha a REST API-k hívása, vagy fér hozzá az objektumok tárolására. Ezenkívül **közös hozzáférési aláírások**, delegálása az Azure Storage-objektumokhoz való hozzáférést, adja meg, hogy csak a HTTPS protokollt használja, közös hozzáférésű Jogosultságkódokat, biztosítva, hogy bárki küldése használatakor lehetőséget is használható meg hivatkozásokat SAS-tokeneket a megfelelő protokollt fogja használni.

Ha engedélyezi a storage-fiókok a REST API-k hívása objektumok betartatásával módosíthatja a HTTPS használata [biztonságos átvitelre van szükség](../storage-require-secure-transfer.md) a tárfiókhoz. Ha ez engedélyezve van a kapcsolat HTTP-n keresztül program elutasítja.

## <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban

### <a name="storage-service-encryption-sse"></a>Storage Service Encryption (SSE)

Az SSE engedélyezve van az összes storage-fiók, és nem tiltható le. Az SSE automatikusan titkosítja az adatokat, azt az Azure Storage írásakor. Adatok olvasása az Azure Storage-ból, akkor visszaadná az Azure Storage által visszaadott előtt. Az SSE lehetővé teszi az adatok védelme a kód módosítása, vagy adja hozzá a kódot és más alkalmazások nélkül.

A Microsoft által felügyelt kulcsok vagy saját egyéni kulcsokat használhat. A Microsoft felügyelt kulcsokat hoz létre, és azok biztonságos tárolását, valamint azok rendszeres forgatását, belső Microsoft-szabályzat alapján kezeli. Egyéni kulcsok használatával kapcsolatos további információkért lásd: [ügyfél által kezelt kulcsok használata az Azure Key Vaultban a Storage Service Encryption](storage-service-encryption-customer-managed-keys.md).
