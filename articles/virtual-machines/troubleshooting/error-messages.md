---
title: Gyakori virtuálisgép-hibakódok az Azure-ban | Microsoft Docs
description: Az Azure-beli virtuális gépek üzembe helyezése és kezelése során előforduló gyakori hibakódok ismertetése
services: virtual-machines
documentationcenter: ''
author: xujing-ms
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure
ms.date: 5/22/2017
ms.author: xujing
ms.openlocfilehash: f5639d1cf94c77d699dc6de9841698b045ac1f96
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76543018"
---
# <a name="understand-common-error-messages-when-you-manage-virtual-machines-in-azure"></a>Gyakori hibaüzenetek ismertetése az Azure-beli virtuális gépek kezelésekor

Ez a cikk a virtuális gépek (VM-EK) az Azure-ban való létrehozásakor és kezelésekor felmerülő leggyakoribb hibakódokat és üzeneteket ismerteti.

>[!NOTE]
> Az oldalon megjelenő megjegyzéseket elhagyhatja Visszajelzésként vagy az #azerrormessage címkével ellátott [Azure-visszajelzéssel](https://feedback.azure.com/forums/216843-virtual-machines) .

## <a name="error-response-format"></a>Hiba-válasz formátuma 
Az Azure-beli virtuális gépek a következő JSON-formátumot használják a hibaüzenetek megválaszolásához:

```json
{
  "status": "status code",
  "error": {
    "code":"Top level error code",
    "message":"Top level error message",
    "details":[
     {
      "code":"Inner level error code",
      "message":"Inner level error message"
     }
    ]
   }
}
```

A hiba válasza mindig tartalmaz egy állapotkódot és egy hibát tartalmazó objektumot. Minden egyes hiba-objektum mindig tartalmaz egy hibakódot és egy üzenetet. Ha a virtuális gép sablonnal lett létrehozva, a hiba objektum a részletek szakaszt is tartalmazza, amely a hibakódok és az üzenet belső szintjét tartalmazza. Általában a legbelső hibaüzenet a legfelső szintű hiba.


## <a name="common-virtual-machine-management-errors"></a>Gyakori virtuálisgép-kezelési hibák

Ez a szakasz a virtuális gépek kezelésekor felmerülő gyakori hibaüzeneteket sorolja fel:

|  Hibakód  |  Hibaüzenet  |  
|  :------| :-------------|  
|  AcquireDiskLeaseFailed  |  Nem sikerült beszerezni a bérletet a (z){0}lemez létrehozásakor a blob és az URI {1}használatával. A blob már használatban van.  |  
|  AllocationFailed  |  A foglalás nem sikerült. Próbálja meg csökkenteni a virtuális gép méretét vagy virtuális gépek számát, próbálkozzon újra később, vagy próbálja meg telepíteni egy másik rendelkezésre állási készletet vagy egy másik Azure-helyet.  |  
|  AllocationFailed  |  A virtuális gép kiosztása belső hiba miatt nem sikerült. Próbálkozzon újra később, vagy próbálja meg egy másik helyre telepíteni.  |
|  ArtifactNotFound  |  Nem található a (z) "{0}" közzétevővel és a (z) "{1}" típussal rendelkező virtuálisgép-bővítmény a (z){2}helyen.  |
|  ArtifactNotFound  |  Nem található a bővítmény a (z){0}közzétevővel, a (z) "{1}" típus és a (z) "{2}" kezelő-verzió nem található a bővítmény adattárában.  |
|  ArtifactVersionNotFound  |  Nem található olyan verzió az összetevő-tárházban, amely megfelel a kért "{0}" verziónak.  |
|  ArtifactVersionNotFound  |  Nem található olyan verzió az összetevő-tárházban, amely megfelel a (z) "{1}" közzétevővel és a (z) "{2}" típussal rendelkező virtuálisgép-bővítményhez szükséges "{0}" verziónak.  |
|  AttachDiskWhileBeingDetached  |  A (z) "{0}" adatlemez nem csatolható a (z){1}virtuális géphez, mert a lemez jelenleg le van választva. Várjon, amíg befejeződik a lemez teljes leválasztása, majd próbálkozzon újra.  |
|  BadRequest  |  Az igazított "rendelkezésre állási csoportok még nem támogatottak ebben a régióban.  |
|  BadRequest  |  A felügyelt lemezekkel rendelkező virtuális gépek hozzáadása nem felügyelt rendelkezésre állási készlethez, illetve a felügyelt rendelkezésre állási készlethez blob-alapú lemezekkel rendelkező virtuális gépek hozzáadása nem támogatott. Hozzon létre egy "felügyelt" tulajdonsággal rendelkező rendelkezésre állási készletet a felügyelt lemezekkel rendelkező virtuális gépek hozzáadásához.  |
|  BadRequest  |  A Managed Disks ebben a régióban nem támogatottak.  |
|  BadRequest  |  Több bővítményekhez nem támogatott a (z) "{0}" operációsrendszer-típus esetében. A (z) "{1}" VMExtension a (z) "{2}" kezelővel már hozzá lett adva, vagy meg van adva  |
|  BadRequest  |  A (z) "{0}" művelet nem támogatott a felügyelt lemezekkel rendelkező "{1}" erőforráson.  |
|  CertificateImproperlyFormatted  |  A titkos kód {0} lekért JSON-ábrázolása olyan adatmezővel rendelkezik, amely nem megfelelően formázott PFX-fájl, vagy a megadott jelszó nem dekódolja helyesen a PFX-fájlt.  |
|  CertificateImproperlyFormatted  |  A {0}ból beolvasott adatok nem deszerializálható JSON-ba.  |
|  Ütközés  |  A lemezek átméretezése csak a virtuális gépek létrehozásakor vagy a virtuális gép kiosztása esetén engedélyezett.  |
|  ConflictingUserInput  |  A (z) "{0}" lemez nem csatolható, mert a lemez már a (z) "{1}" virtuális gép tulajdonában van.  |
|  ConflictingUserInput  |  A forrás-és a célként megadott erőforráscsoportok azonosak.  |
|  ConflictingUserInput  |  A lemez {0} a forrás-és cél Storage-fiókok eltérőek.  |
|  ContainerAlreadyOnLease  |  Már van olyan bérlet a tárolón, amely a blobot a {0}URI-val együtt tartja.  |
|  CrossSubscriptionMoveWithKeyVaultResources  |  Az erőforrások áthelyezése kérelem olyan kulcstartó-erőforrásokat tartalmaz, amelyeket a kérelem egy vagy több {0}a hivatkozik. Ez jelenleg nem támogatott az előfizetések közötti Áthelyezésben. Tekintse át a kulcstartó erőforrás-azonosítóinak részleteit.  |
|  DiagnosticsOperationInternalError  |  Belső hiba történt a virtuális gép {0}diagnosztikai profiljának feldolgozása során.  |
|  DiskBlobAlreadyInUseByAnotherDisk  |  A blob {0} már használatban van egy másik, a (z){1}virtuális géphez tartozó lemez használatával. Megvizsgálhatja a lemezre vonatkozó hivatkozási információk blob-metaadatait.  |
|  DiskBlobNotFound  |  Nem található a (z){1}lemez URI-{0} tartalmazó VHD-blob.  |
|  DiskBlobNotFound  |  A VHD-blob nem található URI-{0}.  |
|  DiskEncryptionKeySecretMissingTags  |  {0} titoknak nincs {1} címkéje. Frissítse a titkos verziót, adja hozzá a szükséges címkéket, majd próbálkozzon újra.  |
|  DiskEncryptionKeySecretUnwrapFailed  |  Nem sikerült kicsomagolni a titkos {0} értéket a Key {1} használatával.  |
|  DiskImageNotReady  |  A lemezkép {0} {1} állapotban van. Próbálkozzon újra, ha a rendszerkép elkészült.  |
|  DiskPreparationError  |  Egy vagy több hiba történt a VM-lemezek előkészítése során. Részletekért lásd a lemez példányának nézetét.  |
|  DiskProcessingError  |  A lemez feldolgozása megszakadt, mert a virtuális gép más lemezekkel rendelkezik a meghibásodott lemezeken.  |
|  ImageBlobNotFound  |  Nem található a (z){1}lemez URI-{0} tartalmazó VHD-blob.  |
|  ImageBlobNotFound  |  A VHD-blob nem található URI-{0}.  |
|  IncorrectDiskBlobType  |  A lemezes Blobok csak oldal típusú Blobok lehetnek. A (z){1}lemezhez tartozó blob-{0} blokk típusú blob.  |
|  IncorrectDiskBlobType  |  A lemezes Blobok csak oldal típusú Blobok lehetnek. A blob {0} típusa "{1}".  |
|  IncorrectImageBlobType  |  A lemezes Blobok csak oldal típusú Blobok lehetnek. A (z){1}lemezhez tartozó blob-{0} blokk típusú blob.  |
|  IncorrectImageBlobType  |  A lemezes Blobok csak oldal típusú Blobok lehetnek. A blob {0} típusa "{1}".  |
|  InternalOperationError  |  Nem oldható fel a Storage-fiók {0}. Győződjön meg arról, hogy a tárolási erőforrás-szolgáltatón keresztül lett létrehozva a számítási erőforrással megegyező helyen.  |
|  InternalOperationError  |  nem sikerült a feladatok felkeresése {0}.  |
|  InternalOperationError  |  Hiba történt a (z){0}virtuális gép hálózati profiljának ellenőrzésekor.  |
|  InvalidAccountType  |  A AccountType {0} érvénytelen.  |
|  InvalidParameter  |  A {0} paraméter értéke érvénytelen.  |
|  InvalidParameter  |  A megadott rendszergazdai jelszó nem engedélyezett.  |
|  InvalidParameter  |  "A megadott jelszónak {0}{1} karakter hosszúnak kell lennie, és meg kell felelnie legalább {2} a jelszó bonyolultságára vonatkozó követelményeknek a következők közül: <ol><li> Nagybetűs karaktert tartalmaz</li><li>Kisbetűs karaktert tartalmaz</li><li>Numerikus számjegyet tartalmaz</li><li>Speciális karaktert tartalmaz.</li></ol>  |
|  InvalidParameter  |  A megadott rendszergazdai Felhasználónév nem engedélyezett.  |
|  InvalidParameter  |  Meglévő operációsrendszer-lemez nem csatolható, ha a virtuális gép egy platformról vagy egy felhasználói rendszerképből lett létrehozva.  |
|  InvalidParameter  |  A tároló neve {0} érvénytelen. A tárolók nevének 3-63 karakter hosszúnak kell lennie, és csak kisbetűket és kötőjeleket tartalmazhat. A kötőjel előtt és után alfanumerikus karakternek kell szerepelnie.  |
|  InvalidParameter  |  A tároló neve {0} az URL-{1} érvénytelen. A tárolók nevének 3-63 karakter hosszúnak kell lennie, és csak kisbetűket és kötőjeleket tartalmazhat. A kötőjel előtt és után alfanumerikus karakternek kell szerepelnie.  |
|  InvalidParameter  |  A blob neve az URL-{0} tartalmaz perjelet. A lemezek jelenleg nem támogatottak.  |
|  InvalidParameter  |  Az URI {0} nem megfelelő blob URI-ként jelenik meg.  |
|  InvalidParameter  |  A (z) "{0}" nevű lemez már ugyanazt a LUN-t használja: {1}.  |
|  InvalidParameter  |  Már létezik "{0}" nevű lemez.  |
|  InvalidParameter  |  A megadott lemezkép-hivatkozásban már definiált lemezhez nem adható meg a felhasználói lemezkép felülbírálásai.  |
|  InvalidParameter  |  A (z) "{0}" nevű lemez már ugyanazt a VHD URL-címet használja {1}.  |
|  InvalidParameter  |  A tartalék tartományok megadott száma {0} a {2}{1} tartományba kell esnie.  |
|  InvalidParameter  |  A licenc típusa {0} érvénytelen. Az érvényes licencfeltételek a következők: Windows_Client vagy Windows_Server, megkülönböztetve a kis-és nagybetűket.  |
|  InvalidParameter  |  A Linux-állomásnév nem lehet hosszabb {0} karakternél, vagy a következő karaktereket tartalmazza: {1}.  |
|  InvalidParameter  |  A nyilvános ssh-kulcsok céljának elérési útja jelenleg az alapértelmezett értékre van korlátozva, {0} a Linux kiépítési ügynök ismert hibája miatt.  |
|  InvalidParameter  |  Már létezik lemez a LUN {0}ban.  |
|  InvalidParameter  |  A kérelem előfizetési {0} meg kell egyeznie a felügyelt lemez azonosítójában található előfizetési {1}.  |
|  InvalidParameter  |  A OSProfile lévő egyéni adatmennyiségeknek Base64 kódolásban kell lenniük, és legfeljebb {0} karakter hosszúnak kell lenniük.  |
|  InvalidParameter  |  A blob nevének az URL-{0} végén szerepelnie kell a "{1}" kiterjesztésnek.  |
|  InvalidParameter  |  a (z) {0}"nem érvényes rögzített VHD-blob nevének előtagja. Egy érvényes előtag megfelel A "{1}" regexnek.  |
|  InvalidParameter  |  Nem adhatók hozzá tanúsítványok a virtuális géphez, ha a virtuálisgép-ügynök nincs kiépítve.  |
|  InvalidParameter  |  Már létezik lemez a LUN {0}ban.  |
|  InvalidParameter  |  A virtuális gép nem hozható létre, mert a kért méret {0} nem érhető el abban a fürtben, ahol a rendelkezésre állási csoport jelenleg le van foglalva. A rendelkezésre álló méretek a következők: {1}. További információ a virtuális gépek átméretezésével kapcsolatos stratégiáról https://aka.ms/azure-resizevm.  |
|  InvalidParameter  |  A kért VM-méret {0} nem érhető el az aktuális régióban. Az aktuális régióban elérhető méretek a következők: {1}. Tudjon meg többet az egyes régiókban elérhető virtuálisgép-méretekről https://aka.ms/azure-regions.  |
|  InvalidParameter  |  A kért VM-méret {0} nem érhető el az aktuális régióban. Tudjon meg többet az egyes régiókban elérhető virtuálisgép-méretekről https://aka.ms/azure-regions.  |
|  InvalidParameter  |  A Windows-rendszergazda felhasználóneve nem lehet hosszabb {0} karakternél, és ponttal (.) végződik, vagy a következő karaktereket tartalmazza: {1}.  |
|  InvalidParameter  |  A Windows-számítógép neve nem lehet {0} karakternél hosszabb, nem lehet teljesen numerikus, vagy a következő karaktereket tartalmazza: {1}.  |
|  MissingMoveDependentResources  |  Az erőforrások áthelyezése kérelem nem tartalmazza az összes függő erőforrást. Tekintse meg a hiányzó erőforrás-azonosítók hibáinak részleteit.  |
|  MoveResourcesHaveInvalidState  |  Az erőforrások áthelyezése kérelem olyan virtuális gépeket tartalmaz, amelyek érvénytelen Storage-fiókokhoz vannak társítva. Tekintse át ezeket az erőforrás-azonosítókat és a hivatkozott Storage-fiókok nevét.  |
|  MoveResourcesHavePendingOperations  |  Az erőforrások áthelyezése kérelem olyan erőforrásokat tartalmaz, amelyeken függőben van egy művelet. Tekintse át az erőforrás-azonosítók részleteit. A függőben lévő műveletek befejeződése után próbálja megismételni a műveletet.  |
|  MoveResourcesNotFound  |  Az erőforrások áthelyezése kérelem olyan erőforrásokat tartalmaz, amelyek nem találhatók. Tekintse át az erőforrás-azonosítók részleteit.  |
|  NetworkingInternalOperationError  |  Ismeretlen hálózati foglalási hiba történt.  |
|  NetworkingInternalOperationError  |  Ismeretlen hálózati foglalási hiba  |
|  NetworkingInternalOperationError  |  Belső hiba történt a virtuális gép hálózati profiljának feldolgozása során.  |
|  NotFound  |  A rendelkezésre állási csoport {0} nem található.  |
|  NotFound  |  A kérelemben megadott "{0}" forrás virtuális gép nem létezik ebben az Azure-helyen.  |
|  NotFound  |  Nem található {0} azonosítójú bérlő.  |
|  NotFound  |  A rendszerkép {0} nem található.  |
|  NotSupported  |  A licenc típusa {0}, de a rendszerkép {1} nem a helyszínen található.  |
|  OperationNotAllowed  |  A rendelkezésre állási csoport {0} nem törölhető. A rendelkezésre állási csoport törlése előtt győződjön meg arról, hogy nem tartalmaz virtuális gépet.  |
|  OperationNotAllowed  |  A rendelkezésre állási készlet SKU-jának a "klasszikus" értékre való módosítása nem engedélyezett.  |
|  OperationNotAllowed  |  A virtuális gép bővítményei nem módosíthatók, ha a virtuális gép nem fut.  |
|  OperationNotAllowed  |  A rögzítési művelet csak blob-alapú lemezekkel rendelkező virtuális gépeken támogatott. A "rendszerkép" erőforrás-API-k segítségével hozzon létre egy rendszerképet egy felügyelt virtuális gépről.  |
|  OperationNotAllowed  |  Az erőforrás-{0} nem hozható létre a rendszerképből {1} a rendszerkép sikeres létrehozása után.  |
|  OperationNotAllowed  |  A encryptionSettings frissítései nem engedélyezettek a virtuális gép lefoglalásakor, próbálkozzon újra a virtuális gép kiosztása után  |
|  OperationNotAllowed  |  Felügyelt lemez hozzáadása egy blob-alapú lemezekkel rendelkező virtuális géphez nem támogatott.  |
|  OperationNotAllowed  |  Az ilyen méretű virtuális géphez csatlakoztatható adatlemezek maximális száma {0}.  |
|  OperationNotAllowed  |  BLOB-alapú lemez hozzáadása a felügyelt lemezekkel rendelkező virtuális géphez nem támogatott.  |
|  OperationNotAllowed  |  A (z) "{0}" művelet nem engedélyezett a (z){1}rendszerképen, mert a rendszerkép meg van jelölve törlésre. Csak újra próbálkozhat a törlési művelettel (vagy megvárhatja, amíg folyamatban van egy Befejezés).  |
|  OperationNotAllowed  |  A (z) "{0}" művelet nem engedélyezett a (z){1}virtuális gépen, mert a virtuális gép általánosítva van.  |
|  OperationNotAllowed  |  A (z) "{0}" művelet nem engedélyezett, mert a (z) "{1}" visszaállítási pontok gyűjteménye törlésre van megjelölve.  |
|  OperationNotAllowed  |  A (z) "{0}" művelet nem engedélyezett a (z){1}virtuálisgép-bővítményen, mert a törlésre van megjelölve. Csak újra próbálkozhat a törlési művelettel (vagy megvárhatja, amíg folyamatban van egy Befejezés).  |
|  OperationNotAllowed  |  A (z) "{0}" művelet nem engedélyezett, mert a (z) "{1}" Virtual Machines a (z) "{2}" rendszerkép használatával lett kiépítve.  |
|  OperationNotAllowed  |  A (z) "{0}" művelet nem engedélyezett, mert a (z) "{1}" virtuális gép méretezési csoport jelenleg a (z) "{2}" rendszerképet használja.  |
|  OperationNotAllowed  |  A (z) "{0}" művelet nem engedélyezett a (z){1}virtuális gépen, mert a virtuális gép törlésre van megjelölve. Csak újra próbálkozhat a törlési művelettel (vagy megvárhatja, amíg folyamatban van egy Befejezés).  |
|  OperationNotAllowed  |  A (z) "{0}" művelet nem engedélyezett a (z) "{1}" virtuális gépen, mert a virtuális gép le van foglalva vagy fel van jelölve a kiosztásra.  |
|  OperationNotAllowed  |  A (z) "{0}" művelet nem engedélyezett a (z){1}virtuális gépen, mert a virtuális gép fut. Ha a virtuális gépet a vendég operációs rendszeren belülről állítja le, kapcsolja ki explicit módon.  |
|  OperationNotAllowed  |  A (z) "{0}" művelet nem engedélyezett a (z){1}virtuális gépen, mert a virtuális gép nincs lefoglalva.  |
|  OperationNotAllowed  |  A (z) "{0}" művelet nem engedélyezett a (z) "{1}" virtuális gépen, mert a virtuális gép "{2}" kiterjesztése sikertelen állapotban van.  |
|  OperationNotAllowed  |  A (z) "{0}" művelet nem engedélyezett a (z){1}virtuális gépen, mert egy másik művelet van folyamatban.  |
|  OperationNotAllowed  |  A (z) "{0}" művelethez a (z) "{1}" virtuális gép általánosítása szükséges.  |
|  OperationNotAllowed  |  A művelet végrehajtásához a virtuális gépnek futnia kell (vagy a futtatásra van állítva).  |
|  OperationNotAllowed  |  A lemez mérete {0}GB, amely kisebb, mint a lemezképben lévő megfelelő lemez mérete {1}GB, nem engedélyezett.  |
|  OperationNotAllowed  |  A (z){0}kezelő virtuálisgép-méretezési csoportjának bővítményei csak a virtuálisgép-méretezési csoport létrehozásakor vehetők fel.  |
|  OperationNotAllowed  |  A (z){0}kezelő virtuálisgép-méretezési csoportjának bővítményei csak a virtuálisgép-méretezési csoport törlésének időpontjában törölhetők.  |
|  OperationNotAllowed  |  A (z){0}virtuális gép már felügyelt lemezeket használ.  |
|  OperationNotAllowed  |  A (z){0}virtuális gép a "klasszikus" rendelkezésre állási csoportba tartozik,{1}. Frissítse a rendelkezésre állási készletet az "igazított" SKU használatára, majd próbálja megismételni az átalakítást.  |
|  OperationNotAllowed  |  A rendszerképből létrehozott virtuális gép nem rendelkezhet blob-alapú lemezekkel. Minden lemeznek felügyelt lemeznek kell lennie.  |
|  OperationNotAllowed  |  A rögzítési művelet nem hajtható végre, mert a virtuális gép nincs általánosítva.  |
|  OperationNotAllowed  |  A (z){0}virtuális gépen lévő felügyeleti műveletek nem megengedettek, mert a virtuálisgép-lemezek felügyelt lemezekre lettek konvertálva.  |
|  OperationNotAllowed  |  Egy folyamatban lévő művelet megváltoztatja a virtuális gép energiaellátási állapotát {1}{0}. Némi idő elteltével hajtsa végre a művelet {2}.  |
|  OperationNotAllowed  |  A virtuális gép nem adható hozzá vagy nem frissíthető. Előfordulhat, hogy a kért VM-méret {0} nem érhető el a meglévő foglalási egységben. További információ a virtuális gépek átméretezésével kapcsolatos stratégiáról https://aka.ms/azure-resizevm.  |
|  OperationNotAllowed  |  Nem lehet átméretezni a virtuális gépet, mert a kért méret {0} nem érhető el abban a fürtben, ahol a rendelkezésre állási csoport jelenleg le van foglalva. A rendelkezésre álló méretek a következők: {1}. További információ a virtuális gépek átméretezésével kapcsolatos stratégiáról https://aka.ms/azure-resizevm.  |
|  OperationNotAllowed  |  Nem lehet átméretezni a virtuális gépet, mert a kért méret {0} nem érhető el abban a fürtben, amelyben a virtuális gép jelenleg le van foglalva. Ha át szeretné méretezni a virtuális gépet {1} kérjük, szabadítson fel (ez a művelet leáll a Azure Portal), majd próbálja megismételni az átméretezési műveletet. További információ a virtuális gépek átméretezésével kapcsolatos stratégiáról https://aka.ms/azure-resizevm.  |
|  OSProvisioningClientError  |  Az operációs rendszer üzembe helyezése nem sikerült a (z){0}virtuális gépen, mert a vendég operációs rendszer üzembe helyezése folyamatban van.  |
|  OSProvisioningClientError  |  Nem sikerült a (z){0}virtuális gép operációs rendszerének üzembe helyezése. Hiba részletei: {1} győződjön meg arról, hogy a rendszerkép megfelelően előkészített (általánosított). <ul><li>Windows-utasítások: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/  </li></ul> |
|  OSProvisioningClientError  |  Az SSH-gazda kulcsának létrehozása sikertelen volt. Hiba részletei: {0}. A probléma megoldásához ellenőrizze, hogy a Linux-ügynök megfelelően van-e beállítva. <ul><li>Az utasításokat a következő helyen tekintheti meg: https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux/ </li></ul> |
|  OSProvisioningClientError  |  A virtuális géphez megadott Felhasználónév érvénytelen ehhez a Linux-disztribúcióhoz. Hiba részletei: {0}.  |
|  OSProvisioningInternalError  |  Belső hiba miatt nem sikerült az operációs rendszer kiépítés a (z){0}virtuális géphez.  |
|  OSProvisioningTimedOut  |  A (z) "{0}" virtuális gép operációs rendszer általi üzembe helyezése nem fejeződik be a megszabott időn belül. A virtuális gép továbbra is befejezheti a kiépítés sikerességét. A kiépítési állapotot később ellenőrizze.  |
|  OSProvisioningTimedOut  |  A (z) "{0}" virtuális gép operációs rendszer általi üzembe helyezése nem fejeződik be a megszabott időn belül. A virtuális gép továbbra is befejezheti a kiépítés sikerességét. A kiépítési állapotot később ellenőrizze. Győződjön meg arról is, hogy a rendszerkép megfelelően előkészített (általánosított).   <ul><li>Windows-utasítások: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Linux-utasítások: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OSProvisioningTimedOut  |  A (z) "{0}" virtuális gép operációs rendszer általi üzembe helyezése nem fejeződik be a megszabott időn belül. A rendszer azonban a virtuális gép vendég ügynökét észlelte. Ez azt sugallja, hogy a vendég operációs rendszer nem lett megfelelően felkészülve a virtuálisgép-rendszerképként való használatra (CreateOption = FromImage). A probléma megoldásához használja a VHD-t a következő módon: CreateOption = csatolja vagy készítse el megfelelően a rendszerképként való használatra:   <ul><li>Windows-utasítások: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Linux-utasítások: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OverConstrainedAllocationRequest  |  A virtuális gép szükséges mérete jelenleg nem érhető el a kiválasztott helyen.  |
|  ResourceUpdateBlockedOnPlatformUpdate  |  Az erőforrás jelenleg nem frissíthető, mert folyamatban van a platform frissítése. Később próbálja meg újra.  |
|  StorageAccountLimitation  |  A (z){0}Storage-fiók nem támogatja a lemezek létrehozásához szükséges blobokat.  |
|  StorageAccountLimitation  |  A (z){0}Storage-fiók túllépte a lefoglalt kvótát.  |
|  StorageAccountLocationMismatch  |  Nem oldható fel a Storage-fiók {0}. Győződjön meg arról, hogy a tárolási erőforrás-szolgáltatón keresztül lett létrehozva a számítási erőforrással megegyező helyen.  |
|  StorageAccountNotFound  |  A Storage-fiók {0} nem található. Győződjön meg arról, hogy a Storage-fiók nincs törölve, és ugyanahhoz az Azure-helyhez tartozik, mint a virtuális gép.  |
|  StorageAccountNotRecognized  |  Használjon tárolási erőforrás-szolgáltató által kezelt Storage-fiókot. A {0} használata nem támogatott.  |
|  StorageAccountOperationInternalError  |  Belső hiba történt a Storage-fiók {0}elérésekor.  |
|  StorageAccountSubscriptionMismatch  |  A Storage-fiók {0} nem tartozik az előfizetési {1}hoz.  |
|  StorageAccountTooBusy  |  A (z){0}Storage-fiók jelenleg túl elfoglalt. Vegye fontolóra egy másik fiók használatát.  |
|  StorageAccountTypeNotSupported  |  A lemez {0} {1}, amely blob Storage-fiók. Próbálkozzon újra az általános célú Storage-fiókkal.  |
|  StorageAccountTypeNotSupported  |  A Storage-fiók {0} {1} típusú. A rendszerindítási diagnosztika támogatja a {2} Storage-fiókok típusát.  <ul><li>Ez a hiba akkor fordul elő, ha a Premium Storage-fiókot használja a rendszerindítási diagnosztika számára. További információ: [a rendszerindítási diagnosztika használata](boot-diagnostics.md). </li></ul> |
|  SubscriptionNotAuthorizedForImage  |  Az előfizetés nincs engedélyezve.  |
|  TargetDiskBlobAlreadyExists  |  A blob {0} már létezik. Adjon meg egy másik blob URI-t egy új, üres adatlemez ({1}) létrehozásához.  |
|  TargetDiskBlobAlreadyExists  |  A rögzítési művelet nem folytatható, mert a célként megadott rendszerkép blobja {0} már létezik, és a VHD-Blobok felülírására szolgáló jelző nincs beállítva. Törölje a blobot, vagy állítsa be a jelzőt a VHD-Blobok felülírásához, majd próbálkozzon újra.  |
|  TargetDiskBlobAlreadyExists  |  A rögzítési művelet nem folytatható, mert a célként megadott rendszerkép-blob {0} rendelkezik aktív bérlettel.   |
|  TargetDiskBlobAlreadyExists  |  A blob {0} már létezik. Adjon meg egy másik blob-URI-t célként a következő lemez számára: "{1}".  |
|  TooManyVMRedeploymentRequests  |  Túl sok újratelepítési kérelem érkezett a (z){0}virtuális géphez vagy a virtuális géppel azonos üzemmódú található virtuális gépekhez. Próbálkozzon újra később.  |
|  VHDSizeInvalid  |  A (z) "{1}" lemez {0} megadott lemezes mérete nem érvényes a blob {2} érvénytelen. A lemez méretének {3} és {4}között kell lennie.  |
|  VMAgentStatusCommunicationError  |  A (z){0}virtuális gép nem jelentett állapotot a virtuálisgép-ügynökhöz vagy-bővítményekhez. Ellenőrizze, hogy a virtuális gép rendelkezik-e futó virtuálisgép-ügynökkel, és képes-e kimenő kapcsolatokat létesíteni az Azure Storage szolgáltatással.  |
|  VMArtifactRepositoryInternalError  |  Hiba történt az összetevő-adattárral folytatott kommunikáció során a virtuálisgép-összetevők részleteinek lekéréséhez.  |
|  VMArtifactRepositoryInternalError  |  Belső hiba történt a virtuális gép összetevőinek az összetevő-tárházból való beolvasása során.  |
|  VMExtensionHandlerNonTransientError  |  A (z) "{0}" kezelő hibát jelzett a (z) "{1}" virtuálisgép-bővítmény hibájában a következő terminál-hibakódtal: "{2}", hibaüzenet: "{3}"  |
|  VMExtensionManagementInternalError  |  Belső hiba történt a (z){0}virtuálisgép-bővítmény feldolgozásakor.  |
|  VMExtensionManagementInternalError  |  Több hiba történt a virtuálisgép-bővítmények előkészítése során. Részletekért lásd a virtuálisgép-bővítmény példányának nézetét.  |
|  VMExtensionProvisioningError  |  A virtuális gép hibát jelzett a következő bővítmény feldolgozásakor: "{0}". Hibaüzenet: "{1}".  |
|  VMExtensionProvisioningError  |  A virtuális gépen több virtuálisgép-bővítményt nem sikerült kiépíteni. A részletekért tekintse meg a virtuálisgép-bővítmény példányának nézetét.  |
|  VMExtensionProvisioningTimeout  |  A (z) "{0}" virtuálisgép-bővítmény üzembe helyezése túllépte az időkorlátot. Előfordulhat, hogy a bővítmény telepítése túl hosszú ideig tart, vagy a bővítmény állapota nem szerezhető be.  |
|  VMMarketplaceInvalidInput  |  Ha nem Piactéri rendszerképből hoz létre virtuális gépet, nem szükséges megterveznie az adatokat, távolítsa el a csomag információit a kérelemben. Az operációsrendszer-lemez neve {0}.  |
|  VMMarketplaceInvalidInput  |  A vásárlási adatok nem egyeznek. Nem lehet üzembe helyezni a piactér lemezképéről. Az operációsrendszer-lemez neve {0}.  |
|  VMMarketplaceInvalidInput  |  A virtuális gépek Piactéri rendszerképből való létrehozásához a kérelemben meg kell tervezni a szükséges információkat. Az operációsrendszer-lemez neve {0}.  |
|  VMNotFound  |  A (z){0}virtuális gép nem található.  |
|  VMRedeploymentFailed  |  A virtuális gép "{0}" újratelepítése belső hiba miatt nem sikerült. Próbálkozzon újra később.  |
|  VMRedeploymentTimedOut  |  A (z){0}virtuális gép újratelepítése nem fejeződik be a megadott időn belül. Néhány perc múlva sikeresen befejeződik. Másik lehetőségként újra megpróbálkozhat a kéréssel.  |
|  VMStartTimedOut  |  A (z){0}virtuális gép nem indult el a megadott időn belül. A virtuális gép továbbra is sikeresen elindulhat. Ellenőrizze a Power állapotot később.  |


## <a name="next-steps"></a>Következő lépések
Ha további segítségre van szüksége, vegye fel a kapcsolatot az Azure [-szakértőkkel az MSDN Azure-ban, és stack overflow fórumokon](https://azure.microsoft.com/support/forums/)is. Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése**lehetőséget.
