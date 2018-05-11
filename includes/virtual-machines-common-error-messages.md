---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-windows
author: genlin
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 04/25/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: db241c1a3c8bfd15e13ae0bd9f1cdf4c92c7081d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
>[!NOTE]
> Ezen a lapon, a visszajelzés vagy a megjegyzések hagyhatja [Azure visszajelzés](https://feedback.azure.com/forums/216843-virtual-machines) #azerrormessage címkével.

## <a name="error-response-format"></a>Hiba történt a válasz formátuma 
Azure virtuális gépek hibaválaszba használja a következő JSON formátummal:

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

Egy hiba történt egy válasz mindig tartalmazza a állapotkódot és hiba objektum. Minden egyes hiba objektum mindig tartalmazza a hibakódot és egy üzenetet. A virtuális Gépet a sablon jön létre, ha a hiba objektum is tartalmaz, amely egy belső szintű hibakódok és az üzenet tartalmaz egy részletes adatait tartalmazó részben. Általában a legtöbb belső hibaüzenet szintje a legfelső szintű sikertelen.


## <a name="common-virtual-machine-management-errors"></a>Általános virtuális gép felügyeleti hibák

Ez a szakasz a leggyakoribb hibaüzenetek jelentkezhetnek, ha a virtuális gépek kezelése:

|  Hibakód  |  Hibaüzenet  |  
|  :------| :-------------|  
|  AcquireDiskLeaseFailed  |  Nem sikerült bérleti jogot szerezni lemez létrehozása során "{0}" blob URI-azonosítójú használatával {1}. A BLOB már használatban van.  |  
|  AllocationFailed  |  A lefoglalás sikertelen. Próbálja meg csökkenteni a virtuális gép méretét vagy a virtuális gépek számát, próbálkozzon újra később, vagy próbálkozzon egy másik rendelkezésre állási csoportban vagy más Azure-beli hely telepítése.  |  
|  AllocationFailed  |  A virtuális gép foglalása belső hiba miatt sikertelen. Próbálkozzon újra később, vagy próbálja üzembe helyezni a másik helyet.  |
|  ArtifactNotFound  |  A Virtuálisgép-bővítmény a közzétevő "{0}"és típusa"{1}"nem található a helyen"{2}".  |
|  ArtifactNotFound  |  A publisher bővítmény "{0}", típus "{1}", típuskezelő verziója "{2}" nem található a bővítménytárban.  |
|  ArtifactVersionNotFound  |  Nem található olyan verzió az összetevőtárban, amely megfelelne a kért verzió "{0}".  |
|  ArtifactVersionNotFound  |  Nem található olyan verzió az összetevőtárban, amely megfelelne a kért verzió "{0}"a Virtuálisgép-bővítmény a közzétevő"{1}"és típusa"{2}".  |
|  AttachDiskWhileBeingDetached  |  Nem lehet adatlemezt csatolni "{0}"a virtuális gép"{1}", mert a lemez van jelenleg leválasztás alatt áll. Várjon, amíg a lemez leválasztása, és próbálkozzon újra.  |
|  Hibás kérés  |  Illesztett "rendelkezésre állási készletek még nem támogatottak ebben a régióban.  |
|  Hibás kérés  |  A virtuális gépek a rendelkezésre állási csoport nem felügyelt kezelt lemezek hozzáadása vagy a virtuális gép és a felügyelt rendelkezésre állási csoport blobalapú lemezek hozzáadása nem támogatott. Hozzon létre egy rendelkezésre állási csoport "kezelt" tulajdonsága felügyelt lemezzel rendelkező virtuális gép hozzá.  |
|  Hibás kérés  |  A felügyelt lemezek használata nem támogatott ebben a régióban.  |
|  Hibás kérés  |  Kezelőnként több operációs rendszer típusa nem támogatott "{0}". VMExtension "{1}"with handler"{2}" már hozzáadott vagy a megadott bemeneti adatok.  |
|  Hibás kérés  |  A művelet "{0}"nem támogatott az erőforrás"{1}" felügyelt lemezzel.  |
|  CertificateImproperlyFormatted  |  A titkos kulcs JSON-megjelenítés lekért {0} egy data mező, amely nem megfelelően formázott PFX-fájlba van, vagy a megadott jelszó nem dekódolja megfelelően a PFX-fájlból.  |
|  CertificateImproperlyFormatted  |  Az adatforrásból származó {0} nem deszerializálható JSON formátumba.  |
|  Ütközés  |  A lemezek átméretezése csak virtuális gép létrehozásakor vagy felszabadított virtuális gép esetén engedélyezett.  |
|  ConflictingUserInput  |  Lemez "{0}"nem lehet csatolni, mert a lemez már van tulajdonosa a virtuális gép"{1}".  |
|  ConflictingUserInput  |  A forrásként és a célként megadott erőforráscsoport megegyezik.  |
|  ConflictingUserInput  |  Forrás és cél tárfiókjai {0} eltérőek.  |
|  ContainerAlreadyOnLease  |  Már létezik a címbérlet URI rendelkező blobot tartalmazó tárolóra {0}.  |
|  CrossSubscriptionMoveWithKeyVaultResources  |  Az áthelyezési kérelem tartalmazza a Key Vault-erőforrásokat, amelyek egy vagy több által hivatkozott {0}s a kérelemben. Ez nem támogatott a jelenleg Áthelyezés az előfizetések közötti. Ellenőrizze a hiba részleteit a KeyVault erőforrás-azonosítók.  |
|  DiagnosticsOperationInternalError  |  Belső hiba történt a virtuális gép diagnosztikai profiljának feldolgozása során {0}.  |
|  DiskBlobAlreadyInUseByAnotherDisk  |  A BLOB {0} már használja egy másik virtuális Géphez tartozó lemez által "{1}". Láthatja, hogy a lemez útmutatót a blob metaadatait.  |
|  DiskBlobNotFound  |  Nem található a VHD-blob URI-azonosítójú {0} lemez "{1}".  |
|  DiskBlobNotFound  |  Nem található a VHD-blob URI-azonosítójú {0}.  |
|  DiskEncryptionKeySecretMissingTags  |  {0} titkos kulcs nem rendelkezik a {1} címkék. Frissítse a titkos, adja hozzá a szükséges címkéket, és próbálja meg újra.  |
|  DiskEncryptionKeySecretUnwrapFailed  |  Titkos kulcs való kibontása {0} kulccsal érték {1} nem sikerült.  |
|  DiskImageNotReady  |  Lemezképfájl {0} van {1} állapotát. Próbálkozzon újra, amikor készen áll a lemezképet.  |
|  DiskPreparationError  |  Egy vagy több hiba történt a Virtuálisgép-lemezek előkészítése során. Tekintse meg a lemez példányait tartalmazó nézetet.  |
|  DiskProcessingError  |  A rendszer leállította a lemez feldolgozását, mert a virtuális gép más lemezei hibásak.  |
|  ImageBlobNotFound  |  Nem található a VHD-blob URI-azonosítójú {0} lemez "{1}".  |
|  ImageBlobNotFound  |  Nem található a VHD-blob URI-azonosítójú {0}.  |
|  IncorrectDiskBlobType  |  Az oldalakra vonatkozó blob típusú lemezblobok csak lehet. A BLOB {0} lemez "{1}" blokkblob típusú van.  |
|  IncorrectDiskBlobType  |  Az oldalakra vonatkozó blob típusú lemezblobok csak lehet. A BLOB {0} típusa "{1}".  |
|  IncorrectImageBlobType  |  Az oldalakra vonatkozó blob típusú lemezblobok csak lehet. A BLOB {0} lemez "{1}" blokkblob típusú van.  |
|  IncorrectImageBlobType  |  Az oldalakra vonatkozó blob típusú lemezblobok csak lehet. A BLOB {0} típusa "{1}".  |
|  InternalOperationError  |  Nem sikerült feloldani a tárfiók {0}. Ellenőrizze, hogy a számítási erőforrással azonos helyen a tárolási erőforrás-szolgáltató használatával hozták létre.  |
|  InternalOperationError  |  {0} célkeresési feladat sikertelen volt.  |
|  InternalOperationError  |  Hiba történt a virtuális gép hálózati profiljának érvényesítése során "{0}".  |
|  InvalidAccountType  |  Az AccountType {0} érvénytelen.  |
|  InvalidParameter  |  A paraméter értékének {0} érvénytelen.  |
|  InvalidParameter  |  A megadott Rendszergazdai jelszó nem engedélyezett.  |
|  InvalidParameter  |  "A megadott jelszó között kell lennie {0}-{1} karakterből állnak, és meg kell felelnie a legalább {2} összetettségi követelménynek a következő: <ol><li> Nagybetűs karaktert tartalmaz</li><li>A kisbetűs karaktert tartalmaz</li><li>Egy numerikus számjegyet tartalmaz</li><li>Speciális karaktert tartalmaz.</li></ol>  |
|  InvalidParameter  |  A megadott Rendszergazdai felhasználónév nem engedélyezett.  |
|  InvalidParameter  |  Ha a virtuális gépet platform vagy felhasználói rendszerkép alapján hozza létre, nem csatolhat hozzá meglévő operációsrendszer-lemezt.  |
|  InvalidParameter  |  Tároló neve {0} érvénytelen. A tároló neve 3 – 63 karakter hosszúságúnak kell lennie, és csak kisbetűs alfanumerikus karaktereket és kötőjelet tartalmazhat. Kötőjel előtt és után alfanumerikus karakternek.  |
|  InvalidParameter  |  Tároló neve {0} URL-címben {1} érvénytelen. A tároló neve 3 – 63 karakter hosszúságúnak kell lennie, és csak kisbetűs alfanumerikus karaktereket és kötőjelet tartalmazhat. Kötőjel előtt és után alfanumerikus karakternek.  |
|  InvalidParameter  |  A blob nevének URL-címben {0} perjelet tartalmaz. Ez jelenleg nem támogatott a lemezeket.  |
|  InvalidParameter  |  Az URI {0} nem tűnik megfelelő BLOB URI.  |
|  InvalidParameter  |  A lemez neve "{0}" már használja ugyanezt a LUN: {1}.  |
|  InvalidParameter  |  A lemez neve "{0}" már létezik.  |
|  InvalidParameter  |  A megadott lemezkép-hivatkozásban már definiált lemezek nem bírálhatók felül felhasználói lemezképekkel.  |
|  InvalidParameter  |  A lemez neve "{0}" már használja a virtuális merevlemez URL-CÍMÉRE {1}.  |
|  InvalidParameter  |  A tartalék tartományok megadott számának {0} között kell lennie {1} való {2}.  |
|  InvalidParameter  |  A licenc típusa {0} érvénytelen. Érvényes licenctípusok a következők: Windows_Client vagy Windows_Server, és a nagybetűk között.  |
|  InvalidParameter  |  Linux gazdagép neve nem lehet hosszabb {0} karakternél, és nem tartalmazhatja a következő karaktereket: {1}.  |
|  InvalidParameter  |  Cél elérési útja az Ssh nyilvános kulcsok célja jelenleg csak az alapértelmezett értékre {0} a Linux-telepítőügynök ismert hibája miatt.  |
|  InvalidParameter  |  A logikai lemez {0} már létezik.  |
|  InvalidParameter  |  Előfizetés {0} a kérelem meg kell egyeznie az előfizetés {1} a felügyelt lemezazonosítóban szereplő.  |
|  InvalidParameter  |  Lehet, hogy OSProfile szereplő egyéni adatoknak a Base64 kódolást és maximális hosszúságú {0} karaktereket.  |
|  InvalidParameter  |  URL-címben a BLOB nevének {0} kell végződnie "{1}" kiterjesztéssel.  |
|  InvalidParameter  |  {0}"nem egy érvényes rögzített VHD blob előtagja van. Egy érvényes előtagnak meg regex "{1}".  |
|  InvalidParameter  |  Tanúsítványok nem vehető fel a virtuális Gépet, ha a Virtuálisgép-ügynök nincs telepítve.  |
|  InvalidParameter  |  A logikai lemez {0} már létezik.  |
|  InvalidParameter  |  Nem hozható létre a virtuális Gépet, mert a kért méret {0} nem érhető el a fürt, ahol a rendelkezésre állási csoport jelenleg foglalt. Az elérhető értékek: {1}. Olvassa el a következő stratégia átméretezése további a virtuális gép https://aka.ms/azure-resizevm.  |
|  InvalidParameter  |  A kért Virtuálisgép-méret {0} nem érhető el az aktuális régióban. A jelenlegi régióban elérhető méret: {1}. További tudnivalók a rendelkezésre álló Virtuálisgép-méretek a minden régióban, https://aka.ms/azure-regions.  |
|  InvalidParameter  |  A kért Virtuálisgép-méret {0} nem érhető el az aktuális régióban. További tudnivalók a rendelkezésre álló Virtuálisgép-méretek a minden régióban, https://aka.ms/azure-regions.  |
|  InvalidParameter  |  Windows Rendszergazda felhasználóneve nem lehet több mint {0} karakter hosszú, végződhet ponttal (.), vagy tartalmazhatja a következő karaktereket: {1}.  |
|  InvalidParameter  |  Windows-számítógép neve nem lehet több mint {0} karakter hosszú, nem állhat csak számokból, vagy tartalmazhatja a következő karaktereket: {1}.  |
|  MissingMoveDependentResources  |  Az áthelyezési kérelem nem tartalmaz a tőle függő összes erőforrásról. Ellenőrizze a hiba részleteit a hiányzó erőforrás-azonosítók.  |
|  MoveResourcesHaveInvalidState  |  Az áthelyezési kérelem tartalmazza a virtuális gépek, amely érvénytelen storage-fiókok kapcsolódik. A részletekben ezen erőforrás-azonosítók és a hivatkozott tárfiókok neve.  |
|  MoveResourcesHavePendingOperations  |  Az áthelyezési kérelem tartalmazza az erőforrásokat, amelyhez egy művelet folyamatban. Részletekben ezen erőforrás-azonosítók. Próbálja megismételni a műveletet, a függőben lévő műveletek befejeződése után.  |
|  MoveResourcesNotFound  |  Az áthelyezési kérelem olyan erőforrásokat tartalmaz, nem található. Részletekben ezen erőforrás-azonosítók.  |
|  NetworkingInternalOperationError  |  Ismeretlen hálózatfoglalási hiba.  |
|  NetworkingInternalOperationError  |  Ismeretlen hálózatfoglalási hiba  |
|  NetworkingInternalOperationError  |  Belső hiba történt a virtuális gép hálózati profiljának feldolgozása során.  |
|  notFound  |  A rendelkezésre állási csoport {0} nem található.  |
|  notFound  |  A forrás virtuális gép "{0}" a megadott kérelem nem létezik ezen a helyen az Azure.  |
|  notFound  |  Azonosítóval rendelkező bérlő {0} nem található.  |
|  notFound  |  A kép {0} nem található.  |
|  NotSupported  |  A licenc típusa {0}, de a szülőlemezkép {1} nincs a helyszíni.  |
|  OperationNotAllowed  |  A rendelkezésre állási csoport {0} nem lehet törölni. Rendelkezésre állási csoport törlése előtt győződjön meg arról, hogy a virtuális gép nem tartalmaz.  |
|  OperationNotAllowed  |  A rendelkezésre állási csoport "Klasszikus" a "Igazított" Termékváltozat nem módosítható.  |
|  OperationNotAllowed  |  A virtuális gép bővítményei nem módosíthatók, ha a virtuális gép nem fut.  |
|  OperationNotAllowed  |  A rögzítési művelet csak egy virtuális gépen a blobalapú lemezek támogatott. Egy felügyelt virtuális gépről a képfájl létrehozásához használja a "Kép" erőforrás API-k.  |
|  OperationNotAllowed  |  Az erőforrás {0} nem lemezkép alapján jön létre {1} amíg lemezkép létrehozása sikeresen befejeződött.  |
|  OperationNotAllowed  |  Az encryptionsettings nem engedélyezett, ha a virtuális gép le van foglalva. Próbálkozzon újra, miután a virtuális gép felszabadítása  |
|  OperationNotAllowed  |  A felügyelt lemezek egy blobalapú lemezekkel rendelkező virtuális géphez való hozzáadása nem támogatott.  |
|  OperationNotAllowed  |  Az adatlemezek ekkora egy virtuális géphez csatolása engedélyezett maximális száma {0}.  |
|  OperationNotAllowed  |  A blobalapú lemezek egy felügyelt lemezekkel rendelkező virtuális géphez való hozzáadása nem támogatott.  |
|  OperationNotAllowed  |  A művelet "{0}"nem engedélyezett a lemezkép"{1}" óta a kép be van jelölve törlésre. Csak próbálkozhat a törlési művelet (vagy várjon, amíg a folyamatban lévő egyikét végrehajtásához).  |
|  OperationNotAllowed  |  A művelet "{0}"nem engedélyezett a virtuális gép"{1}", mert a virtuális gép általánosítva van.  |
|  OperationNotAllowed  |  A művelet "{0}"nem adható meg a helyreállítási pont gyűjtemény"{1}" van jelölve törlésre.  |
|  OperationNotAllowed  |  A művelet "{0}"nem engedélyezett a Virtuálisgép-bővítmény"{1}" mert meg van jelölve törlésre. Csak próbálkozhat a törlési művelet (vagy várjon, amíg a folyamatban lévő egyikét végrehajtásához).  |
|  OperationNotAllowed  |  A művelet "{0}"nem engedélyezett a virtuális gépek óta"{1}"alatt törlődnek a lemezképpel"{2}".  |
|  OperationNotAllowed  |  A művelet "{0}"nem megengedett, mert a virtuális gép ScaleSet"{1}"a lemezkép jelenleg használja"{2}".  |
|  OperationNotAllowed  |  A művelet "{0}"nem engedélyezett a virtuális gép"{1}", mert a virtuális gép ki van jelölve törlésre. Csak próbálkozhat a törlési művelet (vagy várjon, amíg a folyamatban lévő egyikét végrehajtásához).  |
|  OperationNotAllowed  |  A művelet "{0}"nem engedélyezett a virtuális gép"{1}", mert a virtuális gép van szabadítva, vagy meg van jelölve felszabadításra.  |
|  OperationNotAllowed  |  Művelet "{0}"nem engedélyezett a virtuális gép"{1}", mert a virtuális gép fut-e. Kérjük, energiagazdálkodási ki explicit módon abban az esetben, ha leállítja a virtuális gép vendég operációs rendszerében.  |
|  OperationNotAllowed  |  A művelet "{0}"nem engedélyezett a virtuális gép"{1}", mert a virtuális gép nem felszabadítása.  |
|  OperationNotAllowed  |  A művelet "{0}"nem engedélyezett a virtuális gép"{1}"Mivel a virtuális gép kiterjesztése"{2}" hibás állapotban.  |
|  OperationNotAllowed  |  A művelet "{0}"nem engedélyezett a virtuális gép"{1}" óta egy másik művelet van folyamatban.  |
|  OperationNotAllowed  |  A művelet "{0}"igényel a virtuális gép"{1}" való általánosítva.  |
|  OperationNotAllowed  |  A művelethez a virtuális gépnek futnia kell (vagy be kell lennie állítva futásra).  |
|  OperationNotAllowed  |  Lemez méretével {0}GB, ami kisebb, mint {1}GB-lemezképet, a kapcsolódó lemezt nem engedélyezett.  |
|  OperationNotAllowed  |  Méretezési csoportjának bővítményei leíró "{0}" felveheti csak a Virtuálisgép-méretezési csoportban létrehozásának időpontjában.  |
|  OperationNotAllowed  |  Méretezési csoportjának bővítményei leíró "{0}" törölheti csak a Virtuálisgép-méretezési csoport törlése idején.  |
|  OperationNotAllowed  |  Virtuális gép "{0}" már használja a felügyelt lemezek.  |
|  OperationNotAllowed  |  Virtuális gép "{0}"a "Klasszikus" rendelkezésre állási csoportba tartozik"{1}". Frissítse a rendelkezésre állási csoportot használja a "Igazított" SKU, majd próbálkozzon újra az átalakításhoz.  |
|  OperationNotAllowed  |  VM-lemezkép alapján létre blobalapú lemezek nem rendelkezhet. Az összes lemez kell felügyelt lemezek.  |
|  OperationNotAllowed  |  A rögzítési művelet nem hajtható végre, mert a virtuális gép nincs általánosítva.  |
|  OperationNotAllowed  |  Felügyeleti műveletek a virtuális gép "{0}" engedélyezettek, mert a virtuális gépek lemezei felügyelt lemezek konvertálása.  |
|  OperationNotAllowed  |  A folyamatban lévő művelet van módosítása energiaállapot virtuális gép {0} való {1}. Hajtson végre műveletet {2} egy kis idő múlva.  |
|  OperationNotAllowed  |  Nem lehet hozzáadni, vagy frissítse a virtuális Gépet. A kért Virtuálisgép-méret {0} nem lehet a meglévő foglalási egység érhető el. Olvassa el a következő stratégia átméretezése további a virtuális gép https://aka.ms/azure-resizevm.  |
|  OperationNotAllowed  |  Nem sikerült átméretezni a virtuális Gépet, mert a kért méret {0} nem érhető el a fürt, ahol a rendelkezésre állási csoport jelenleg foglalt. Az elérhető értékek: {1}. Olvassa el a következő stratégia átméretezése további a virtuális gép https://aka.ms/azure-resizevm.  |
|  OperationNotAllowed  |  Nem sikerült átméretezni a virtuális Gépet, mert a kért méret {0} nem érhető el a fürt, ahol a virtuális gép jelenleg foglalt. Átméretezni a virtuális Gépet {1} adjon felszabadítani (Ez a Stop műveletet az Azure portálon), és próbálkozzon újra az átméretezés. Olvassa el a következő stratégia átméretezése további a virtuális gép https://aka.ms/azure-resizevm.  |
|  OSProvisioningClientError  |  Operációs rendszer telepítése sikertelen volt a virtuális gép "{0}", mert a vendég operációs rendszer jelenleg folyamatban van.  |
|  OSProvisioningClientError  |  Operációs rendszer telepítése a virtuális gép "{0}" nem sikerült. Hiba legutolsó részletes adatai: {1} győződjön meg arról, hogy a lemezkép megfelelően elő van készítve (általánosítva van). <ul><li>A Windows utasításokat: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/  </li></ul> |
|  OSProvisioningClientError  |  SSH állomás kulcs létrehozása sikertelen volt. Hiba legutolsó részletes adatai: {0}. Oldja meg a probléma ellenőrizze Ha Linux-ügynök megfelelően van-e beállítva. <ul><li>Ellenőrizheti, hogy a következő utasításokat: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-agent-user-guide/ </li></ul> |
|  OSProvisioningClientError  |  A virtuális géphez megadott felhasználónévnek a Linux-disztribúció érvénytelen. Hiba legutolsó részletes adatai: {0}.  |
|  OSProvisioningInternalError  |  Operációs rendszer telepítése sikertelen volt a virtuális gép "{0}" belső hiba miatt.  |
|  OSProvisioningTimedOut  |  Operációs rendszer telepítése a virtuális gép "{0}" nem fejeződött be a megadott időn belül. A virtuális gép továbbra is lehetséges, hogy befejezése sikeres. Ellenőrizze később a telepítés állapotát.  |
|  OSProvisioningTimedOut  |  Operációs rendszer telepítése a virtuális gép "{0}" nem fejeződött be a megadott időn belül. A virtuális gép továbbra is lehetséges, hogy befejezése sikeres. Ellenőrizze később a telepítés állapotát. Bizonyosodjon meg arról, hogy a lemezkép megfelelően elő van készítve (általánosítva van).   <ul><li>A Windows utasításokat: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Útmutatás Linux rendszerhez: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OSProvisioningTimedOut  |  Operációs rendszer telepítése a virtuális gép "{0}" nem fejeződött be a megadott időn belül. Azonban a virtuális gép vendégügynökének futtató volt észlelhető. Ez azt sugallja, a vendég operációs rendszer nem lett megfelelően előkészített Virtuálisgép-lemezképet használ (az CreateOption = FromImage). A probléma megoldásához, vagy használja a virtuális Merevlemezt, mert CreateOption a csatolás = vagy képként használatra megfelelően előkészítése:   <ul><li>A Windows utasításokat: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Útmutatás Linux rendszerhez: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OverConstrainedAllocationRequest  |  A virtuális gép előírt mérete jelenleg nem érhető el a kijelölt helyszínen.  |
|  ResourceUpdateBlockedOnPlatformUpdate  |  Erőforrás nem frissíthető, mert a platform folyamatban lévő frissítés. Később próbálja meg újra.  |
|  StorageAccountLimitation  |  A tárfiók "{0}" nem támogatja a lapblobokat, amelyek azonban szükségesek a lemezek létrehozásához.  |
|  StorageAccountLimitation  |  A tárfiók "{0}" túllépte a számára lefoglalt kvótát.  |
|  StorageAccountLocationMismatch  |  Nem sikerült feloldani a tárfiók {0}. Ellenőrizze, hogy a számítási erőforrással azonos helyen a tárolási erőforrás-szolgáltató használatával hozták létre.  |
|  StorageAccountNotFound  |  A tárfiók {0} nem található. Győződjön meg arról, a tárfiók nem törlődik, és a virtuális Gépet a azonos Azure-beli helyhez tartozik.  |
|  StorageAccountNotRecognized  |  A szolgáltató által kezelt tárfiókot használja. A használatára {0} nem támogatott.  |
|  StorageAccountOperationInternalError  |  Belső hiba történt a tárfiók elérésekor {0}.  |
|  StorageAccountSubscriptionMismatch  |  A tárfiók {0} nem tartozik előfizetés {1}.  |
|  StorageAccountTooBusy  |  A tárfiók "{0}" jelenleg túlságosan elfoglalt. Érdemes lehet egy másik fiókot.  |
|  StorageAccountTypeNotSupported  |  Lemez {0} használ {1} Ez az a Blob storage-fiók. Próbálja meg újra egy általános célú tárfiókkal.  |
|  StorageAccountTypeNotSupported  |  A tárfiók {0} a {1} típusa. Rendszerindítási diagnosztika támogatja {2} tárfióktípusokat.  <ul><li>Ez a hiba akkor fordul elő, ha a prémium szintű tárfiókot használni, a rendszerindítási diagnosztika. További információkért lásd: [használata a rendszerindítási diagnosztika](../articles/virtual-machines/windows/boot-diagnostics.md). </li></ul> |
|  SubscriptionNotAuthorizedForImage  |  Az előfizetés nem rendelkezik megfelelő jogosultsággal.  |
|  TargetDiskBlobAlreadyExists  |  A BLOB {0} már létezik. Adjon meg egy másik blob URI-t hozzon létre egy új üres adatlemez "{1}".  |
|  TargetDiskBlobAlreadyExists  |  Rögzítési művelet nem folytatható, mert a cél lemezképet tartalmazó blob {0} már létezik, és a VHD-blobok felülírására vonatkozó jelölő nincs beállítva. A blob törölje vagy állítsa be a jelzőt, amely a VHD-blobok felülírására, és próbálkozzon újra.  |
|  TargetDiskBlobAlreadyExists  |  Rögzítési művelet nem folytatható, mert a cél lemezképet tartalmazó blob {0} egy aktív bérleti rendelkezzen.   |
|  TargetDiskBlobAlreadyExists  |  A BLOB {0} már létezik. Adjon meg egy másik blob URI célként lemez "{1}".  |
|  TooManyVMRedeploymentRequests  |  Túl sok újbóli üzembe helyezése kérést kapott a virtuális gép "{0}" vagy a virtuális gépek azonos availabilityset rendelkező virtuális Gépet. Próbálkozzon újra később.  |
|  VHDSizeInvalid  |  A megadott lemez mérete értékének {0} lemez "{1}" blobbal együtt {2} érvénytelen. Lemez méretének {3} és {4}.  |
|  VMAgentStatusCommunicationError  |  Virtuális gép "{0}" nem jelentette a Virtuálisgép-ügynök vagy a bővítmény állapotát. Ellenőrizze, hogy a virtuális gép futó Virtuálisgép-ügynök, és az Azure storage kimenő kapcsolatok hozhatnak létre.  |
|  VMArtifactRepositoryInternalError  |  Hiba történt az összetevőtárral folytatott kommunikáció során, amely a virtuális gép összetevőadatainak lekérésére irányult.  |
|  VMArtifactRepositoryInternalError  |  Belső hiba történt a virtuális gép összetevőadatainak az összetevőtárból való lekérésére során.  |
|  VMExtensionHandlerNonTransientError  |  Eseménykezelő "{0}"Virtuálisgép-bővítmény kapcsolatos hibát jelzett"{1}"with terminál hibakódja"{2}" és a hibaüzenet: "{3}"  |
|  VMExtensionManagementInternalError  |  Belső hiba történt a Virtuálisgép-bővítmény feldolgozása "{0}".  |
|  VMExtensionManagementInternalError  |  A Virtuálisgép-bővítmények előkészítése során több hiba történt. Tekintse meg a virtuális gép bővítmény példányait tartalmazó nézetet.  |
|  VMExtensionProvisioningError  |  Bővítmény feldolgozása során a virtuális gép hibát jelzett "{0}". Hibaüzenet: "{1}".  |
|  VMExtensionProvisioningError  |  Több Virtuálisgép-bővítmény telepítése sikertelen volt a virtuális Gépen. Tekintse meg a virtuális gép bővítmény példányait tartalmazó nézetet.  |
|  VMExtensionProvisioningTimeout  |  Virtuálisgép-bővítmény telepítése "{0}" túllépte az időkorlátot. Bővítmény telepítése előfordulhat, hogy túl sokáig tart, vagy a bővítmény állapota nem határozható meg.  |
|  VMMarketplaceInvalidInput  |  A virtuális gép létrehozása egy nem Piactéri rendszerképből nem kell tervezze meg információkat, távolítsa el a terv adatai a kérelemben. Az operációs rendszer lemezének neve: {0}.  |
|  VMMarketplaceInvalidInput  |  A vásárlási információk nem egyezik. Nem lehet üzembe helyezést végezni a Piactéri lemezképhez. Az operációs rendszer lemezének neve: {0}.  |
|  VMMarketplaceInvalidInput  |  Terv adatainak a kérelem egy virtuális gép létrehozásához a Piactéri lemezképből van szükség. Az operációs rendszer lemezének neve: {0}.  |
|  VMNotFound  |  A virtuális gép "{0}" nem található.  |
|  VMRedeploymentFailed  |  Virtuális gép "{0}" újbóli üzembe helyezése belső hiba miatt sikertelen. Próbálkozzon újra később.  |
|  VMRedeploymentTimedOut  |  Virtuális gép újbóli üzembe helyezése "{0}" nem fejeződött be a megadott időn belül. Akkor lehet, hogy a Befejezés sikeresen egy kis ideig. Más esetben újra a kérelmet.  |
|  VMStartTimedOut  |  Virtuális gép "{0}" a megadott időn belül nem indult el. A virtuális gép továbbra is kezdheti el sikeresen. Az áramellátási állapot később próbálja meg.  |


## <a name="next-steps"></a>További lépések
Ha további segítségre van szüksége, forduljon az Azure-szakértők a [az MSDN Azure és a Stack Overflow fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként is fájl az Azure támogatási incidens. Lépjen a [az Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatja az beszerzése**.