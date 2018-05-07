---
title: Az Azure Storage biztonsági útmutatója |} Microsoft Docs
description: A sok védelmének biztosítása az Azure Storage, beleértve többek között a Szerepalapú, Storage szolgáltatás titkosítási, ügyféloldali titkosítás, az SMB 3.0-s és Azure Disk Encryption adatokat.
services: storage
author: craigshoemaker
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/06/2018
ms.author: cshoe
ms.openlocfilehash: 4145f7edb93801aa6f98df7e9cff34ae7370fc52
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
---
# <a name="azure-storage-security-guide"></a>Az Azure Storage biztonsági útmutató

## <a name="overview"></a>Áttekintés

Az Azure Storage biztonsági képességeket, amelyek együtt lehetővé teszik a fejlesztők számára a biztonságos alkalmazások széles választékát nyújtja:

- Azure Storage írt összes adat automatikusan titkosítva van, használatával [Storage Service Encryption (SSE)](storage-service-encryption.md). További információkért lásd: [bejelentése alapértelmezett titkosítás Azure BLOB, fájlok, Table és Queue Storage](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).
- A tárfiók magát a szerepköralapú hozzáférés-vezérlés és az Azure Active Directory használatával kell biztonságossá. 
- Adatok védve legyenek az alkalmazás és az Azure közötti átvitel során használatával [ügyféloldali titkosítás](../storage-client-side-encryption.md), HTTPS és SMB 3.0-s.  
- Az Azure virtuális gépek által használt operációsrendszer- és adatlemezek segítségével titkosíthatók [Azure Disk Encryption](../../security/azure-security-disk-encryption.md). 
- Meghatalmazott hozzáférést biztosít az adatok objektumok az Azure Storage használatával engedélyezhetők [megosztott hozzáférési aláírásokkal](../storage-dotnet-shared-access-signature-part-1.md).

Ez a cikk áttekintést nyújt az egyes szolgáltatások az Azure Storage használható. A hivatkozások akkor megadott cikket, amely az egyes szolgáltatások részleteit, egyszerűen tegye további vizsgálatra minden témakör.

Az alábbiakban a témakörök Ez a cikk tárgyalja:

* [Felügyeleti Vezérlősík biztonsági](#management-plane-security) – a Tárfiók védelmének biztosítása

  A felügyeleti vezérlősík a tárfiók kezelésére szolgáló erőforrások áll. Ez a fejezet az Azure Resource Manager telepítési modell és a szerepköralapú hozzáférés-vezérlést (RBAC) használata a storage-fiókok való hozzáférés vezérlése érdekében. Is javítja a tárfiókok kulcsait, és hogyan generálja újra őket kezelése.
* [Adatok biztonsági sík](#data-plane-security) – az adatok hozzáférésének biztonságossá tétele

  Ebben a szakaszban megnézzük, amely hozzáférést biztosít a tényleges adatok objektumok tárfiókba blobok, fájlok, üzenetsorokat és táblákat, például megosztott hozzáférési aláírásokkal és hozzáférési házirendek tárolja. Bemutatjuk a szolgáltatásiszint-SAS és a fiók szintű SAS. Azt is megtudhatja, hogyan korlátozhatja az egy adott IP-cím (vagy az IP-címek), hogyan legyen korlátozva a HTTPS protokoll és egy közös hozzáférésű Jogosultságkód visszavonása lejár való várakozás nélkül.
* [Titkosítás az átvitel során](#encryption-in-transit)

  Ez a szakasz tárgyalja biztosítani az adatokat, ha azt át virtuális gépbe vagy onnan Azure Storage. Ajánlott a HTTPS és a titkosítás, az Azure fájlmegosztások SMB 3.0 által használt használatáról lesz döntésről. Most elindítjuk ügyféloldali titkosítás, mely lehetővé teszi az adatok titkosítását az ügyfélalkalmazás továbbított a tárolóba és az adatok visszafejtéséhez után tárolási kivitt egy pillantást is.
* [Titkosítás inaktív állapotban](#encryption-at-rest)

  Az előadás kapcsolatos Storage szolgáltatás titkosítási (SSE), amely automatikusan engedélyezve van az új és meglévő tárfiókokat. Hogyan használja az Azure Disk Encryption, és megismerkedhet az alapvető különbséget és az adatok titkosítása és SSE ügyféloldali titkosítás és esetek is fog keresni. Röviden: a FIPS előírásainak való megfelelést az USA fog keresni Kormánya számítógépek.
* Használatával [tárolási analitika](#storage-analytics) az Azure Storage-hozzáférés naplózása

  Ez a szakasz ismerteti a storage analytics naplók megkeresése a kérelmek. Azt bemutatjuk vessen egy pillantást valós tárolási analitika naplóadatokat, lásd: how to megfejteni a behatolók, hogy a kérelem a Tárfiók hívóbetűjét, megosztott hozzáférési aláírással rendelkező vagy névtelenül, illetve hogy az sikeres vagy sikertelen volt.
* [CORS használatával Fiókértékek ügyfelek engedélyezése](#Cross-Origin-Resource-Sharing-CORS)

  Ez a szakasz beszél hogyan teszi lehetővé az eltérő eredetű erőforrások megosztása (CORS). Lesz döntésről bővebben a tartományok közötti elérést, és hogyan kezelje az Azure Storage épített CORS funkciókkal.

## <a name="management-plane-security"></a>Felügyeleti Vezérlősík biztonsága
A felügyeleti vezérlősík-műveletek hatása a tárfiók maga áll. Például akkor is hozzon létre vagy tárfiók törlése, storage-fiókok listájának beolvasása egy előfizetésben, lekérni a tárfiók kulcsait vagy újragenerálni a tárfiókkulcsokat.

Amikor létrehoz egy új tárfiókot, akkor klasszikus és Resource Manager telepítési modell kiválasztása. Az Azure-erőforrások létrehozása a Klasszikus modell csak mindent hozzáférés az előfizetéshez, és a tárfiók viszont lehetővé teszi.

Ez az útmutató a Resource Manager modellt, amely az ajánlott módszert storage-fiókok létrehozására összpontosít. Az erőforrás-kezelő storage-fiókok, nem pedig az egész előfizetésre amely elérésére, és szabályozhatja a hozzáférést a szerepköralapú hozzáférés-vezérlést (RBAC) használata felügyeleti és több véges szinten.

### <a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>A storage-fiókkal, és a szerepköralapú hozzáférés-vezérlést (RBAC) biztonságossá tétele
Most szolgáltatással kapcsolatban RBAC van, és hogyan használhatja azt. Minden Azure-előfizetés Azure Active Directoryval rendelkezik. Felhasználók, csoportok és alkalmazások tartalmazza a hozzáférési engedélyt kapnak az Azure-előfizetés a Resource Manager üzembe helyezési modellel használó erőforrások kezelésére. Az ilyen típusú biztonsági szerepköralapú hozzáférés-vezérlést (RBAC) hivatkozunk. A hozzáférés kezeléséhez használja a [Azure-portálon](https://portal.azure.com/), a [Azure CLI-eszközei](../../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs), vagy a [Azure Storage erőforrás szolgáltató REST API-k](https://msdn.microsoft.com/library/azure/mt163683.aspx).

A Resource Manager modellt helyezett a tárfiók egy erőforrás-csoport és a vezérlés hozzáférés és azon az Azure Active Directoryval bizonyos tárolási fiók kezelése. Például biztosíthat bizonyos felhasználók a tárfiókok kulcsait, elérését, amíg más felhasználók megtekinthetik a tárfiók adatait, de nem tud hozzáférni a tárfiók kulcsait.

#### <a name="granting-access"></a>Hozzáférés biztosítása
Hozzáférés a megfelelő RBAC szerepkört rendel a felhasználók, csoportok és alkalmazások, a megfelelő hatókörben. Az egész előfizetésre való hozzáférés biztosításához rendelhet hozzá egy szerepkört az előfizetés szintjén. Engedélyek megadása az erőforráscsoporthoz, maga szerint is engedélyezheti a hozzáférést az összes erőforrást erőforráscsoportban. Konkrét erőforrásokat, például a storage-fiókok rendelhet szerepköröket is.

Az alábbiakban a fő pontokat, meg kell ismernie az RBAC használata a kezelési műveletek, az Azure Storage-fiók eléréséhez:

* Ha hozzáférés hozzárendelése alapvetően rendeljen hozzá egy szerepkört a fiók rendelkezik hozzáféréssel kívánt. Szabályozhatja a hozzáférést a tárolási fiók kezeléséhez használt műveleteket, de nem a data objects a fiókban. Például engedélyt adhat beolvasni a tulajdonságokat a tárfiók (például a redundancia érdekében), de nem a tároló vagy egy belül a Blob Storage tárolóban lévő adatok.
* Mások számára, hogy rendelkezik hozzáféréssel a data objects a tárfiókban lévő őket engedélyt adhat a tárfiókkulcsokat olvasni, és, hogy a felhasználó használhatja ezeknek a kulcsoknak a BLOB, a várólisták, a táblák és a fájlok eléréséhez.
* Szerepkörök rendelhetők hozzá egy adott felhasználói fiók, a felhasználók egy csoportját, vagy egy adott alkalmazáshoz.
* Minden szerepkörhöz műveletek és a nem műveletek listáját. Például a virtuális gép közreműködő szerepkörrel rendelkezik "listKeys" művelet, amely lehetővé teszi, hogy a tárfiók kulcsait kell olvasni. A közreműködői "Nem műveletek" rendelkezik, ilyen például az Active Directory felhasználók hozzáférésének frissítés.
* Tárolási szerepkörei tartalmazzák (azonban nem csak) a következő szerepkörök:

  * Tulajdonos – azok mindent felügyelhetnek, beleértve a hozzáférést.
  * Közreműködő – azok is végrehajthat a tulajdonos is kivéve hozzáférés hozzárendelése. Ezzel a szerepkörrel rendelkező bármely személy megtekintheti, és a tárfiókkulcsok újragenerálása. A tárfiókok kulcsait, az adatok objektumok eléréséhez.
  * Olvasó – akkor megtekintheti a titkos kulcsok kivételével a tárfiók adatait. Például ha valaki egy olvasási engedéllyel a tárfiók szerepkört rendel, akkor megtekintheti a storage-fiók tulajdonságait, de nem módosítja a tulajdonságokat és a tárfiók kulcsait megtekintése.
  * Tárolási fiók közreműködői – kezelésére a tárfiók – elolvasása az előfizetés erőforráscsoportok és erőforrásokat, és hozzon létre és előfizetés erőforrás csoport központi telepítések felügyeletéhez szükséges. A tárfiókok kulcsait, ami viszont azt jelenti, hogy az adatok vezérlősík eléréséhez is eléréséhez.
  * Felhasználói hozzáférés adminisztrátora – azok is felügyelheti a felhasználók hozzáférését a tárfiókhoz. Például hogy hozzáférést biztosíthat olvasó egy adott felhasználó.
  * Virtuális gép közreműködő – kezelésére virtuális gépek, de nem a tárfiókot, amelyhez csatlakoznak. Ez a szerepkör is listázhatja a tárfiókok kulcsait, ami azt jelenti, hogy a felhasználó, akinek a szerepkör hozzárendelése frissítheti az adatok vezérlősík.

    Ahhoz, hogy a felhasználót, hogy a virtuális gép létrehozása rendelkeznek a megfelelő VHD-fájlt létrehozni a tárfiók. Ehhez kell tudni lekérni a tárfiók hívóbetűjét, és adja át az API-t a virtuális gép létrehozása. Ezért ezek is listázhatja a tárfiókkulcsokat, ezzel az engedéllyel kell rendelkezniük.
* Egyéni szerepkörök definiálása nem olyan funkció, amely lehetővé teszi a különböző műveleteket listájából elérhető Azure-erőforrások a végrehajtható műveletek összeállításához.
* A felhasználó kell előtt be kell állítania az Azure Active Directoryban egy szerepkör rendelhet hozzájuk.
* Ki megadott vagy visszavont milyen típusú hozzáférést és a akinek, és milyen hatókörben, a PowerShell vagy az Azure parancssori felület jelentést hozhat létre.

#### <a name="resources"></a>További források
* [Azure Active Directory szerepköralapú hozzáférés-vezérlése](../../role-based-access-control/role-assignments-portal.md)

  Ez a cikk az Azure Active Directory szerepkörön alapuló hozzáférés-vezérlését és annak működési módját ismerteti.
* [RBAC: Beépített szerepkörök](../../role-based-access-control/built-in-roles.md)

  Ez a cikk részletezi az összes elérhető RBAC beépített szerepkör.
* [A Resource Manager-alapú és a klasszikus üzembe helyezés ismertetése](../../azure-resource-manager/resource-manager-deployment-model.md)

  Ez a cikk ismerteti a Resource Manager telepítés és a klasszikus üzembe helyezési modellel, és a Resource Manager és az erőforrás-csoportok használata előnyeinek bemutatása. A Resource Manager modellben az Azure számítási, hálózati és tárolási szolgáltatók működése ismerteti.
* [Szerepköralapú hozzáférés-vezérlés kezelése REST API-val](../../role-based-access-control/role-assignments-rest.md)

  Ez a cikk leírja, hogyan használható a REST API az RBAC kezeléséhez.
* [Az Azure Storage erőforrás szolgáltató REST API-referencia](https://msdn.microsoft.com/library/azure/mt163683.aspx)

  Az API-referencia az API-k segítségével a tárfiók kezelése programozott módon ismerteti.
* [A hitelesítés az Azure Resource Manager API-JÁVAL fejlesztői útmutató](http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/)

  Ez a cikk bemutatja, hogyan hitelesítheti a Resource Manager API-k használatával.
* [Szerepköralapú hozzáférés-vezérlés az Ignite-tól a Microsoft Azure számára](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

  Ez a hivatkozás a Channel 9 2015-ös MS Ignite-konferencia videójára mutat. Ebben a részben arról beszélnek, milyen hozzáférés-kezelési és jelentési képességeket nyújt az Azure, és bemutatják az Azure-előfizetés hozzáférés-biztosításának legjobb gyakorlatait az Azure Active Directory használatával.

### <a name="managing-your-storage-account-keys"></a>A Tárfiók kulcsait kezelése
Tárfiókkulcsok olyan Azure használható, a tárfiók nevét, valamint az adatok objektumokhoz, például a tárfiókban tárolt, blobok, egy táblát, üzenetsor-üzeneteket és Azure File megosztáson belüli által létrehozott 512 bites karakterláncok. A tárolási fiók kulcsok szabályozza a hozzáférést az adatok vezérlősík tárolási fiók hozzáférés szabályozása.

Minden tárfiók néven "1. kulcs" és "Kulcs 2" a két kulcs van a [Azure-portálon](http://portal.azure.com/) és a PowerShell-parancsmagokkal. Ezek helyreállíthatja segítségével többféle módszer, beleértve, de nem kizárólagosan használatával manuálisan a [Azure-portálon](https://portal.azure.com/), PowerShell, az Azure CLI vagy programozott módon, a .NET a Storage ügyféloldali kódtára vagy az Azure Storage szolgáltatások REST API felülete.

Tetszőleges számú újragenerálni a tárfiókkulcsokat okok miatt van.

* Előfordulhat, hogy generálja újra őket rendszeresen biztonsági okokból.
* A tárfiók kulcsait volna generálja újra, ha valaki felügyelt ellophatja egy alkalmazásba, és lekérni a kulcsot, szoftveresen kötött vagy menteni a konfigurációs fájlban adjon teljes hozzáférést a tárfiókhoz.
* Egy másik eset a kulcs újragenerálása, ha a csapatával használ egy Tártallózó alkalmazást, amely megőrzi a tárfiók hívóbetűjét, és egy csoport tagja. Az alkalmazás akkor is működik, hozzáférést kaphatnak a tárfiók után már nem fontosságúak. Ez oka ténylegesen az elsődleges fiók szintű megosztott hozzáférési aláírásokkal hozza létre őket – használhat egy fiók szintű SAS helyett a tárelérési kulcsok tárolása egy konfigurációs fájlban.

#### <a name="key-regeneration-plan"></a>Kulcs újragenerálása terv
Nem kívánja most újragenerálja a kulcsot, néhány tervezés nélkül használ. Ha így tesz, akkor a tárolási fiók, amely súlyos problémákat okozhat levágási sikerült minden hozzáférés. Ezért két kulcs van. Egyszerre csak egy kulcs kell generálni.

Mielőtt újragenerálja a kulcsokat, mindenképpen az alkalmazásokat, amelyek a tárfiók függenek, valamint használ az Azure-szolgáltatások listáját. Például a tárfiók függő Azure Media Services használ, ha meg kell újraszinkronizálásra a tárelérési kulcsokat a médiaszolgáltatással után újragenerálja a kulcsot. Az alkalmazásokat, például a Tártallózó alkalmazással használ, ha szüksége lesz arra, hogy ezeket az alkalmazásokat, valamint az új kulccsal. Ha virtuális gépeket, amelyek VHD-fájlokat a tárfiók vannak tárolva, nem vonatkoznak rá a tárfiókkulcsok újragenerálása által.

A kulcsok az Azure-portálon állíthatja helyre. Miután a rendszer hozza újra létre a kulcsok vannak, azok tárolási szolgáltatások közötti szinkronizálását. legfeljebb 10 percig is tarthat.

Ha elkészült, ez az általános folyamat, és részletesen leírja, hogyan kell módosítani a kulcsot. Ebben az esetben a feltételezi, hogy jelenleg használt kulcs 1, és mindent kulcs 2 inkább módosítani kívánja.

1. Újragenerálja a kulcsot a 2. Győződjön meg arról, hogy biztonságos. Ez az Azure portálon teheti meg.
2. Az összes alkalmazást a biztonságitár-kulcs tárolására módosítsa a kulcs új kulcs 2 értéket használja. Tesztelje, és tegye közzé az alkalmazást.
3. Ha minden az alkalmazások és szolgáltatások működik, és megfelelően fut-e., generálja újra a kulcs 1. Ez biztosítja, hogy birtokában bárki számára, akihez nem kifejezetten adott az új kulcs már nem fog hozzáférni a tárfiókhoz.

Ha a jelenleg használt kulcs 2, ugyanazzal az eljárással, de a kulcsnevek fordított.

Néhány napon keresztül áttelepítése módosítása egyes alkalmazásokat, az új kulcsot használják, és a közzététel. Után az összes befejezése után meg kell majd lépjen vissza, és a régi kulcs újragenerálása, így már nem működik.

Egy másik lehetőség a tárfiók hívóbetűjét elhelyezése egy [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) valamilyen titkos adatot, és rendelkezik az alkalmazások kulcs lekérését onnan. Majd amikor újragenerálja a kulcsot, és frissítse az Azure Key Vault, az alkalmazások nem kell újratelepítése, mert azok felveszi az új kulcsot az Azure Key Vault automatikusan. Vegye figyelembe, hogy az alkalmazás olvassa a kulcsot minden alkalommal, amikor szükség lehet, vagy gyorsítótár, a memória, és használja azt, ha nem sikerül lekérni a kulcsot újra az Azure Key Vaulthoz.

Az Azure Key Vault használatával is egy másik védelmet biztosít a kulcsok számára. Ezt a módszert használja, ha soha nem lesz a tárolási kulcs szoftveresen kötött a konfigurációs fájlban, amely egy, a kulcsok külön engedélye nélkül hozzáférést az adott erőfeszítések eltávolítja.

Egy másik Azure Key Vault előnye is szabályozhatja a hozzáférést a kulcsokat az Azure Active Directoryval. Ez azt jelenti, hogy a néhány olyan alkalmazásokat, amelyek a kulcsok lekérése az Azure Key Vault és tudja, hogy más alkalmazások nem tudják anélkül, hogy azok kifejezetten engedéllyel kellene kulcsok eléréséhez szükséges hozzáférést biztosíthat.

Megjegyzés: ajánlott használni a kulcsok közül csak az alkalmazásokat egy időben. Az egyes helyek kulcs 1 és más kulcs 2 használatakor csak akkor tudja forgassa el a kulcsok egy alkalmazás-hozzáférés elvesztése nélkül.

#### <a name="resources"></a>További források
* [Az Azure Storage-fiókokról](storage-create-storage-account.md#regenerate-storage-access-keys)

  A cikk áttekintést nyújt a tárfiókok, valamint ismerteti a megtekintése, másolása és tárelérési kulcsok újragenerálása.
* [Az Azure Storage erőforrás szolgáltató REST API-referencia](https://msdn.microsoft.com/library/mt163683.aspx)

  Ez a cikk a tárfiókkulcsokat beolvasása és a tárfiókkulcsok újragenerálása egy Azure-fiók a REST API használatával meghatározott cikkek mutató hivatkozásokat tartalmaz. Megjegyzés: Ez az erőforrás-kezelő storage-fiókok.
* [A storage-fiókok műveletek](https://msdn.microsoft.com/library/ee460790.aspx)

  Ez a cikk a Service Manager tárolási REST API-referencia a meghatározott cikkek beolvasása és a REST API használatával tárfiókkulcsok újragenerálása mutató hivatkozásokat tartalmaz. Megjegyzés: Ez a klasszikus tárfiókokkal.
* [Sem kell kulcskezelés – az Azure AD az Azure Storage-adatokkal való hozzáférés kezelése](http://www.dushyantgill.com/blog/2015/04/26/say-goodbye-to-key-management-manage-access-to-azure-storage-data-using-azure-ad/)

  Ez a cikk bemutatja, hogyan Active Directory használata az Azure Storage kulcsok Azure Key Vault való hozzáférés szabályozása. Azt is bemutatja, hogyan újragenerálja a kulcsokat óránként egy Azure Automation-feladat segítségével.

## <a name="data-plane-security"></a>Adatbiztonság Vezérlősík
Adatbiztonság Vezérlősík védi az Azure Storage – a BLOB, a várólisták, a táblák és a fájlok tárolt adatok objektumok módszerek hivatkozik. Is láttuk módszerek titkosítani az adatokat és a biztonsági adatok átvitel során, de hogyan továbblépne hozzáféréssel az objektumokhoz szabályozásával kapcsolatban?

Kétféleképpen, amelyek engedélyezik a hozzáférést az adatok objektumokban. Ezek közé tartozik a tárfiókkulcsokat hozzáférés szabályozása, és hozzáférést biztosít az adott adatok objektumok egy adott időtartamig megosztott hozzáférési aláírásokkal használatával.

Emellett a Blob Storage engedélyezheti, nyilvános hozzáférés a blobok a hozzáférési szint beállítása a tároló, amely a blobok ennek megfelelően. Hozzáférés egy tárolót a Blob vagy tároló állítja be, ha engedélyezi az adott tárolóban lévő blobok nyilvános olvasási hozzáférés. Ez azt jelenti, hogy bárki, aki egy adott tárolóban lévő blob mutató URL-címet is nyissa meg a böngésző segítségével egy közös hozzáférésű Jogosultságkód-nak a tárfiókok kulcsait, vagy nélkül.

Engedélyezési keresztül-hozzáférés korlátozása mellett is használhatja [tűzfalak és a virtuális hálózatok](storage-network-security.md) a hálózati szabályok alapján a tárfiók való hozzáférés korlátozásához.  Ez a megközelítés lehetővé teszi, hogy megtagadja a hozzáférést, hogy nyilvános internetes forgalmat, és engedélyezze hozzáférést csak meghatározott Azure virtuális hálózat vagy a nyilvános internet IP-címtartományok.


### <a name="storage-account-keys"></a>Tárfiókkulcsok
Tárfiókkulcsok olyan 512 bites karakterláncok hozta létre Azure használható, a tárfiók nevét, valamint a tárfiókban tárolt adatok objektumok eléréséhez.

Például akkor is olvassa el a BLOB, üzenetsorok, táblák létrehozása, fájlok írására és módosítására. Ezek a műveletek számos Azure-portálon végezhető el vagy sok Tártallózó alkalmazást egyikével. Is írhat kódot a REST API-t vagy a Storage Ügyfélkódtáraival egyik segítségével hajthatja végre ezeket a műveleteket.

Az a szakaszban leírtaknak megfelelően a [felügyeleti Vezérlősík biztonsági](#management-plane-security), a klasszikus tárfiók adjon teljes hozzáférést az Azure-előfizetés által biztosított a tárolási kulcsok elérhető. A tárolási kulcsokat az Azure Resource Manager modellt használó tárfiókot hozzáférést szerepköralapú hozzáférés-vezérlést (RBAC) szabályozható.

### <a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>Hogyan adhat hozzáférést a fiókját megosztott hozzáférési aláírásokkal és hozzáférési házirendek tárolt objektumok
Egy közös hozzáférésű Jogosultságkód: URI, amely lehetővé teszi a tárolási objektum hozzáférést biztosíthat, és adja meg például az engedélyeket és a dátum/idő tartomány hozzáférési korlátozásokat lehet rendelni egy biztonsági jogkivonatot tartalmazó karakterlánc.

Blobok, tárolók, várólista-üzenetek, fájlok és táblák hozzáférést biztosíthat. Táblákkal ténylegesen engedélyt adhat a partíció- és sorfejlécek kulcstartományokkal, amely a felhasználó hozzáférjen szeretné megadásával entitástartományának a tábla elérésére. Például ha földrajzi állapot partíciókulcsú tárolt adatokat, akkor adhat valaki hozzáférést kaliforniai csupán azokat az adatokat.

Egy másik példa, előfordulhat, hogy adjon egy webalkalmazás egy SAS-jogkivonatot, amely lehetővé teszi, hogy bejegyzést írni a várólista, és adjon a feldolgozói szerepkör alkalmazás egy SAS-jogkivonat üzenetet az üzenetsorból, és dolgozza fel őket. Vagy nem ad egy ügyfél egy SAS-jogkivonat ezek segítségével a képek feltöltése a Blob Storage tárolót, és engedélyt a webes alkalmazás a képek olvasni. Mindkét esetben nincs kizárás kérdések – minden alkalmazáshoz is hozzáférést kell biztosítani csak a számukra a feladat végrehajtásához szükséges. Ez az megosztott hozzáférési aláírásokkal használatával lehetséges.

#### <a name="why-you-want-to-use-shared-access-signatures"></a>Miért érdemes használni a megosztott hozzáférési aláírásokkal
Miért érdemes használni egy SAS helyett csak a tárfiók kulcsára, amely így sokkal könnyebben kiadása? A tárfiók kulcsára kiadása van például a megosztás a kulcsokat, a tárolási Királyság. Teljes hozzáférést van. Valaki nem sikerült a kulcsok használatára, és töltse fel a teljes zenetár a tárfiókhoz. Ezek nem is vírus fertőzte verziók cserélje le a fájlokat, vagy az adatok ellopására. A tárfiókhoz átruházása a korlátlan hozzáféréssel olyan dolog, amire nem kell enyhén venni.

Megosztott hozzáférési aláírásokkal adhat egy ügyfél csak korlátozott mennyiségű időt a szükséges engedélyekkel. Például ha valaki tölti fel a blob-fiókjába, meg lehet adni azokat éppen elegendő idő (attól függően, a blob mérete természetesen) blob feltöltése írási hozzáférést. És ha megváltoztatja döntését, visszavonhatja a hozzáférést.

Ezenkívül megadható, hogy a SAS használatával kérelmek egy adott IP-cím vagy IP-címtartomány Azure külső korlátozódnak. Is megkövetelheti, hogy kérések (HTTPS vagy HTTP/HTTPS) egy adott protokoll használatával. Ez azt jelenti, ha szeretné engedélyezi a HTTPS forgalmat, beállíthatja a szükséges protokoll csak HTTPS és HTTP-forgalom le lesz tiltva.

#### <a name="definition-of-a-shared-access-signature"></a>A közös hozzáférésű Jogosultságkód meghatározása
Egy közös hozzáférésű Jogosultságkód egy olyan lekérdezési paraméterek fűzött az erőforráscsoportok mutató URL-címet

tevékenységszabályzat engedélyezett hozzáférési és az időtartamot, amelynek a hozzáférés engedélyezett. Íme egy példa a; Ezt az URI blob olvasási hozzáférést biztosít a öt perc. Vegye figyelembe, hogy a biztonsági Társítások lekérdezési paramétert kell URL-kódolású, például % 3A kettőspont (:) vagy a szóközt 20 %.

```
http://mystorage.blob.core.windows.net/mycontainer/myblob.txt (URL to the blob)
?sv=2015-04-05 (storage service version)
&st=2015-12-10T22%3A18%3A26Z (start time, in UTC time and URL encoded)
&se=2015-12-10T22%3A23%3A26Z (end time, in UTC time and URL encoded)
&sr=b (resource is a blob)
&sp=r (read access)
&sip=168.1.5.60-168.1.5.70 (requests can only come from this range of IP addresses)
&spr=https (only allow HTTPS requests)
&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D (signature used for the authentication of the SAS)
```

#### <a name="how-the-shared-access-signature-is-authenticated-by-the-azure-storage-service"></a>Hogyan hitelesíti a közös hozzáférésű Jogosultságkód az Azure Storage szolgáltatás
Amikor a társzolgáltatás a kérelmet kap, a lekérdezési paraméterek fogadja el, és létrehoz egy ugyanazzal a módszerrel a hívó program aláírást. Ezután összeveti a két aláírások. Ha az engedélyt megadják, majd a társzolgáltatás ellenőrizze a tároló verzióját győződjön meg arról, hogy érvényes, győződjön meg arról, hogy az aktuális dátumot és időt a megadott időszakon belül van, győződjön meg arról, hogy a hozzáférést a kért felel meg a kérelmet, stb.

Például a fenti URL-cím, a Ha az URL-cím helyett egy blobot egy fájlba lett mutat, a kérelem sikertelen lesz, mert ez azt jelenti, hogy a közös hozzáférésű Jogosultságkód egy BLOB. Ha a többi parancs meghívott blob frissíteni, mivel a közös hozzáférésű Jogosultságkód határozza meg, hogy engedélyezett-e a csak olvasási hozzáféréssel fognak működni.

#### <a name="types-of-shared-access-signatures"></a>Közös hozzáférésű Jogosultságkód típusai
* Egy szolgáltatási szint SAS segítségével tárfiókokban adott erőforrásokhoz férnek hozzá. Ez néhány példát keres a BLOB tároló, blob letöltése, egy tábla egy entitás frissítése, üzeneteket ad hozzá egy üzenetsort vagy fájlt feltölteni a fájlmegosztásba listáját.
* Egy fiók szintű SAS használatával lehet hozzáférni, amelyeket a szolgáltatásiszint-SAS-kód nem használható. Emellett biztosíthat a beállítások nem használhatók együtt egy szolgáltatási szint SAS-tárolók, táblák, üzenetsorok és fájlmegosztások létrehozásához például erőforrásokhoz. Több szolgáltatásokhoz való hozzáférés egyszerre is megadható. Például előfordulhat, hogy adjon valaki blobok és a tárfiókban lévő fájlok elérését.

#### <a name="creating-a-sas-uri"></a>A SAS URI-t létrehozni
1. Létrehozhat egy URI-t az igény szerinti meghatározása a lekérdezési paraméterek mindegyikét minden alkalommal, amikor.

   Ez a megközelítés rugalmas, de ha egy olyan logikai készlete, amelyek hasonló minden alkalommal, amikor paraméterek, a tárolt házirend használatával segítenek meghatározni.
2. A tárolt hozzáférési házirend egy teljes tárolóhoz, a fájlmegosztásokhoz, a tábla vagy a várólista hozhat létre. Majd ezzel a alapjául a SAS URI azonosítók hoz létre. Engedélyek tárolt hozzáférési házirendek alapján könnyen visszavonhatók. Minden egyes tároló, a várólista, a tábla vagy a fájlmegosztás definiált legfeljebb öt házirendek lehet.

   Például ha szeretné, hogy egy adott tárolóban lévő blobok olvasási sokan volt is, létrehozhat egy tárolt hozzáférési házirend, amely szerint a "olvasási hozzáférést" és a ugyanaz lesz minden alkalommal, amikor egyéb beállításait. Ezután egy SAS URI azonosítója a tárolt házirend beállításainak használatával, és a lejárati dátum és idő megadása is létrehozhat. Ennek előnye, hogy ne kelljen adja meg az összes lekérdezési paraméterekhez minden alkalommal.

#### <a name="revocation"></a>Visszavonási
Tegyük fel, hogy a biztonsági Társítások biztonsága sérült, vagy módosítja azt a vállalati biztonsági vagy az előírásoknak való megfelelés követelmények miatt. Hogyan, visszavonhatja a hozzáférést egy erőforráshoz, hogy a SAS használatával? Függ, hogyan hozza létre a SAS URI-t.

Ha ad hoc URI-azonosítók használata esetén lehetősége van három. SAS-tokenje rövid lejárati házirendek adja ki, és várja meg, a biztonsági Társítások lejár. Nevezze át vagy törölje az erőforrást (feltéve, hogy a jogkivonat egy adott objektum lett hatóköre) is. Módosíthatja a tárfiók kulcsait. Az utolsó lehetőség jelentős hatással lehet, attól függően, hogy hány szolgáltatásokat használ, hogy a tárfiók, és valószínűleg nem valami néhány tervezés nélkül szeretné.

A tárolt házirend származó SAS használatakor is eltávolíthat, hozzáférés visszavonása a tárolt házirend – ugyanúgy módosíthatja, már lejárt, vagy távolítsa el az egész. Ez azonnal érvénybe lép, és minden SAS létre adott tárolt házirend érvényteleníti. Frissítése, vagy a tárolt házirend eltávolítása is hatás férnek hozzá a meghatározott tárolóban, megosztott fájl tábla, vagy várólista keresztül SAS, ha az ügyfelek készültek, így egy új SAS kérnek, ha a régit válik érvénytelen, de ez működnek jól.

A tárolt házirend származó SAS használatával lehetővé teszi az adott SAS azonnal visszavonni, mert az ajánlott mindig használjon tárolt hozzáférési házirendek lehetőség.

#### <a name="resources"></a>További források
További részletes információt a megosztott hozzáférési aláírásokkal és tárolt hozzáférési házirendeket, kész, de példák tekintse meg a következő cikkeket:

* Ezek azok a útmutatót.

  * [Service SAS](https://msdn.microsoft.com/library/dn140256.aspx)

    Ez a cikk egy szolgáltatási szint SAS használatával blobokat, az üzenetsor-üzeneteket, a tábla tartományokkal és a fájlok példákat.
  * [A szolgáltatásalapú SAS létrehozása](https://msdn.microsoft.com/library/dn140255.aspx)
  * [SAS fiók létrehozása](https://msdn.microsoft.com/library/mt584140.aspx)
* Ezek a oktatóanyagok a .NET ügyféloldali kódtár használatával történő létrehozásához a megosztott hozzáférési aláírásokkal és hozzáférési házirendek tárolja.

  * [Közös hozzáférésű Jogosultságkód (SAS) használatával](../storage-dotnet-shared-access-signature-part-1.md)
  * [Közös hozzáférésű Jogosultságkód, 2. rész: Létrehozása és SAS-kód a Blob szolgáltatás használatához](../blobs/storage-dotnet-shared-access-signature-part-2.md)

    Ez a cikk tartalmazza a SAS-modell, megosztott hozzáférési aláírásokkal, például annak magyarázatát, és az ajánlott eljárás javaslatok SAS használja. Azt is ismertetjük, a megadott engedélyeket visszavonását.

* Hitelesítés

  * [Az Azure Storage szolgáltatásainak hitelesítése](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* Közös hozzáférésű Jogosultságkód első lépések útmutató

  * [Első lépések útmutató SAS](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

## <a name="encryption-in-transit"></a>Az átvitel során titkosítás
### <a name="transport-level-encryption--using-https"></a>Átviteli szintű titkosítást – HTTPS-kapcsolaton keresztül
Egy újabb lépést kell tennie az Azure Storage-adatok védelme érdekében titkosítja az adatokat, az ügyfél és az Azure Storage között. Az első javasoljuk, hogy mindig a [HTTPS](https://en.wikipedia.org/wiki/HTTPS) protokoll, amely biztosítja a biztonságos kommunikáció érdekében a nyilvános interneten keresztül.

Ahhoz, hogy egy biztonságos kommunikációs csatornát, mindig használandó HTTPS tárolási objektumokat a REST API-k hívása vagy eléréséhez. Emellett **megosztott hozzáférési aláírásokkal**, amelyek segítségével delegálása az Azure Storage-objektumokhoz való hozzáférést, adja meg, hogy csak a HTTPS protokollt is használható-e a megosztott hozzáférési aláírásokkal, győződjön meg arról, hogy a megfelelő protokollt használja birtokában bárki küldi ki az SAS-tokenje hivatkozások használatával teszi.

Amikor tárfiókok engedélyezésével történt a REST API-k elérésére objektumokat a HTTPS használata kényszerítheti [szükséges átviteli biztonságos](../storage-require-secure-transfer.md) a tárfiók. Ha ez engedélyezve van a kapcsolatok HTTP-n keresztül program elutasítja.

### <a name="using-encryption-during-transit-with-azure-file-shares"></a>Az Azure fájlmegosztások titkosítással továbbítás során
Az Azure Files támogatja a HTTPS PROTOKOLLT, ha a REST API használatával, de több általánosan használt SMB-fájlmegosztás a virtuális Géphez van csatolva. SMB 2.1 nem támogatja a titkosítást, tehát kapcsolatokat csak az Azure-ban ugyanabban a régióban belül engedélyezett. Azonban az SMB 3.0 támogatja a titkosítást, és a Windows Server 2012 R2, Windows 8, Windows 8.1 és Windows 10, lehetővé téve a kereszt-régió hozzáférés és a hozzáférés az asztalon érhető el.

Az Azure fájlmegosztások Unix használható, amíg a Linux SMB-ügyfél még támogatja a titkosítást, így csak elérését az Azure-régiót belül. Linux titkosítás támogatása van Linux fejlesztők SMB funkció felelős a programba. Titkosítási adnak hozzá, amikor Ön fog rendelkezésre áll a Linux Azure fájlmegosztás eléréséhez a Windows.

A rendszer az Azure-fájlok szolgáltatással titkosítás kényszerítheti a engedélyezésével [szükséges átviteli biztonságos](../storage-require-secure-transfer.md) a tárfiók. A REST API-k használata, ha HTTPs megadása kötelező. Az SMB-csak SMB-kapcsolatok, amely támogatja a titkosítást az sikeresen fog csatlakozni.

#### <a name="resources"></a>További források
* [Az Azure Files bemutatása](../files/storage-files-introduction.md)
* [A Windows Azure-fájlok az első lépései](../files/storage-how-to-use-files-windows.md)

  Ez a cikk áttekintést nyújt az Azure fájlmegosztások és csatlakoztatja, és alkalmazza őket a Windows hogyan.

* [Az Azure Files használata Linuxszal](../files/storage-how-to-use-files-linux.md)

  Ez a cikk bemutatja, hogyan csatlakoztatása az Azure-fájlmegosztáshoz egy Linux rendszer- és feltöltése/letöltés fájlok.

### <a name="using-client-side-encryption-to-secure-data-that-you-send-to-storage"></a>Az ügyféloldali titkosítással tárolási küldött adatok védelmét
Egy másik lehetőség, amelynek segítségével győződjön meg arról, hogy az adatok biztonságos egy ügyfélalkalmazást és a tároló között történő átvitel során az ügyféloldali titkosítás. Az adatok titkosítása az Azure Storage átvitele előtt. Amikor az adatok beolvasása az Azure Storage-ból, az adatok visszafejtése ügyféloldali fogadását követően. Annak ellenére, hogy az adatok titkosítása a hálózaton keresztül is, azt javasoljuk, hogy is használja HTTPS, mert már van, amelyek segítenek az adatok integritásának érintő hálózati hibák beépített adatok integritás-ellenőrzést.

Ügyféloldali titkosítás egyben inaktív, az adatok titkosítására, a titkosított formában tárolja az adatokat. Lesz döntésről bővebben Ez a szakasz részletesen a [titkosítását](#encryption-at-rest).

## <a name="encryption-at-rest"></a>Aktívan titkosítása
Nincsenek három Azure szolgáltatást biztosító titkosítását. Az Azure Disk Encryption az operációsrendszer- és adatlemezek IaaS virtuális gépek titkosítására szolgál. Ügyféloldali titkosítás és SSE is használják az Azure Storage-adatok titkosítására. 

Ügyféloldali titkosítás használhatja az adattitkosítást átvitel közben (amely a tárolási titkosítás nélkül is tárol), célszerű lehet az átvitel közben HTTPS használja, és az adatok titkosításához automatikusan tárolt bármilyen módon. Két módon ehhez--Azure Disk Encryption és SSE. Közvetlenül a virtuális gépek által használt operációsrendszer- és adatlemezek az adatok titkosításához használt egyik, és az egyéb Azure Blob Storage írt adatok titkosítására szolgál.

### <a name="storage-service-encryption-sse"></a>Storage Service Encryption (SSE)

SSE engedélyezve van az összes tárfiók, és nem tiltható le. SSE automatikusan titkosítja az adatokat, az Azure Storage írásakor. Azure Storage adatokat olvasni, amikor az visszafejti Azure Storage előtt ad vissza. SSE lehetővé teszi az adatok anélkül, hogy a kód módosítására, vagy adja hozzá a kódot az alkalmazásokat.

A Microsoft által felügyelt kulcsok vagy a saját egyéni kulcsokat is használhatja. A Microsoft felügyelt kulcsoknál állít elő, és a biztonságos tárolására, valamint a rendszeres Elforgatás belső Microsoft házirend által meghatározott kezeli. Egyéni kulcsok használatával kapcsolatos további információkért lásd: [Storage szolgáltatás titkosítási kulcsokkal ügyfél által felügyelt Azure Key Vault a](storage-service-encryption-customer-managed-keys.md).

Az SSE automatikusan titkosítja minden teljesítményszint (Standard és Prémium), minden üzembehelyezési modell (Azure Resource Manager és klasszikus) és minden Azure Storage-szolgáltatás (Blob, Queue, Table és File) adatait. 

### <a name="client-side-encryption"></a>Client-side Encryption
Ügyféloldali titkosítás azt említett, az adatok átvitele történik a titkosítás ismertetésekor. Ez a funkció lehetővé teszi a programozott módon az ügyfélalkalmazás mielőtt elküldené a hálózaton keresztül Azure Storage kellene írni, és szoftveres visszafejteni az adatok beolvasása az Azure Storage-ból után az adatok titkosítását.

Így a titkosítás az átvitel során, de aktívan titkosítás funkciót is biztosít. Bár az adatok titkosítása az átvitel során, ajánlott a beépített adatok integritás-ellenőrzést, amelyekkel csökkenthető annak az adatok integritásának érintő hálózati hibák előnyeinek kihasználása érdekében a HTTPS protokoll használatával.

Amennyiben ezzel például ha egy webes alkalmazás, amely BLOB tárolja, és lekéri a blobok, de azt szeretné, hogy az alkalmazás és az adatok a biztonságos lehető. Ebben az esetben használja az ügyféloldali titkosítás. A forgalom között az ügyfél és az Azure Blob szolgáltatás a titkosított erőforrást tartalmaz, és senki sem értelmezheti az adatokat átvitel közben, és a titkos blobok pótlására azt.

Ügyféloldali titkosítás a Java és a .NET storage ügyfélkódtáraival, amelyek viszont a Azure kulcs tároló API-k, így könnyen megvalósítása van beépítve. Az adatok titkosítása és visszafejtése folyamatán a boríték módszerrel, és a titkosítás, az egyes tárolási objektum által használt metaadatokat tárol. Például a blobok, tárol, a blob metaadatai között, a várólisták, azt hozzáadja azt minden sor üzenetet.

A titkosítási magát Ön hozza létre és a saját titkosítási kulcsok kezeléséhez. Használhatja az Azure Storage ügyféloldali kódtár által létrehozott kulcsok, vagy beállíthatja, hogy az Azure Key Vault a kulcsok létrehozásához. A titkosítási kulcsok tárolása a helyszíni kulcstároló, vagy tárolhatja őket az Azure Key Vault. Az Azure Key Vault lehetővé teszi a titkos kulcsok Azure Key Vault a hozzáférést az adott felhasználóknak, az Azure Active Directoryval. Ez azt jelenti, hogy birtokában bárki nem csak az Azure Key Vault olvasni, valamint használata ügyféloldali titkosítási kulcsok.

#### <a name="resources"></a>További források
* [Titkosításához és visszafejtéséhez az Azure Key Vault használatával a Microsoft Azure Storage blobs](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)

  Ez a cikk bemutatja, hogyan ügyféloldali titkosítás használata az Azure Key Vault, többek között a KEK létrehozása és a PowerShell használatával tárolóban tárolja.
* [A Microsoft Azure Storage ügyféloldali titkosítás és az Azure Key Vault](../storage-client-side-encryption.md)

  Ez a cikk ügyféloldali titkosítás magyarázatot ad, és példákat a storage ügyféloldali kódtár segítségével titkosításához és visszafejtéséhez a négy tárolási szolgáltatások erőforrásait. Azt is Azure Key Vault szól.

### <a name="using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines"></a>A virtuális gépek által használt lemezek Azure Disk Encryption titkosítása
Az Azure Disk Encryption az új szolgáltatása. Ez a funkció lehetővé teszi, hogy az operációs rendszer és az infrastruktúra-szolgáltatási virtuális gép által használt adatok lemezek titkosítását. A Windows a meghajtók titkosítása szabványos BitLocker titkosítás technológia használatával. Linux a lemez titkosítása a DM-Crypt technológia használatával. Ez integrálva van az Azure Key Vault lehetővé teszi a lemez titkosítási kulcsok kezeléséhez, és szabályozhatja.

A megoldás a következő szituációkat IaaS virtuális gépek Microsoft Azure-ban engedélyezésekor:

* Az Azure Key Vault integrációja
* Standard szint virtuális gépek: [A, D, DS, G, GS és stb adatsorozat IaaS virtuális gépeket](https://azure.microsoft.com/pricing/details/virtual-machines/)
* A Windows és Linux IaaS virtuális gépeket titkosítás engedélyezése
* Az operációs rendszer és az adatok titkosításának letiltása Windows IaaS virtuális meghajtók
* Linux IaaS virtuális gépeket az adatmeghajtók titkosításának letiltása
* Az infrastruktúra-szolgáltatási virtuális gépeken futó Windows-ügyfél operációs rendszert futtató titkosítás engedélyezése
* Csatlakoztatási elérési utakat tartalmazó kötetek titkosítás engedélyezése
* A Linux virtuális gépeken, a lemez (RAID) szétosztott mdadm használatával konfigurált titkosítás engedélyezése
* A Linux virtuális gépeken titkosítás engedélyezése adatlemezek LVM használatával
* A Windows virtuális gépeken, a tárolóhelyek használatával konfigurált titkosítás engedélyezése
* Minden Azure-nyilvános régió támogatottak.

A megoldás nem támogatja a következő forgatókönyvek, szolgáltatások és technológia a kiadásban:

* Az alapszintű csomag IaaS virtuális gépeket
* Linux IaaS virtuális gépeket egy operációs rendszer meghajtóján titkosításának letiltása
* Infrastruktúra-szolgáltatási virtuális gépeket, a klasszikus virtuális gép létrehozási módszer használatával létrehozott
* Integráció a helyszíni kulcskezelő szolgáltatás
* Az Azure Files (megosztott fájlrendszer), a hálózati fájlrendszer (NFS), a dinamikus köteteket és a Windows alapú szoftveres RAID-rendszerek használatára konfigurált virtuális gépek


> [!NOTE]
> A következő Linux terjesztésekről jelenleg támogatott Linux operációs rendszert futtató lemeztitkosítás: RHEL 7.2, CentOS 7.2n és Ubuntu 16.04.
>
>

Ez a szolgáltatás biztosítja, hogy a virtuális gépek lemezeit a összes adata titkosításra kerül-e az Azure Storage aktívan.

#### <a name="resources"></a>További források
* [Windows és Linux IaaS virtuális gépeket az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)

### <a name="comparison-of-azure-disk-encryption-sse-and-client-side-encryption"></a>Az Azure Disk Encryption, az SSE és az ügyféloldali titkosítás összehasonlítása

#### <a name="iaas-vms-and-their-vhd-files"></a>Infrastruktúra-szolgáltatási virtuális gépek és a VHD-fájlok

Az adatlemezek IaaS virtuális gépek által használt az Azure Disk Encryption ajánlott. Ha egy virtuális Gépet hoz létre nem felügyelt a lemezek lemezkép az Azure piactérről, az Azure-ban egy [másolási sekély](https://en.wikipedia.org/wiki/Object_copying) a kép a tárolási fiók az Azure Storage, és nem titkosított akkor is, ha az SSE engedélyezve van. Miután a virtuális Gépet hoz létre, és elindítja a lemezkép frissítése, SSE indul el az adatok titkosításához. Emiatt tanácsos Azure Disk Encryption használandó virtuális gépeket az Azure piactéren képek alapján létrehozott, ha azt szeretné, teljesen titkosítottak, nem felügyelt lemezzel. Felügyelt lemezt egy virtuális Gépet hoz létre, ha SSE titkosítja az adatokat felügyelt platform kulcsokkal alapértelmezés szerint. 

Ha később egy előre titkosított virtuális Gépet az Azure a helyszíni, lesz, a titkosítási kulcsok feltöltése az Azure Key Vaultba, és továbbra is a titkosítást használni ezt a virtuális gépet, hogy a helyszínen használt. Az Azure Disk Encryption engedélyezve van ez a forgatókönyv kezelésére.

Ha virtuális merevlemez nem titkosított a helyszíni, töltse fel az egyéni lemezképként gyűjteménye, és a virtuális gép kiépítéséhez. Ehhez a Resource Manager-sablonok használatával, kérje meg, hogy a virtuális gép elinduló Azure Disk Encryption bekapcsolás.

Ha hozzá adatlemezt, és csatlakoztassa azt a virtuális Gépet, a lemezen levő adatok bekapcsolhatja Azure Disk Encryption. Először titkosítja az adott adatok lemezek helyi, és a klasszikus üzembe helyezési modell rétege fogja tegye elleni tárolási Lusta írási, a tárolási tartalom titkosított.

#### <a name="client-side-encryption"></a>Ügyféloldali titkosítás
Ügyféloldali titkosítás nem az adatok titkosítása a legbiztonságosabb módszer, mert azt titkosítja az adatokat átvitel közben előtt.  Azonban ez nem igényel kód hozzáadása az alkalmazások, a tároló, amely előfordulhat, hogy nem szeretné. Ezekben az esetekben a HTTPS használatával az átvitel során az adatok védelme. Amennyiben az adatok Azure Storage ér el, a SSE titkosítás.

Az ügyféloldali titkosítással táblaentitásokat, az üzenetsor-üzeneteket és a blobok titkosíthatók. 

Ügyféloldali titkosítás teljes mértékben az alkalmazás kezeli. Ez a legbiztonságosabb módszer, de kell programozott módosítja az alkalmazás és kulcskezelés folyamatok vezetnek be. Használja ezt a további biztonsági átvitel során, és azt szeretné, hogy a tárolt adatok titkosításához.

Ügyféloldali titkosítás nagyobb terhelést az ügyfélen, és különösen akkor, ha titkosított és nagy mennyiségű adat átvitele a méretezhetőség tervek a fiókot, hogy.

#### <a name="storage-service-encryption-sse"></a>Storage Service Encryption (SSE)

Azure Storage SSE kezeli. SSE nem biztosítja a biztonsági adatok az átvitel során, de az adatok titkosítása, Azure Storage változatlan formában. Az SSE nem befolyásolja az Azure Storage teljesítményét.

Titkosíthatja az adatokat a tárfiók SSE használatával bármilyen típusú (blokkblobokat, hozzáfűző blobokat, a lapblobokat, a tábla adatai, a várólista adatok és a fájlok).

Ha egy archív vagy a könyvtár a VHD-fájlokat az új virtuális gépek létrehozásának alapjaként használja, hozzon létre egy új tárfiókot, és majd töltse fel a VHD-fájlok ezekhez a fiókokhoz. Ezeket a VHD-fájlok Azure Storage titkosítva legyen.

Ha Azure Disk Encryption engedélyezve a virtuális gép lemezét, majd minden újonnan írt adatok titkosítása SSE és Azure Disk Encryption.

## <a name="storage-analytics"></a>Storage Analytics
### <a name="using-storage-analytics-to-monitor-authorization-type"></a>Storage Analytics segítségével engedélyezési figyelőtípus
Minden tárfiók engedélyezheti az Azure Storage Analytics naplózási elvégzéséhez és a metrikák adatainak tárolásához. Ez az egy remek eszköz használatára ellenőrizheti a tárfiók a teljesítményi értékeket, vagy kell elhárítania a storage-fiók, mert teljesítményproblémák lépnek.

A storage analytics naplók látható egy másik adat tároló elérésekor valaki által használt hitelesítési módszert. Például a Blob-tároló, megtekintheti azokat használni egy közös hozzáférésű Jogosultságkód vagy a tárfiókok kulcsait, vagy ha a blob elérhető nyilvános volt.

Ez akkor lehet hasznos, ha szorosan esetlegesen korán tároló elérésére. Például a Blob Storage tárolóban után állítsa be a tároló összes magán is valósítja meg az SAS-szolgáltatás használata az alkalmazások teljes. Ellenőrizze rendszeresen a naplókat blobok a tárfiókok kulcsait, ami azt jelezheti a biztonság megsértése, érhető el, vagy ha a blobok olyan nyilvános, de nem lehetnek.

#### <a name="what-do-the-logs-look-like"></a>Hogyan hajtsa végre a naplók meg?
Engedélyezi a tárolási fiók metrikák és naplózás az Azure portálon keresztül, analitikai adatok megkezdik gyorsan felhalmozhat után. A naplózás és a metrikák minden szolgáltatás külön; a naplózás csak akkor ír, amikor a forgalom storage-fiókhoz tartozó közben minden percben, óránként vagy naponta, attól függően, hogy hogyan konfigurálja a metrikák lesz naplózva.

A naplók blokkblobokat a tárfiókban $logs nevű tárolóban vannak tárolva. Ebben a tárolóban automatikusan jön létre, ha engedélyezve van a tárolási analitika. A tároló jön létre, nem törölhetők, de törölheti annak tartalmát.

A $logs tárolóban a mappa minden egyes szolgáltatás, és majd nincsenek almappái az év/hónap/nap/óra. Óránként a naplók számozása. Ez az mit fog kinézni a mappastruktúrát:

![Naplófájlok megtekintése](./media/storage-security-guide/image1.png)

Azure Storage minden kérést a rendszer naplózza. Íme egy naplófájlba, az első néhány mezőket megjelenítő pillanatképet.

![Pillanatkép egy naplófájl](./media/storage-security-guide/image2.png)

Láthatja, hogy segítségével a naplók tárfiókba hívások bármilyen nyomon.

#### <a name="what-are-all-of-those-fields-for"></a>Mik azok a mezők összes?
Van egy cikket, amely a erőforrásait, szerepel a naplókat, valamint a használatuk sok mezőinek listáját jeleníti meg. A mezők sorrendben listája itt található:

![Pillanatkép a mezők a naplófájlban](./media/storage-security-guide/image3.png)

Azt is érdekli GetBlob vonatkozó bejegyzéseket, és hogyan hitelesítik, így kell a művelet-type "Get-Blob" bejegyzések, a kérelem-állapotának (negyedik</sup> oszlop) és a hitelesítési típus (nyolcadik</sup> oszlop).

Például a fenti listaelem első néhány sor a szolgáltatáskérés állapota "Sikeres" és a hitelesítési típus "hitelesített". Ez azt jelenti, hogy a kérelem a tárfiók kulcsa segítségével lett érvényesítve.

#### <a name="how-are-my-blobs-being-authenticated"></a>Hogyan vannak a blobok hitelesített?
Három olyan esetekben, amely azt is van.

1. A blob nem nyilvános, és hozzáférés nélkül egy közös hozzáférésű Jogosultságkód egy URL-cím használatával. Ebben az esetben a kérelem-állapota "AnonymousSuccess" és az engedélyezési-típus: "névtelen".

   1.0;2015-11-17T02:01:29.0488963Z;GetBlob;**AnonymousSuccess**;200;124;37;**anonymous**;;mystorage…
2. A blob magánjellegű és egy közös hozzáférésű Jogosultságkód volt használva. Ebben az esetben a kérelem-állapota "SASSuccess" és az engedélyezési-típus: "sas".

   1.0;2015-11-16T18:30:05.6556115Z;GetBlob;**SASSuccess**;200;416;64;**sas**;;mystorage…
3. A blob személyes, és a kulcs lett megadva az eléréséhez. Ebben az esetben a kérelem-állapota "**sikeres**"és a hitelesítési típus:"**hitelesített**".

   1.0; 2015-11-16T18:32:24.3174537Z; GetBlob; **Sikeres**; 206-os; 59; 22; **hitelesített**; mystorage...

A Microsoft Message Analyzer segítségével megtekintheti, és ezek a naplók elemzése. Ez magában foglalja a keresési és szűrési lehetőségeket. Például előfordulhat, hogy a keresni kívánt várt, ez azt jelenti, hogy a használata esetén győződjön meg arról, hogy valaki nem fér hozzá a tárfiók nem megfelelően megjelenítéséhez GetBlob példányai.

#### <a name="resources"></a>További források
* [Storage Analytics](../storage-analytics.md)

  Ez a cikk a tárolási analitika és ahhoz, hogyan nyújt áttekintést.
* [Storage Analytics naplóformátumban](https://msdn.microsoft.com/library/azure/hh343259.aspx)

  Ez a cikk bemutatja a Storage Analytics naplóformátumban, és adatokat, az elérhető mezők, például hitelesítés típusa, amely megadja, hogy a kérelemhez használt hitelesítés típusa.
* [A figyelő egy Tárfiókot, Azure-portálon](../storage-monitor-storage-account.md)

  Ez a cikk ismerteti, hogyan konfigurálható a metrikák figyelését és a storage-fiókok naplózása.
* [Azure Storage Metrics és a naplózás, az AzCopy és a Message Analyzer segítségével végpontok – hibaelhárítás](../storage-e2e-troubleshooting.md)

  Ez a cikk beszél hibaelhárítás a Storage Analytics segítségével, és a Microsoft Message Analyzert használatát ismerteti.
* [Microsoft Message Analyzer üzemeltetési útmutató](https://technet.microsoft.com/library/jj649776.aspx)

  Ez a cikk a Microsoft Message Analyzert mutató hivatkozás, és egy oktatóanyag, a gyors üzembe helyezés és a szolgáltatás összefoglaló mutató hivatkozásokat tartalmaz.

## <a name="cross-origin-resource-sharing-cors"></a>Eltérő eredetű erőforrások megosztása (CORS)
### <a name="cross-domain-access-of-resources"></a>Tartományok közötti elérést erőforrások
Ha egy tartományban futó webböngésző hajt végre egy HTTP-kérelem erőforrás más tartományokból, ez a lehetőség egy eltérő eredetű HTTP-kérelem. Például a contoso.com helyről HTML-lapot fabrikam.blob.core.windows.net üzemeltetett JPEG-fájlok kérelmet küld. Biztonsági okokból böngészők korlátozza a parancsprogramok, például a JavaScriptek belül kezdeményezett eltérő eredetű HTTP-kérelmekre. Ez azt jelenti, hogy néhány JavaScript-kódot egy weblapon a contoso.com tartományon kéri, hogy a fabrikam.blob.core.windows.net jpeg, amikor a böngésző nem teszi lehetővé a kérelmet.

Mi nem ezt kell elvégezni az Azure Storage? Is, ha statikus erőforrásokat, például a JSON- vagy XML-adatfájlok tárolja a Blob Storage a Fabrikam tárfiókot használ neve, a tartomány eszközök lesz fabrikam.blob.core.windows.net, és a contoso.com webalkalmazás nem lesz képes azok eléréséhez JavaScript használatával, mert a tartományok eltérőek. Ez akkor is igaz, ha egy Azure Storage szolgáltatások – például a Table Storage – hívni kívánt, térjen vissza a JSON a JavaScript-ügyfél által feldolgozandó adatokat.

#### <a name="possible-solutions"></a>Lehetséges megoldások
One way to resolve this is to assign a custom domain like "storage.contoso.com" to fabrikam.blob.core.windows.net. A probléma oka, hogy csak rendelhet, hogy egyéni tartomány egy tárfiókot. Mi történik, ha az eszközök vannak tárolva több tárfiókot?

A probléma megoldásához úgy, hogy a webes alkalmazás a tároló hívások proxyként működjön. Ez azt jelenti, ha a fájl feltöltése a Blob Storage, a webes alkalmazás ehhez vagy helyileg írják és másolásához Blob-tároló, az ehhez a memóriába az összes és írni a Blob Storage. Alternatív megoldásként egy dedikált webes alkalmazás (például egy webes API), amely feltölti a fájlokat helyileg, és írja őket a Blob Storage lehet írni. Mindkét módszer esetén kell fiók adott funkcióval, ha igények meghatározása a méretezhetőséget.

#### <a name="how-can-cors-help"></a>Hogyan segíthetnek a CORS?
Az Azure Storage lehetővé teszi a kereszt-eredetű erőforrások megosztása – a CORS engedélyezése. Minden tárfiók férhetnek hozzá a fiókhoz tartozó tárolási erőforrások tartományokat is megadhat. Például a fent vázolt esetünkben azt is a CORS engedélyezése a fabrikam.blob.core.windows.net tárfiók, és konfigurálja úgy, hogy engedélyezi a hozzáférést a contoso.com felé. A webes alkalmazás contoso.com majd közvetlenül hozzáférhetnek a fabrikam.blob.core.windows.net lévő erőforrásokat.

Egy dolog, ami arról értesít, hogy a CORS lehetővé teszi a hozzáférést, de nem biztosít hitelesítést, amely szükséges az összes nem-nyilvános hozzáférés tárolási erőforrások. Ez azt jelenti, akkor csak hozzáférni az blobokat, ha azok nyilvános vagy telepíthet egy közös hozzáférésű Jogosultságkód a megfelelő engedélyt. Táblák, üzenetsorok és fájlok nincs nyilvános hozzáférés, és a SAS-kód szükséges.

A CORS alapértelmezés szerint le van tiltva az összes szolgáltatás. Engedélyezheti a CORS hívja service házirendek beállítása a módszerek egyikét a REST API-t vagy a storage ügyféloldali kódtár segítségével. Ha így tesz, meg kell adnia egy CORS szabály, amely az XML-Fájlban. Íme egy példa egy CORS szabályt, amely a szolgáltatás tulajdonságainak beállítása művelet a Blob szolgáltatás használatával egy tárfiók van beállítva. Ennek a műveletnek az Azure Storage a storage ügyféloldali kódtár vagy a REST API-k használatával végezheti el.

```xml
<Cors>    
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
        <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
        <MaxAgeInSeconds>200</MaxAgeInSeconds>
    </CorsRule>
<Cors>
```

Ez minden egyes sorára jelenti:

* **AllowedOrigins** ez alapján, mely nem egyező tartományok kérelmezhet és fogadhat adatokat a tárhelyre. Ez azt jelzi, hogy a contoso.com és fabrikam.com kérhetnek az adatok Blob-tároló egy adott tárfiók. A helyettesítő karakter is állíthat (\*) kérelmek eléréséhez minden tartományban engedélyezi.
* **AllowedMethods** a lista (HTTP-kérelem műveletek) módszer használható, amikor a kérést. Ebben a példában csak a PUT és a GET használható. Beállíthatja a helyettesítő karakter (\*) engedélyezéséhez használt összes módszert.
* **AllowedHeaders** Ez az a kérelem fejlécében, hogy a forrástartomány adhat meg, amikor a kérést. Ebben a példában minden metaadat fejléc kezdve az x-ms-metaadatok, x-ms-metaadat-tároló és az x-ms-meta-abc engedélyezett. A helyettesítő karakter (\*) azt jelzi, hogy engedélyezi-e a-tól kezdődően fejlécben megadott előtag.
* **ExposedHeaders** ez alapján mely válaszfejlécek elérhetővé tehető a böngésző számára a kérelmet kibocsátó. Ebben a példában minden kezdve fejléc "x-ms - meta-" megjelenik.
* **MaxAgeInSeconds** Ez az a maximális időt, hogy a böngésző gyorsítótárazza-e a ellenőrzési beállítások kérelmet. (Az előzetes kérelmekre vonatkozó további információkért ellenőrizze a első cikkben.)

#### <a name="resources"></a>További források
CORS és az engedélyezéshez kapcsolatos további információkért tekintse meg ezeket az erőforrásokat.

* [Eltérő eredetű erőforrások megosztása (CORS) támogatása az Azure Storage szolgáltatásainak az Azure.com-on](../storage-cors-support.md)

  Ez a cikk áttekintése CORS-t és a különböző tárolószolgáltatások vonatkozó szabályok beállításával.
* [Eltérő eredetű erőforrások megosztása (CORS) az MSDN Webhelyén az Azure Storage szolgáltatások támogatása](https://msdn.microsoft.com/library/azure/dn535601.aspx)

  Ez a CORS-támogatás az Azure Storage szolgáltatások dokumentációját. Ez alkalmazása az egyes társzolgáltatás cikkek hivatkozásainak rendelkezik és példáját mutatja be, és ismerteti az egyes elemei a CORS-fájlban.
* [A Microsoft Azure Storage: A CORS bemutatása](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/02/03/windows-azure-storage-introducing-cors.aspx)

  Ez a CORS bejelentése, és bemutatja, hogyan használhatja a kezdeti blog cikkre mutató hivatkozást.

## <a name="frequently-asked-questions-about-azure-storage-security"></a>Azure Storage biztonsági kapcsolatos gyakori kérdések
1. **Hogyan ellenőrizheti a blobok I virtuális gépbe vagy onnan Azure Storage vagyok átvitel, ha nem használható a HTTPS protokoll sértetlenségének?**

   Ha bármilyen okból kell használni a HTTP helyett HTTPS és a blokkblobokhoz dolgozik, használhatja a MD5 ellenőrzése érdekében ellenőrizni az átvitel során a BLOB. Ez segít hálózati transport layer hibák elleni védelem, de nem feltétlenül közvetítő támadások.

   Ha a HTTPS-t, amely átvitelszintű biztonság, akkor használja, MD5 ellenőrzése redundáns és szükségtelen is használhatja.

   További információkért vegye ki a [Azure Blob MD5 áttekintése](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/18/windows-azure-blob-md5-overview.aspx).
2. **Mi a helyzet a FIPS-kompatibilitás a az Egyesült Államok Kormánya?**

   Az Amerikai Egyesült Államokban Federal Information Processing Standard (FIPS) Egyesült Államok használatra jóváhagyott titkosítási algoritmusokat határozza meg Szövetségi kormányzati számítógépes rendszerek bizalmas adatok védelméről. FIPS engedélyezése a Windows server vagy az asztal mód be van állítva az operációs rendszer, hogy csak a FIPS használatával érvényesített kriptográfiai algoritmusok kell használni. Ha egy alkalmazás nem megfelelő algoritmusok használja, az alkalmazások megszakad. With.NET keretrendszer-verziók 4.5.2 vagy újabb rendszerre, az alkalmazás automatikusan vált, a titkosítási algoritmusok FIPS előírásainak megfelelő algoritmusok használata, ha a számítógép FIPS-módban van.

   Microsoft hagyja legfeljebb minden ügyfél határozza meg, hogy engedélyezze a FIPS-módban. Úgy véljük, vonzó miatt nem szükséges az ügyfelek, akik nem tartoznak kormányzati szabályozásaikat, hogy alapértelmezés szerint engedélyezi a FIPS-módban van.

   **Erőforrások**

* [Miért még nem javasoljuk "FIPS-módban" többé](https://blogs.technet.microsoft.com/secguide/2014/04/07/why-were-not-recommending-fips-mode-anymore/)

  Blog cikkben FIPS áttekintést, és elmagyarázza, hogy miért ezek nem engedélyezi az FIPS-módban alapértelmezés szerint.
* [A FIPS 140 érvényesítése](https://technet.microsoft.com/library/cc750357.aspx)

  Ebben a cikkben megtudhatja hogyan Microsoft-termékek és a titkosítási modulok felel meg a FIPS szabványnak esetében az Egyesült Államok Szövetségi Kormánya.
* ["Rendszer-kriptográfia: használata FIPS szabványnak megfelelő algoritmusok használata titkosításhoz, kivonatoláshoz és aláíráshoz" biztonsági beállítások hatások a Windows XP és a Windows újabb verzióiban](https://support.microsoft.com/kb/811833)

  Ez a cikk beszél FIPS-módban a régebbi Windows-számítógépek használatát.
