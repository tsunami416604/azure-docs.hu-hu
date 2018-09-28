---
title: Általános virtuális gép hibakódjai az Azure-ban |} A Microsoft Docs
description: Néhány gyakori hibakódok észlelt, amikor üzembe helyezése és kezelése az Azure virtual machines ismertetése
services: virtual-machines
documentationcenter: ''
author: xujing-ms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure
ms.date: 5/22/2017
ms.author: xujing
ms.openlocfilehash: b996d42e5c543235d09b46d29889bc5eaeafd52a
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47413890"
---
# <a name="understand-common-error-messages-when-you-manage-virtual-machines-in-azure"></a>Gyakori hibaüzenetek ismertetése, ha Ön kezeli az Azure virtual machines

Ez a cikk ismerteti az egyes leggyakoribb hibakódok és üzenetek felmerülhet a létrehozásakor vagy az Azure virtual machines (VM) kezelése.

>[!NOTE]
> Megjegyzéseket ezen az oldalon a visszajelzés vagy keresztül hagyhatja [Azure visszajelzési](https://feedback.azure.com/forums/216843-virtual-machines) #azerrormessage címkével.

## <a name="error-response-format"></a>Hiba történt a válasz formátuma 
Az Azure virtuális gépek hibaválaszt használja a következő JSON formátummal:

```json
{
  "status": "status code",
  "error": {
    "code":"Top level error code",
    "message":"Top level error message",
    "details":[
     {
      "code":"Inner evel error code",
      "message":"Inner level error message"
     }
    ]
   }
}
```

Egy hibaválasz mindig tartalmazza a állapotkódot és a egy hiba objektum. Minden egyes hiba objektum mindig tartalmaz egy hibakódot és a egy üzenetet. A virtuális gép létrehozása sablon alapján, ha a hiba objektum is tartalmaz, amely tartalmaz egy belső szint hibakódokat és -üzenet részletei szakasz. Általában a legtöbb belső hibaüzenet szintje a legfelső szintű hiba.


## <a name="common-virtual-machine-management-errors"></a>Virtuális gép felügyeleti előforduló gyakori hibák

Ez a szakasz ismerteti a Gyakori hibaüzenetek jelentkezhetnek, ha a virtuális gépek kezelése:

|  Hibakód  |  Hibaüzenet  |  
|  :------| :-------------|  
|  AcquireDiskLeaseFailed  |  Nem sikerült beszerezni a bérletet lemez létrehozásakor a(z){0}"blob-URI használatával {1}. A BLOB már használatban van.  |  
|  AllocationFailed  |  A lefoglalás sikertelen. Próbálja meg csökkenteni a virtuális gép méretét vagy a virtuális gépek számát, próbálkozzon újra később, vagy próbáljon üzembe helyezni egy másik rendelkezésre állási csoport vagy más Azure-helyen.  |  
|  AllocationFailed  |  A virtuális gép foglalása belső hiba miatt nem sikerült. Próbálkozzon újra később, vagy próbáljon üzembe helyezni egy másik helyre.  |
|  ArtifactNotFound  |  A Virtuálisgép-bővítmény a közzétevő "{0}"és a típus"{1}"nem található a helyen"{2}".  |
|  ArtifactNotFound  |  Publisher bővítmény "{0}", típus "{1}", típuskezelő verziója "{2}" nem található a bővítménytárban.  |
|  ArtifactVersionNotFound  |  Nem található olyan verzió az összetevőtárban, amely megfelelne a kért verzió "{0}".  |
|  ArtifactVersionNotFound  |  Nem található olyan verzió az összetevőtárban, amely megfelelne a kért verzió "{0}"a Virtuálisgép-bővítmény a közzétevő"{1}"és a típus"{2}".  |
|  AttachDiskWhileBeingDetached  |  Adatlemez csatolása nem "{0}"a virtuális gép"{1}", mert a lemez leválasztása folyamatban. Várjon, amíg a lemez leválasztása befejeződik, és próbálkozzon újra.  |
|  Hibás kérés  |  Igazítva "rendelkezésre állási csoportok még nem támogatottak ebben a régióban.  |
|  Hibás kérés  |  Emellett a nem felügyelt rendelkezésre állási csoportot felügyelt lemezekkel rendelkező virtuális gépek vagy a felügyelt rendelkezésre állási csoport blobalapú lemezekkel rendelkező virtuális gépek hozzáadása nem támogatott. Hozzon létre egy rendelkezésre állási csoportot az "felügyelt" tulajdonság beállítása annak érdekében, hogy a felügyelt lemezekkel rendelkező virtuális gép hozzáadása.  |
|  Hibás kérés  |  A felügyelt lemezek használata nem támogatott ebben a régióban.  |
|  Hibás kérés  |  Kezelőnként több operációs rendszer típusa nem támogatott "{0}". VM-bővítmény a(z){1}"-kezelő"{2}"már hozzá, vagy a megadott bemeneti adatok.  |
|  Hibás kérés  |  Művelet "{0}"nem támogatott az erőforrás"{1}" felügyelt lemezek.  |
|  CertificateImproperlyFormatted  |  A titkos kulcs JSON-reprezentációval lekért {0} olyan rendelkezik, amely nem megfelelően formázott PFX-fájlt, vagy a megadott jelszó nem dekódolja megfelelően a PFX-fájlt.  |
|  CertificateImproperlyFormatted  |  A lekért adatok {0} nem deszerializálható JSON formátumba.  |
|  Ütközés  |  A lemezek átméretezése csak virtuális gép létrehozásakor vagy felszabadított virtuális gép esetén engedélyezett.  |
|  ConflictingUserInput  |  Lemez "{0}"nem lehet csatolni, mert a lemez már a virtuális gép tulajdonosa"{1}".  |
|  ConflictingUserInput  |  A forrásként és a célként megadott erőforráscsoport megegyezik.  |
|  ConflictingUserInput  |  Forrás- és storage-fiókok lemez {0} eltérőek.  |
|  ContainerAlreadyOnLease  |  Már van egy bérletet tárolóra a blob URI-azonosítójú {0}.  |
|  CrossSubscriptionMoveWithKeyVaultResources  |  Áthelyezési kérelem tartalmazza a Key Vault-erőforrásokat, amelyek egy vagy több által hivatkozott {0}s a kérésben. Ez nem támogatott jelenleg a áthelyezése előfizetések közötti. Ellenőrizze a hiba részleteit a KeyVault erőforrás-azonosítóját.  |
|  DiagnosticsOperationInternalError  |  Belső hiba történt a virtuális gép diagnosztikai profiljának feldolgozása során {0}.  |
|  DiskBlobAlreadyInUseByAnotherDisk  |  BLOB {0} már használja egy másik lemez virtuális géphez tartozó "{1}". Tekintse meg a lemez kapcsolódó információk a blob metaadatait.  |
|  DiskBlobNotFound  |  Nem található a VHD-blob URI-azonosítójú {0} lemez "{1}".  |
|  DiskBlobNotFound  |  Nem található a VHD-blob URI-azonosítójú {0}.  |
|  DiskEncryptionKeySecretMissingTags  |  {0} titkos kulcs nem rendelkezik a {1} címkék. Frissítse a titkos kód verziója, adja hozzá a szükséges címkéket, és próbálkozzon újra.  |
|  DiskEncryptionKeySecretUnwrapFailed  |  Titkos kód kicsomagolása {0} kulccsal érték {1} nem sikerült.  |
|  DiskImageNotReady  |  Lemezkép {0} van {1} állapota. Próbálkozzon újra, amikor a lemezkép készen áll.  |
|  DiskPreparationError  |  Egy vagy több hiba történt a Virtuálisgép-lemezek előkészítése során. Tekintse meg a lemez példányait tartalmazó nézetet.  |
|  DiskProcessingError  |  A rendszer leállította a lemez feldolgozását, mert a virtuális gép más lemezei hibásak.  |
|  ImageBlobNotFound  |  Nem található a VHD-blob URI-azonosítójú {0} lemez "{1}".  |
|  ImageBlobNotFound  |  Nem található a VHD-blob URI-azonosítójú {0}.  |
|  IncorrectDiskBlobType  |  Lemezblobok csak lapblob típusú lehet. BLOB {0} lemez "{1}" blokkblob típusú van.  |
|  IncorrectDiskBlobType  |  Lemezblobok csak lapblob típusú lehet. BLOB {0} típusa "{1}".  |
|  IncorrectImageBlobType  |  Lemezblobok csak lapblob típusú lehet. BLOB {0} lemez "{1}" blokkblob típusú van.  |
|  IncorrectImageBlobType  |  Lemezblobok csak lapblob típusú lehet. BLOB {0} típusa "{1}".  |
|  InternalOperationError  |  Nem sikerült feloldani a storage-fiók {0}. Ellenőrizze, hogy azt a számítási erőforrás ugyanazon a helyen a Tárolásierőforrás-szolgáltató használatával hozták létre.  |
|  InternalOperationError  |  {0} célkeresési feladat nem sikerült.  |
|  InternalOperationError  |  Hiba történt a virtuális gép hálózati profiljának érvényesítése során "{0}".  |
|  InvalidAccountType  |  Az AccountType {0} je neplatná.  |
|  InvalidParameter  |  A paraméter értékének {0} je neplatná.  |
|  InvalidParameter  |  A megadott Rendszergazdai jelszó nem engedélyezett.  |
|  InvalidParameter  |  "A megadott jelszónak között kell lennie {0}-{1} karakterből, és meg kell felelniük legalább {2} a jelszó-összetettségi követelményeknek a következők közül: <ol><li> Egy nagybetűs karaktert tartalmaz</li><li>Egy kisbetűs karaktert tartalmaz</li><li>Egy numerikus számjegyet tartalmaz</li><li>Egy speciális karaktert tartalmaz.</li></ol>  |
|  InvalidParameter  |  A megadott Rendszergazdai felhasználónév nem engedélyezett.  |
|  InvalidParameter  |  Ha a virtuális gépet platform vagy felhasználói rendszerkép alapján hozza létre, nem csatolhat hozzá meglévő operációsrendszer-lemezt.  |
|  InvalidParameter  |  Tárolónév {0} je neplatná. A tároló nevének 3 – 63 karakter hosszúságúnak kell lennie, és csak kisbetűs alfanumerikus karaktereket és kötőjelet tartalmazhat. Kötőjel előtt és után alfanumerikus karakternek.  |
|  InvalidParameter  |  Tárolónév {0} URL-CÍMBEN szereplő {1} je neplatná. A tároló nevének 3 – 63 karakter hosszúságúnak kell lennie, és csak kisbetűs alfanumerikus karaktereket és kötőjelet tartalmazhat. Kötőjel előtt és után alfanumerikus karakternek.  |
|  InvalidParameter  |  Az URL-CÍMBEN szereplő blobnév {0} perjelet tartalmaz. Ez jelenleg nem támogatott lemezek esetén.  |
|  InvalidParameter  |  Az URI-t {0} nem tűnik megfelelő BLOB URI-t.  |
|  InvalidParameter  |  Egy lemez neve "{0}" már használja az ugyanahhoz a logikai Egységhez: {1}.  |
|  InvalidParameter  |  A lemez neve "{0}" már létezik.  |
|  InvalidParameter  |  A megadott lemezkép-hivatkozásban már definiált lemezek nem bírálhatók felül felhasználói lemezképekkel.  |
|  InvalidParameter  |  Egy lemez neve "{0}" már használja az ugyanazon virtuális merevlemez URL-cím {1}.  |
|  InvalidParameter  |  A megadott tartalék tartományok száma {0} tartományba kell esnie {1} való {2}.  |
|  InvalidParameter  |  A licenc típusa {0} je neplatná. Érvényes licenctípusok a következők: Windows_Client vagy Windows_Server, kis-és nagybetűket.  |
|  InvalidParameter  |  Linux gazdagép neve nem lehet hosszabb {0} karakter hosszúságúnak és nem tartalmazhatja a következő karaktereket: {1}.  |
|  InvalidParameter  |  Nyilvános Ssh-kulcsokat a cél elérési út jelenleg csak az alapértelmezett értékére {0} a Linux-telepítőügynök ismert hibája miatt.  |
|  InvalidParameter  |  A lemez LUN {0} már létezik.  |
|  InvalidParameter  |  Előfizetés {0} a kérelem meg kell egyeznie az előfizetés {1} szerepel a felügyelt lemez azonosítója.  |
|  InvalidParameter  |  Egyéni adatok OSProfile kell lennie a Base64 kódolást és maximális hosszúságú {0} karakter.  |
|  InvalidParameter  |  URL-CÍMBEN szereplő blobnév {0} kell végződnie:{1}"kiterjesztése.  |
|  InvalidParameter  |  {0}"nem egy érvényes rögzített VHD blobnév előtagja. Egy érvényes előtagot blobnévelőtagjaként "{1}".  |
|  InvalidParameter  |  Tanúsítványok nem adható hozzá a virtuális géphez, ha a Virtuálisgép-ügynök nincs telepítve.  |
|  InvalidParameter  |  A lemez LUN {0} már létezik.  |
|  InvalidParameter  |  Nem sikerült létrehozni a virtuális Gépet, mivel a kért méret {0} nem érhető el a fürt, ahol a rendelkezésre állási csoport jelenleg lefoglalt. Az elérhető méretek a következők: {1}. Információ a virtuális gépek átméretezésének stratégiájával címen olvasható https://aka.ms/azure-resizevm.  |
|  InvalidParameter  |  A kért Virtuálisgép-méret {0} nem érhető el a jelenlegi régióban. A jelenlegi régióban elérhető méretek a következők: {1}. További információ az elérhető Virtuálisgép-méretekről, minden régióban található https://aka.ms/azure-regions.  |
|  InvalidParameter  |  A kért Virtuálisgép-méret {0} nem érhető el a jelenlegi régióban. További információ az elérhető Virtuálisgép-méretekről, minden régióban található https://aka.ms/azure-regions.  |
|  InvalidParameter  |  Windows-Rendszergazda felhasználóneve nem lehet több mint {0} karakter hosszú, végződhet vagy tartalmazhatja a következő karaktereket: {1}.  |
|  InvalidParameter  |  Windows-számítógép neve nem lehet több mint {0} karakter hosszú, nem állhat csupán számokból, vagy tartalmazhatja a következő karaktereket: {1}.  |
|  MissingMoveDependentResources  |  Áthelyezési kérelem nem tartalmaz a függő erőforrásokat. Ellenőrizze a hiba részletei az erőforrás-azonosítók hiányoznak.  |
|  MoveResourcesHaveInvalidState  |  Az erőforrás-áthelyezési kérelem érvénytelen tárfiókokhoz társított virtuális gépeket tartalmaz. Ellenőrizze az ezen erőforrás-azonosítók és a hivatkozott tárfiókneveket.  |
|  MoveResourcesHavePendingOperations  |  Áthelyezési kérelem tartalmazza az erőforrás, amelynek egy művelet folyamatban. Ellenőrizze az erőforrások azonosítóit a részletekben. Próbálja megismételni a műveletet a függőben lévő műveletek befejeződése után.  |
|  MoveResourcesNotFound  |  Áthelyezési kérelem olyan erőforrásokat tartalmaz, nem található. Ellenőrizze az erőforrások azonosítóit a részletekben.  |
|  NetworkingInternalOperationError  |  Ismeretlen hálózatfoglalási hiba.  |
|  NetworkingInternalOperationError  |  Ismeretlen hálózatfoglalási hiba  |
|  NetworkingInternalOperationError  |  Belső hiba történt a virtuális gép hálózati profiljának feldolgozása során.  |
|  NotFound  |  A rendelkezésre állási csoport {0} nem található.  |
|  NotFound  |  A forrás virtuális gép "{0}" az a kérelem nem létezik az Azure-beli helyen.  |
|  NotFound  |  Azonosítóval rendelkező bérlő {0} nem található.  |
|  NotFound  |  A kép {0} nem található.  |
|  NotSupported  |  A licenc típusa {0}, de a szülőlemezkép {1} nem a helyszínről.  |
|  OperationNotAllowed  |  Rendelkezésre állási csoport {0} nem lehet törölni. Rendelkezésre állási csoport törlése előtt győződjön meg arról, hogy minden virtuális gép nem tartalmaz.  |
|  OperationNotAllowed  |  A rendelkezésre állási csoport módosítása "Klasszikus" a "Igazított" SKU nem engedélyezett.  |
|  OperationNotAllowed  |  A virtuális gép bővítményei nem módosíthatók, ha a virtuális gép nem fut.  |
|  OperationNotAllowed  |  A rögzítési művelet csak blobalapú lemezekkel rendelkező virtuális gépeken támogatott. A rendszerkép-erőforráshoz tartozó API-k használatával hozzon létre egy képet a felügyelt virtuális gépről.  |
|  OperationNotAllowed  |  Az erőforrás {0} nem hozható létre rendszerképből {1} mindaddig, amíg a rendszerkép sikeresen létrejött.  |
|  OperationNotAllowed  |  Frissítések encryptionsettings elem nem engedélyezett, ha a virtuális gép van lefoglalva, próbálkozzon újra, miután a virtuális gép fel van szabadítva.  |
|  OperationNotAllowed  |  A felügyelt lemezek egy blobalapú lemezekkel rendelkező virtuális géphez való hozzáadása nem támogatott.  |
|  OperationNotAllowed  |  Az ilyen méretű virtuális gépekhez csatolható adatlemezek maximális száma {0}.  |
|  OperationNotAllowed  |  A blobalapú lemezek egy felügyelt lemezekkel rendelkező virtuális géphez való hozzáadása nem támogatott.  |
|  OperationNotAllowed  |  A művelet "{0}"nem engedélyezett a lemezkép"{1}" mivel a lemezkép meg van jelölve törlésre. Ön csak a törlési művelettel próbálkozhat újra (vagy megvárhatja, amíg folyamatban lévő befejeződik).  |
|  OperationNotAllowed  |  A művelet "{0}"nem engedélyezett a virtuális gép"{1}" mivel a virtuális gép általánosítva van.  |
|  OperationNotAllowed  |  A művelet "{0}"nem engedélyezett, mert Visszaállításipont-gyűjtemény"{1}" törlésre van kijelölve.  |
|  OperationNotAllowed  |  A művelet "{0}"nem engedélyezett a Virtuálisgép-bővítmény"{1}", mivel azt törlésre van kijelölve. Ön csak a törlési művelettel próbálkozhat újra (vagy megvárhatja, amíg folyamatban lévő befejeződik).  |
|  OperationNotAllowed  |  A művelet "{0}"nem engedélyezett a virtuális gépek"{1}"lettek létrehozva a lemezképpel"{2}".  |
|  OperationNotAllowed  |  A művelet "{0}"nem engedélyezett a virtuálisgép-méretezési csoport"{1}"a lemezkép jelenleg használ"{2}".  |
|  OperationNotAllowed  |  A művelet "{0}"nem engedélyezett a virtuális gép"{1}" mivel a virtuális gép ki van jelölve törlésre. Ön csak a törlési művelettel próbálkozhat újra (vagy megvárhatja, amíg folyamatban lévő befejeződik).  |
|  OperationNotAllowed  |  A művelet "{0}"nem engedélyezett a virtuális gép"{1}" mivel a virtuális gép nincs lefoglalva, vagy meg van jelölve felszabadításra.  |
|  OperationNotAllowed  |  A művelet "{0}"nem engedélyezett a virtuális gép"{1}" mivel a virtuális gép fut-e. Kapcsolja ki explicit módon abban az esetben, ha leállítja a virtuális Gépet a vendég operációs rendszeren belül.  |
|  OperationNotAllowed  |  A művelet "{0}"nem engedélyezett a virtuális gép"{1}" mivel a virtuális gép nem fel van szabadítva.  |
|  OperationNotAllowed  |  A művelet "{0}"nem engedélyezett a virtuális gép"{1}"Mivel a virtuális gép kiterjesztése"{2}" hibás állapotban.  |
|  OperationNotAllowed  |  Művelet "{0}"nem engedélyezett a virtuális gép"{1}: mivel egy másik művelet van folyamatban.  |
|  OperationNotAllowed  |  A művelet "{0}"van szükség a virtuális gép"{1}" általánosítása.  |
|  OperationNotAllowed  |  A művelethez a virtuális gépnek futnia kell (vagy be kell lennie állítva futásra).  |
|  OperationNotAllowed  |  -Lemezének mérete {0}GB, amely kisebb, mint {1}GB-nyi, lemezképben lévő megfelelő lemez nem engedélyezett.  |
|  OperationNotAllowed  |  Kezelőhöz tartozó virtuális gépek méretezési csoportjának bővítményei "{0}" is adhatók hozzá a virtuális gépek méretezési csoportjának létrehozásakor.  |
|  OperationNotAllowed  |  Kezelőhöz tartozó virtuális gépek méretezési csoportjának bővítményei "{0}" csak a virtuális gépek méretezési csoportjának törlésekor törölhetők.  |
|  OperationNotAllowed  |  Virtuális gép "{0}" már felügyelt lemezeket használ.  |
|  OperationNotAllowed  |  Virtuális gép "{0}"a "Klasszikus" rendelkezésre állási csoporthoz tartozik"{1}". Frissítse a rendelkezésre állási csoportban "Igazított" SKU használja, és próbálkozzon újra az átalakítást.  |
|  OperationNotAllowed  |  Lemezképből létrehozott virtuális gép blobalapú lemezekkel rendelkezhet. Az összes lemez kell lenniük a felügyelt lemezeket.  |
|  OperationNotAllowed  |  A rögzítési művelet nem hajtható végre, mert a virtuális gép nincs általánosítva.  |
|  OperationNotAllowed  |  Műveletek a virtuális gép "{0}" engedélyezettek, mert a Virtuálisgép-lemezek konvertálása a managed Disks szolgáltatásba.  |
|  OperationNotAllowed  |  Egy folyamatban lévő művelet módosul a virtuális gép energiaállapotát {0} való {1}. Hajtsa végre a műveletet {2} egy kis idő múlva.  |
|  OperationNotAllowed  |  Nem lehet hozzáadni, vagy frissítse a virtuális Gépet. A kért Virtuálisgép-méret {0} nem lehet elérhető a meglévő foglalási egységben. Információ a virtuális gépek átméretezésének stratégiájával címen olvasható https://aka.ms/azure-resizevm.  |
|  OperationNotAllowed  |  Nem sikerült átméretezni a virtuális Gépet, mert a kért méret {0} nem érhető el a fürt, ahol a rendelkezésre állási csoport jelenleg lefoglalt. Az elérhető méretek a következők: {1}. Információ a virtuális gépek átméretezésének stratégiájával címen olvasható https://aka.ms/azure-resizevm.  |
|  OperationNotAllowed  |  Nem sikerült átméretezni a virtuális Gépet, mert a kért méret {0} nem érhető el a fürt, ahol a virtuális gép jelenleg lefoglalt. A virtuális gép átméretezése {1} szabadítsa fel (Ez a leállítási művelet az Azure Portalon), és próbálkozzon újra az átméretezés. Információ a virtuális gépek átméretezésének stratégiájával címen olvasható https://aka.ms/azure-resizevm.  |
|  OSProvisioningClientError  |  Virtuális gép operációs rendszerének kiépítése nem sikerült "{0}", mert a vendég operációs rendszer kiépítése folyamatban van.  |
|  OSProvisioningClientError  |  Virtuális gép operációs rendszerének kiépítése "{0}" nem sikerült. A hiba részletei: {1} győződjön meg arról, hogy a lemezkép megfelelően lett előkészítve (általánosítva). <ul><li>Windows utasításokat: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/  </li></ul> |
|  OSProvisioningClientError  |  SSH-állomáskulcs létrehozása nem sikerült. A hiba részletei: {0}. Oldja meg a probléma ellenőrizze-e ha Linux-ügynök megfelelően van-e beállítva. <ul><li>Ellenőrizheti a webhelyen található utasításokat: https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux/ </li></ul> |
|  OSProvisioningClientError  |  A virtuális Géphez megadott felhasználónév érvénytelen ezen Linux-disztribúció. A hiba részletei: {0}.  |
|  OSProvisioningInternalError  |  Virtuális gép operációs rendszerének kiépítése nem sikerült "{0}" belső hiba miatt.  |
|  OSProvisioningTimedOut  |  Virtuális gép operációs rendszerének kiépítése "{0}" a megengedett időn belül nem fejeződött be. Előfordulhat, hogy a virtuális gép továbbra is be a sikeres kiépítés. Ellenőrizze később a telepítés állapotát.  |
|  OSProvisioningTimedOut  |  Virtuális gép operációs rendszerének kiépítése "{0}" a megengedett időn belül nem fejeződött be. Előfordulhat, hogy a virtuális gép továbbra is be a sikeres kiépítés. Ellenőrizze később a telepítés állapotát. Ellenőrizzük, hogy a lemezkép megfelelően lett előkészítve (általánosítva).   <ul><li>Windows utasításokat: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Útmutatás Linux rendszerhez: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OSProvisioningTimedOut  |  Virtuális gép operációs rendszerének kiépítése "{0}" a megengedett időn belül nem fejeződött be. Azonban a virtuális gép vendégügynökének futó volt észlelhető. Ez azt sugallja, hogy a vendég operációs rendszer nem lett megfelelően előkészített Virtuálisgép-lemezképként kell használni (a CreateOption = FromImage). A probléma megoldásához, vagy használjon a virtuális Merevlemezt, mert createoption = Attach vagy készítse elő megfelelően képként való használathoz:   <ul><li>Windows utasításokat: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Útmutatás Linux rendszerhez: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OverConstrainedAllocationRequest  |  A virtuális gép előírt mérete jelenleg nem érhető el a kijelölt helyszínen.  |
|  ResourceUpdateBlockedOnPlatformUpdate  |  Erőforrás platform folyamatban lévő frissítése miatt jelenleg nem lehet frissíteni. Később próbálja meg újra.  |
|  StorageAccountLimitation  |  Storage-fiók "{0}" nem támogatja a lapblobokat, melyek szükségesek a lemezek létrehozásához.  |
|  StorageAccountLimitation  |  Storage-fiók "{0}" processzorhasználata túllépte a számára lefoglalt kvótát.  |
|  StorageAccountLocationMismatch  |  Nem sikerült feloldani a storage-fiók {0}. Ellenőrizze, hogy azt a számítási erőforrás ugyanazon a helyen a Tárolásierőforrás-szolgáltató használatával hozták létre.  |
|  StorageAccountNotFound  |  Storage-fiók {0} nem található. Ellenőrizze a tárfiók nincs törölve, és a virtuális Gépet az azonos Azure-beli helyhez tartozik.  |
|  StorageAccountNotRecognized  |  Tárolásierőforrás-szolgáltató által kezelt tárfiókot használja. Felhasználása {0} nem támogatott.  |
|  StorageAccountOperationInternalError  |  Belső hiba történt a tárfiók elérése során {0}.  |
|  StorageAccountSubscriptionMismatch  |  Storage-fiók {0} nem tartozik előfizetés {1}.  |
|  StorageAccountTooBusy  |  Storage-fiók "{0}" jelenleg túlzottan elfoglalt. Fontolja meg egy másik fiókot.  |
|  StorageAccountTypeNotSupported  |  Lemez {0} használ {1} Ez a Blob storage-fiók. Próbálkozzon újra egy általános célú tárfiókkal.  |
|  StorageAccountTypeNotSupported  |  Storage-fiók {0} azonban {1} típusa. Rendszerindítási diagnosztika támogatja {2} tárfiókok típusairól.  <ul><li>Ez a hiba akkor fordul elő, ha a premium storage-fiókot használ a rendszerindítási diagnosztika. További információkért lásd: [rendszerindítási diagnosztika használata](boot-diagnostics.md). </li></ul> |
|  SubscriptionNotAuthorizedForImage  |  Az előfizetés nem rendelkezik megfelelő jogosultsággal.  |
|  TargetDiskBlobAlreadyExists  |  BLOB {0} már létezik. Adjon meg egy másik blob URI-t hozzon létre egy új üres adatlemez a(z){1}".  |
|  TargetDiskBlobAlreadyExists  |  Rögzítési művelet nem folytatható, mert a cél lemezképet blob {0} már létezik, és a VHD-blobok felülírására vonatkozó jelölő nincs beállítva. Vagy törölje a blobot, vagy állítsa be azt a jelzőt, a VHD-blobok felülírására, és próbálkozzon újra.  |
|  TargetDiskBlobAlreadyExists  |  Rögzítési művelet nem folytatható, mert a cél lemezképet blob {0} aktív bérletre van rajta.   |
|  TargetDiskBlobAlreadyExists  |  BLOB {0} már létezik. Adjon meg egy másik blob URI célként lemez "{1}".  |
|  TooManyVMRedeploymentRequests  |  Virtuális gép újbóli üzembe helyezés túl sok kérést kapott "{0}" vagy a virtuális gép az azonos rendelkezésre állási csoport a virtuális géppel. Próbálkozzon újra később.  |
|  VHDSizeInvalid  |  A megadott lemezméretérték {0} lemez "{1}" blob- {2} je neplatná. Lemezméretek között kell lennie {3} és {4}.  |
|  VMAgentStatusCommunicationError  |  Virtuális gép "{0}" nem jelentette a Virtuálisgép-ügynök vagy a bővítmény állapotát. Ellenőrizze, hogy a virtuális gép futó Virtuálisgép-ügynökkel rendelkezik, és az Azure storage kimenő kapcsolatokat létesíthet.  |
|  VMArtifactRepositoryInternalError  |  Hiba történt az összetevőtárral folytatott kommunikáció során, amely a virtuális gép összetevőadatainak lekérésére irányult.  |
|  VMArtifactRepositoryInternalError  |  Belső hiba történt a virtuális gép összetevőadatainak az összetevőtárból való lekérésére során.  |
|  VMExtensionHandlerNonTransientError  |  Kezelő "{0}"Virtuálisgép-bővítmény hibát jelzett a(z)"{1}"a terminál hibakódja:{2}", hibaüzenet:"{3}"  |
|  VMExtensionManagementInternalError  |  Belső hiba történt a Virtuálisgép-bővítmény feldolgozása során a(z){0}".  |
|  VMExtensionManagementInternalError  |  A Virtuálisgép-bővítmények előkészítése során több hiba történt. Tekintse meg a virtuális gép bővítmény példányait tartalmazó nézetet.  |
|  VMExtensionProvisioningError  |  Virtuális gép hibát jelzett bővítmény feldolgozása során a(z){0}". Hibaüzenet: "{1}".  |
|  VMExtensionProvisioningError  |  Több Virtuálisgép-bővítmény telepítése sikertelen volt a virtuális gépen. Tekintse át a virtuális gép bővítmény példányait tartalmazó nézetet.  |
|  VMExtensionProvisioningTimeout  |  Virtuálisgép-bővítmény kiépítési "{0}" túllépte az időkorlátot. A bővítmény telepítése előfordulhat, hogy túl sokáig tart, vagy nem sikerült beolvasni a bővítmény állapotát.  |
|  VMMarketplaceInvalidInput  |  Hoz létre virtuális gépet egy nem Marketplace-lemezképből nem szükség Csomagadatokra távolítsa el a csomagadatokat a kérésben. Az operációs rendszer lemezének neve {0}.  |
|  VMMarketplaceInvalidInput  |  A vásárlási információk nem egyezik. Nem sikerült a Marketplace-lemezképből üzembe helyezéséhez. Az operációs rendszer lemezének neve {0}.  |
|  VMMarketplaceInvalidInput  |  A kérelemben szereplő információk hoz létre virtuális gépet a Marketplace-lemezképből van szükség. Az operációs rendszer lemezének neve {0}.  |
|  VMNotFound  |  A virtuális gép "{0}" nem található.  |
|  VMRedeploymentFailed  |  Virtuális gép "{0}" újbóli üzembe helyezése belső hiba miatt nem sikerült. Próbálkozzon újra később.  |
|  VMRedeploymentTimedOut  |  Virtuális gép újbóli üzembe helyezés "{0}" a megengedett időn belül nem fejeződött be. Még befejeződhet sikeresen a. Más esetben megismételheti a kérelmet.  |
|  VMStartTimedOut  |  Virtuális gép "{0}" a megengedett időn belül nem indult el. A virtuális gép továbbra is lehetséges, hogy sikerült elindítani. Ellenőrizze később a kiemelt állapotát.  |


## <a name="next-steps"></a>További lépések
Ha további segítségre van szüksége, forduljon az Azure-szakértőket a [az MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatja az első**.