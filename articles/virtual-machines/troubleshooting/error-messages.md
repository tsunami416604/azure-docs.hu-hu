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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "76543018"
---
# <a name="understand-common-error-messages-when-you-manage-virtual-machines-in-azure"></a>A gyakori hibaüzenetek ismertetése a virtuális gépek kezelésekor az Azure-ban

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
|  AcquireDiskLeaseFailed  |  Nem sikerült beszerezni a bérletet a (z) "" lemez létrehozásakor {0} a blob és az URI használatával {1} . A blob már használatban van.  |  
|  AllocationFailed  |  A foglalás nem sikerült. Próbálja meg csökkenteni a virtuális gép méretét vagy virtuális gépek számát, próbálkozzon újra később, vagy próbálja meg telepíteni egy másik rendelkezésre állási készletet vagy egy másik Azure-helyet.  |  
|  AllocationFailed  |  A virtuális gép kiosztása belső hiba miatt nem sikerült. Próbálkozzon újra később, vagy próbálja meg egy másik helyre telepíteni.  |
|  ArtifactNotFound  |  Nem található a (z) "" közzétevővel és típussal rendelkező virtuálisgép-bővítmény a (z) "" {0} {1} helyen {2} .  |
|  ArtifactNotFound  |  A bővítmény nem található a (z) "" közzétevővel {0} , a (z) "" típussal és a (z) "" típusú {1} kezelő-verzióval {2} .  |
|  ArtifactVersionNotFound  |  Nem található olyan verzió az összetevő-tárházban, amely megfelel a kért "" verziónak {0} .  |
|  ArtifactVersionNotFound  |  Nem található olyan verzió az összetevő-tárházban, amely megfelel a (z {0} ) közzétevővel {1} és típussal rendelkező virtuálisgép-bővítményhez szükséges "" verziónak {2} .  |
|  AttachDiskWhileBeingDetached  |  A (z) "" adatlemez nem csatolható a (z) {0} virtuális géphez, {1} mert a lemez jelenleg le van választva. Várjon, amíg befejeződik a lemez teljes leválasztása, majd próbálkozzon újra.  |
|  BadRequest  |  Az igazított "rendelkezésre állási csoportok még nem támogatottak ebben a régióban.  |
|  BadRequest  |  A felügyelt lemezekkel rendelkező virtuális gépek hozzáadása nem felügyelt rendelkezésre állási készlethez, illetve a felügyelt rendelkezésre állási készlethez blob-alapú lemezekkel rendelkező virtuális gépek hozzáadása nem támogatott. Hozzon létre egy "felügyelt" tulajdonsággal rendelkező rendelkezésre állási készletet a felügyelt lemezekkel rendelkező virtuális gépek hozzáadásához.  |
|  BadRequest  |  A Managed Disks ebben a régióban nem támogatottak.  |
|  BadRequest  |  Több bővítményekhez nem támogatott a (z) operációsrendszer-típus esetében {0} . A (z) "" VMExtension a (z) "" {1} kezelővel {2} már hozzáadva vagy megadva a bemenetben.  |
|  BadRequest  |  A (z {0} ) "" művelet nem támogatott {1} a felügyelt lemezekkel rendelkező "" erőforráson.  |
|  CertificateImproperlyFormatted  |  A fájlból lekért JSON-ábrázolás {0} olyan adatmezővel rendelkezik, amely nem megfelelően FORMÁZOTT pfx-fájl, vagy a megadott jelszó nem dekódolja helyesen a pfx-fájlt.  |
|  CertificateImproperlyFormatted  |  A rendszerből beolvasott adatok {0} nem deszerializálható JSON-ba.  |
|  Ütközés  |  A lemezek átméretezése csak a virtuális gépek létrehozásakor vagy a virtuális gép kiosztása esetén engedélyezett.  |
|  ConflictingUserInput  |  A (z {0} ) "" lemez nem csatolható, mert a (z) "" virtuális gép már tulajdonosa a lemeznek {1} .  |
|  ConflictingUserInput  |  A forrás-és a célként megadott erőforráscsoportok azonosak.  |
|  ConflictingUserInput  |  A lemez forrás-és cél Storage-fiókjai {0} eltérőek.  |
|  ContainerAlreadyOnLease  |  Már van olyan bérlet a tárolón, amely az URI-val rendelkező blobot tartja {0} .  |
|  CrossSubscriptionMoveWithKeyVaultResources  |  Az erőforrások áthelyezése kérelem olyan kulcstartó-erőforrásokat tartalmaz, amelyeket a kérelem egy vagy több oka hivatkozik {0} . Ez jelenleg nem támogatott az előfizetések közötti Áthelyezésben. Tekintse át a kulcstartó erőforrás-azonosítóinak részleteit.  |
|  DiagnosticsOperationInternalError  |  Belső hiba történt a virtuális gép diagnosztikai profiljának feldolgozása során {0} .  |
|  DiskBlobAlreadyInUseByAnotherDisk  |  A blobot {0} már egy másik, a (z) virtuális géphez tartozó lemez használja {1} . Megvizsgálhatja a lemezre vonatkozó hivatkozási információk blob-metaadatait.  |
|  DiskBlobNotFound  |  Nem található a (z) "" lemez URI-azonosítóját tartalmazó VHD-blob {0} {1} .  |
|  DiskBlobNotFound  |  A VHD-blob nem található URI-val {0} .  |
|  DiskEncryptionKeySecretMissingTags  |  {0}a titkos kód nem rendelkezik {1} címkékkel. Frissítse a titkos verziót, adja hozzá a szükséges címkéket, majd próbálkozzon újra.  |
|  DiskEncryptionKeySecretUnwrapFailed  |  Nem sikerült kicsomagolni a titkos {0} értéket a kulcs használatával {1} .  |
|  DiskImageNotReady  |  A lemezkép {0} állapotban van {1} . Próbálkozzon újra, ha a rendszerkép elkészült.  |
|  DiskPreparationError  |  Egy vagy több hiba történt a VM-lemezek előkészítése során. Részletekért lásd a lemez példányának nézetét.  |
|  DiskProcessingError  |  A lemez feldolgozása megszakadt, mert a virtuális gép más lemezekkel rendelkezik a meghibásodott lemezeken.  |
|  ImageBlobNotFound  |  Nem található a (z) "" lemez URI-azonosítóját tartalmazó VHD-blob {0} {1} .  |
|  ImageBlobNotFound  |  A VHD-blob nem található URI-val {0} .  |
|  IncorrectDiskBlobType  |  A lemezes Blobok csak oldal típusú Blobok lehetnek. A (z) {0} "" lemezhez tartozó blob {1} blokk típusú blob.  |
|  IncorrectDiskBlobType  |  A lemezes Blobok csak oldal típusú Blobok lehetnek. {0}A blob típusa " {1} ".  |
|  IncorrectImageBlobType  |  A lemezes Blobok csak oldal típusú Blobok lehetnek. A (z) {0} "" lemezhez tartozó blob {1} blokk típusú blob.  |
|  IncorrectImageBlobType  |  A lemezes Blobok csak oldal típusú Blobok lehetnek. {0}A blob típusa " {1} ".  |
|  InternalOperationError  |  Nem oldható fel a Storage-fiók {0} . Győződjön meg arról, hogy a tárolási erőforrás-szolgáltatón keresztül lett létrehozva a számítási erőforrással megegyező helyen.  |
|  InternalOperationError  |  {0}nem sikerült felkeresni a feladatokat.  |
|  InternalOperationError  |  Hiba történt a (z) "" virtuális gép hálózati profiljának ellenőrzésekor {0} .  |
|  InvalidAccountType  |  A AccountType {0} érvénytelen.  |
|  InvalidParameter  |  A paraméter értéke {0} érvénytelen.  |
|  InvalidParameter  |  A megadott rendszergazdai jelszó nem engedélyezett.  |
|  InvalidParameter  |  "A megadott jelszónak {0} -karakter hosszúságú kell lennie {1} , és legalább a {2} jelszó bonyolultságára vonatkozó követelménynek kell megfelelnie a következők közül: <ol><li> Nagybetűs karaktert tartalmaz</li><li>Kisbetűs karaktert tartalmaz</li><li>Numerikus számjegyet tartalmaz</li><li>Speciális karaktert tartalmaz.</li></ol>  |
|  InvalidParameter  |  A megadott rendszergazdai Felhasználónév nem engedélyezett.  |
|  InvalidParameter  |  Meglévő operációsrendszer-lemez nem csatolható, ha a virtuális gép egy platformról vagy egy felhasználói rendszerképből lett létrehozva.  |
|  InvalidParameter  |  A tároló neve {0} érvénytelen. A tárolók nevének 3-63 karakter hosszúnak kell lennie, és csak kisbetűket és kötőjeleket tartalmazhat. A kötőjel előtt és után alfanumerikus karakternek kell szerepelnie.  |
|  InvalidParameter  |  {0}Érvénytelen a tároló neve az URL-címben {1} . A tárolók nevének 3-63 karakter hosszúnak kell lennie, és csak kisbetűket és kötőjeleket tartalmazhat. A kötőjel előtt és után alfanumerikus karakternek kell szerepelnie.  |
|  InvalidParameter  |  Az URL-címben szereplő blob neve {0} perjelet tartalmaz. A lemezek jelenleg nem támogatottak.  |
|  InvalidParameter  |  Az URI {0} nem megfelelő blob URI-ként jelenik meg.  |
|  InvalidParameter  |  A (z) "" nevű lemez {0} már ugyanazt a LUN-t használja: {1} .  |
|  InvalidParameter  |  Már létezik egy " {0} " nevű lemez.  |
|  InvalidParameter  |  A megadott lemezkép-hivatkozásban már definiált lemezhez nem adható meg a felhasználói lemezkép felülbírálásai.  |
|  InvalidParameter  |  A (z) "" nevű lemez {0} már ugyanazt a VHD URL-címet használja {1} .  |
|  InvalidParameter  |  A tartalék tartományok megadott számának {0} a tartományba kell esnie {1} {2} .  |
|  InvalidParameter  |  A licenc típusa {0} érvénytelen. Az érvényes licencfeltételek a következők: Windows_Client vagy Windows_Server, megkülönböztetve a kis-és nagybetűket.  |
|  InvalidParameter  |  A Linux-állomásnév nem lehet hosszabb {0} karakternél, vagy a következő karaktereket tartalmazza: {1} .  |
|  InvalidParameter  |  Az SSH nyilvános kulcsok céljának elérési útja jelenleg az alapértelmezett értékre van korlátozva, a {0} Linux-kiépítési ügynök ismert hibája miatt.  |
|  InvalidParameter  |  Már létezik lemez a LUN-ban {0} .  |
|  InvalidParameter  |  A kérelem előfizetésének {0} meg kell egyeznie a {1} felügyelt lemez azonosítójában szereplő előfizetéssel.  |
|  InvalidParameter  |  A OSProfile lévő egyéni adatmennyiségnek Base64 kódolással kell rendelkeznie, és legfeljebb karakter hosszúnak kell lennie {0} .  |
|  InvalidParameter  |  Az URL-címben szereplő blob nevének {0} a (z {1} ) "" kiterjesztéssel kell végződnie.  |
|  InvalidParameter  |  {0}"nem érvényes rögzített VHD-blob nevének előtagja. Egy érvényes előtag megfelel A " {1} " regexnek.  |
|  InvalidParameter  |  Nem adhatók hozzá tanúsítványok a virtuális géphez, ha a virtuálisgép-ügynök nincs kiépítve.  |
|  InvalidParameter  |  Már létezik lemez a LUN-ban {0} .  |
|  InvalidParameter  |  A virtuális gép nem hozható létre, mert a kért méret {0} nem érhető el abban a fürtben, ahol a rendelkezésre állási csoport jelenleg le van foglalva. A rendelkezésre álló méretek a következők: {1} . További információk a virtuális gépek átméretezésével kapcsolatos stratégiáról: https://aka.ms/azure-resizevm .  |
|  InvalidParameter  |  A kért VM {0} -méret nem érhető el az aktuális régióban. Az aktuális régióban elérhető méretek a következők: {1} . Tudjon meg többet az egyes régiókban elérhető virtuálisgép-méretekről a következő helyen: https://aka.ms/azure-regions .  |
|  InvalidParameter  |  A kért VM {0} -méret nem érhető el az aktuális régióban. Tudjon meg többet az egyes régiókban elérhető virtuálisgép-méretekről a következő helyen: https://aka.ms/azure-regions .  |
|  InvalidParameter  |  A Windows-rendszergazda felhasználóneve nem lehet hosszabb {0} karakternél, egy ponttal (.) végződik, vagy a következő karaktereket tartalmazza: {1} .  |
|  InvalidParameter  |  A Windows-számítógép neve nem lehet hosszabb {0} karakternél, nem lehet teljesen numerikus, vagy a következő karaktereket tartalmazza: {1} .  |
|  MissingMoveDependentResources  |  Az erőforrások áthelyezése kérelem nem tartalmazza az összes függő erőforrást. Tekintse meg a hiányzó erőforrás-azonosítók hibáinak részleteit.  |
|  MoveResourcesHaveInvalidState  |  Az erőforrások áthelyezése kérelem olyan virtuális gépeket tartalmaz, amelyek érvénytelen Storage-fiókokhoz vannak társítva. Tekintse át ezeket az erőforrás-azonosítókat és a hivatkozott Storage-fiókok nevét.  |
|  MoveResourcesHavePendingOperations  |  Az erőforrások áthelyezése kérelem olyan erőforrásokat tartalmaz, amelyeken függőben van egy művelet. Tekintse át az erőforrás-azonosítók részleteit. A függőben lévő műveletek befejeződése után próbálja megismételni a műveletet.  |
|  MoveResourcesNotFound  |  Az erőforrások áthelyezése kérelem olyan erőforrásokat tartalmaz, amelyek nem találhatók. Tekintse át az erőforrás-azonosítók részleteit.  |
|  NetworkingInternalOperationError  |  Ismeretlen hálózati foglalási hiba történt.  |
|  NetworkingInternalOperationError  |  Ismeretlen hálózati foglalási hiba  |
|  NetworkingInternalOperationError  |  Belső hiba történt a virtuális gép hálózati profiljának feldolgozása során.  |
|  NotFound  |  A rendelkezésre állási csoport {0} nem található.  |
|  NotFound  |  {0}A kérelemben megadott forrás virtuális gép nem létezik ebben az Azure-helyen.  |
|  NotFound  |  Nem található az azonosítóval rendelkező bérlő {0} .  |
|  NotFound  |  A rendszerkép {0} nem található.  |
|  NotSupported  |  A licenc típusa {0} , de a rendszerkép-blob {1} nem a helyszínen található.  |
|  OperationNotAllowed  |  A rendelkezésre állási csoport {0} nem törölhető. A rendelkezésre állási csoport törlése előtt győződjön meg arról, hogy nem tartalmaz virtuális gépet.  |
|  OperationNotAllowed  |  A rendelkezésre állási készlet SKU-jának a "klasszikus" értékre való módosítása nem engedélyezett.  |
|  OperationNotAllowed  |  A virtuális gép bővítményei nem módosíthatók, ha a virtuális gép nem fut.  |
|  OperationNotAllowed  |  A rögzítési művelet csak blob-alapú lemezekkel rendelkező virtuális gépeken támogatott. A "rendszerkép" erőforrás-API-k segítségével hozzon létre egy rendszerképet egy felügyelt virtuális gépről.  |
|  OperationNotAllowed  |  Az erőforrás {0} {1} csak a rendszerkép sikeres létrehozása után hozható létre a rendszerképből.  |
|  OperationNotAllowed  |  A encryptionSettings frissítései nem engedélyezettek a virtuális gép lefoglalásakor, próbálkozzon újra a virtuális gép kiosztása után  |
|  OperationNotAllowed  |  Felügyelt lemez hozzáadása egy blob-alapú lemezekkel rendelkező virtuális géphez nem támogatott.  |
|  OperationNotAllowed  |  Az ilyen méretű virtuális géphez csatlakoztatható adatlemezek maximális száma {0} .  |
|  OperationNotAllowed  |  BLOB-alapú lemez hozzáadása a felügyelt lemezekkel rendelkező virtuális géphez nem támogatott.  |
|  OperationNotAllowed  |  A (z) "" művelet nem engedélyezett a (z) " {0} " rendszerképen, {1} mert a rendszerkép meg van jelölve törlésre. Csak újra próbálkozhat a törlési művelettel (vagy megvárhatja, amíg folyamatban van egy Befejezés).  |
|  OperationNotAllowed  |  A (z) " {0} " művelet nem engedélyezett a (z) "" virtuális gépen, {1} mert a virtuális gép általánosítva van.  |
|  OperationNotAllowed  |  A (z) " {0} " művelet nem engedélyezett, mert a (z) "" visszaállítási pont gyűjteménye {1} törlésre van megjelölve.  |
|  OperationNotAllowed  |  A (z) " {0} " művelet nem engedélyezett a (z) "" virtuálisgép-bővítményen, {1} mert a törlésre van megjelölve. Csak újra próbálkozhat a törlési művelettel (vagy megvárhatja, amíg folyamatban van egy Befejezés).  |
|  OperationNotAllowed  |  A (z) "" művelet {0} nem engedélyezett, mert a (z) "" Virtual Machines a (z {1} ) "" rendszerkép használatával lett kiépítve {2} .  |
|  OperationNotAllowed  |  A (z {0} ) "" művelet nem engedélyezett, mert a (z) "" virtuális gép méretezési csoport {1} jelenleg a (z) "" rendszerképet használja {2} .  |
|  OperationNotAllowed  |  A (z) " {0} " művelet nem engedélyezett a (z) "" virtuális gépen, {1} mert a virtuális gép meg van jelölve törlésre. Csak újra próbálkozhat a törlési művelettel (vagy megvárhatja, amíg folyamatban van egy Befejezés).  |
|  OperationNotAllowed  |  A (z) " {0} " művelet nem engedélyezett a (z) "" virtuális gépen, {1} mert a virtuális gép le van foglalva vagy fel van jelölve a kiosztásra.  |
|  OperationNotAllowed  |  A (z) "" művelet nem engedélyezett a (z) " {0} " virtuális gépen {1} , mert a virtuális gép fut. Ha a virtuális gépet a vendég operációs rendszeren belülről állítja le, kapcsolja ki explicit módon.  |
|  OperationNotAllowed  |  A (z) " {0} " művelet nem engedélyezett a (z) "" virtuális gépen, {1} mert a virtuális gép nincs lefoglalva.  |
|  OperationNotAllowed  |  A (z) "" művelet nem engedélyezett a (z) "" {0} virtuális gépen, {1} mert a virtuális gép " {2} " kiterjesztése sikertelen állapotban van.  |
|  OperationNotAllowed  |  A (z) " {0} " művelet nem engedélyezett a (z) "" virtuális gépen, {1} mert egy másik művelet van folyamatban.  |
|  OperationNotAllowed  |  A (z) "" művelethez a (z) "" {0} virtuális gép {1} általánosítása szükséges.  |
|  OperationNotAllowed  |  A művelet végrehajtásához a virtuális gépnek futnia kell (vagy a futtatásra van állítva).  |
|  OperationNotAllowed  |  A GB méretű lemez {0} , amely kisebb, mint a {1} lemezképben lévő megfelelő lemez mérete (GB), nem megengedett.  |
|  OperationNotAllowed  |  A (z) "" kezelő virtuálisgép-méretezési csoportjának bővítményei {0} csak a virtuálisgép-méretezési csoport létrehozásakor vehetők fel.  |
|  OperationNotAllowed  |  A (z) "" kezelő virtuálisgép-méretezési csoportjának bővítményei {0} csak a virtuálisgép-méretezési csoport törlésének időpontjában törölhetők.  |
|  OperationNotAllowed  |  A (z) "" virtuális gép {0} már felügyelt lemezeket használ.  |
|  OperationNotAllowed  |  A (z) "" virtuális gép {0} a "klasszikus" rendelkezésre állási csoporthoz tartozik {1} . Frissítse a rendelkezésre állási készletet az "igazított" SKU használatára, majd próbálja megismételni az átalakítást.  |
|  OperationNotAllowed  |  A rendszerképből létrehozott virtuális gép nem rendelkezhet blob-alapú lemezekkel. Minden lemeznek felügyelt lemeznek kell lennie.  |
|  OperationNotAllowed  |  A rögzítési művelet nem hajtható végre, mert a virtuális gép nincs általánosítva.  |
|  OperationNotAllowed  |  A (z) virtuális gépen lévő felügyeleti műveletek {0} nem megengedettek, mert a virtuálisgép-lemezek felügyelt lemezekre lettek konvertálva.  |
|  OperationNotAllowed  |  Egy folyamatban lévő művelet megváltoztatja a virtuális gép energiaellátási állapotát a következőre: {0} {1} . Némi idő elteltével végezze el a műveletet {2} .  |
|  OperationNotAllowed  |  A virtuális gép nem adható hozzá vagy nem frissíthető. Előfordulhat, hogy a kért VM {0} -méret nem érhető el a meglévő foglalási egységben. További információk a virtuális gépek átméretezésével kapcsolatos stratégiáról: https://aka.ms/azure-resizevm .  |
|  OperationNotAllowed  |  Nem lehet átméretezni a virtuális gépet, mert a kért méret {0} nem érhető el abban a fürtben, ahol a rendelkezésre állási csoport jelenleg le van foglalva. A rendelkezésre álló méretek a következők: {1} . További információk a virtuális gépek átméretezésével kapcsolatos stratégiáról: https://aka.ms/azure-resizevm .  |
|  OperationNotAllowed  |  Nem lehet átméretezni a virtuális gépet, mert a kért méret {0} nem érhető el abban a fürtben, amelyben a virtuális gép jelenleg le van foglalva. Ha szeretné átméretezni a virtuális gépet, hogy {1} felszabadítsa a lefoglalást (ez a leállítási művelet a Azure Portal), majd próbálja megismételni az átméretezési műveletet. További információk a virtuális gépek átméretezésével kapcsolatos stratégiáról: https://aka.ms/azure-resizevm .  |
|  OSProvisioningClientError  |  Az operációs rendszer üzembe helyezése nem sikerült a (z) "" virtuális géphez, {0} mert a vendég operációs rendszer jelenleg ki van építve.  |
|  OSProvisioningClientError  |  A (z) "" virtuális gép operációs rendszerének üzembe helyezése {0} nem sikerült. Hiba részletei: Győződjön meg {1} arról, hogy a rendszerkép megfelelően előkészített (általánosított). <ul><li>Windows-utasítások:https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/  </li></ul> |
|  OSProvisioningClientError  |  Az SSH-gazda kulcsának létrehozása sikertelen volt. Hiba részletei: {0} . A probléma megoldásához ellenőrizze, hogy a Linux-ügynök megfelelően van-e beállítva. <ul><li>Az utasításokat a következő helyen tekintheti meg:https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux/ </li></ul> |
|  OSProvisioningClientError  |  A virtuális géphez megadott Felhasználónév érvénytelen ehhez a Linux-disztribúcióhoz. Hiba részletei: {0} .  |
|  OSProvisioningInternalError  |  Belső hiba miatt nem sikerült a (z) "" virtuális gép operációs rendszerének üzembe helyezése {0} .  |
|  OSProvisioningTimedOut  |  A (z) "" virtuális gép operációs rendszer általi üzembe {0} helyezése nem fejeződik be a megadott időn belül. A virtuális gép továbbra is befejezheti a kiépítés sikerességét. A kiépítési állapotot később ellenőrizze.  |
|  OSProvisioningTimedOut  |  A (z) "" virtuális gép operációs rendszer általi üzembe {0} helyezése nem fejeződik be a megadott időn belül. A virtuális gép továbbra is befejezheti a kiépítés sikerességét. A kiépítési állapotot később ellenőrizze. Győződjön meg arról is, hogy a rendszerkép megfelelően előkészített (általánosított).   <ul><li>Windows-utasítások:https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Linux-utasítások:https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OSProvisioningTimedOut  |  A (z) "" virtuális gép operációs rendszer általi üzembe {0} helyezése nem fejeződik be a megadott időn belül. A rendszer azonban a virtuális gép vendég ügynökét észlelte. Ez azt sugallja, hogy a vendég operációs rendszer nem lett megfelelően felkészülve a virtuálisgép-rendszerképként való használatra (CreateOption = FromImage). A probléma megoldásához használja a VHD-t a következő módon: CreateOption = csatolja vagy készítse el megfelelően a rendszerképként való használatra:   <ul><li>Windows-utasítások:https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Linux-utasítások:https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OverConstrainedAllocationRequest  |  A virtuális gép szükséges mérete jelenleg nem érhető el a kiválasztott helyen.  |
|  ResourceUpdateBlockedOnPlatformUpdate  |  Az erőforrás jelenleg nem frissíthető, mert folyamatban van a platform frissítése. Próbálkozzon újra később.  |
|  StorageAccountLimitation  |  A (z) "" Storage-fiók {0} nem támogatja a lemezek létrehozásához szükséges Blobok használatát.  |
|  StorageAccountLimitation  |  A (z) "" Storage-fiók {0} túllépte a lefoglalt kvótát.  |
|  StorageAccountLocationMismatch  |  Nem oldható fel a Storage-fiók {0} . Győződjön meg arról, hogy a tárolási erőforrás-szolgáltatón keresztül lett létrehozva a számítási erőforrással megegyező helyen.  |
|  StorageAccountNotFound  |  A Storage-fiók {0} nem található. Győződjön meg arról, hogy a Storage-fiók nincs törölve, és ugyanahhoz az Azure-helyhez tartozik, mint a virtuális gép.  |
|  StorageAccountNotRecognized  |  Használjon tárolási erőforrás-szolgáltató által kezelt Storage-fiókot. A használata {0} nem támogatott.  |
|  StorageAccountOperationInternalError  |  Belső hiba történt a Storage-fiók elérésekor {0} .  |
|  StorageAccountSubscriptionMismatch  |  A Storage {0} -fiók nem tartozik az előfizetéshez {1} .  |
|  StorageAccountTooBusy  |  A (z) "" Storage-fiók {0} jelenleg túl elfoglalt. Vegye fontolóra egy másik fiók használatát.  |
|  StorageAccountTypeNotSupported  |  {0}A lemez {1} a blob Storage-fiókot használja. Próbálkozzon újra az általános célú Storage-fiókkal.  |
|  StorageAccountTypeNotSupported  |  A Storage-fiók {0} {1} típusa. A rendszerindítási diagnosztika támogatja a {2} Storage-fiókok típusait.  <ul><li>Ez a hiba akkor fordul elő, ha a Premium Storage-fiókot használja a rendszerindítási diagnosztika számára. További információ: [a rendszerindítási diagnosztika használata](boot-diagnostics.md). </li></ul> |
|  SubscriptionNotAuthorizedForImage  |  Az előfizetés nincs engedélyezve.  |
|  TargetDiskBlobAlreadyExists  |  A blob {0} már létezik. Adjon meg egy másik blob URI-t egy új, üres adatlemez létrehozásához {1} .  |
|  TargetDiskBlobAlreadyExists  |  A rögzítési művelet nem folytatható, mert a célként megadott lemezkép {0} -blob már létezik, és a VHD-Blobok felülírására szolgáló jelző nincs beállítva. Törölje a blobot, vagy állítsa be a jelzőt a VHD-Blobok felülírásához, majd próbálkozzon újra.  |
|  TargetDiskBlobAlreadyExists  |  A rögzítési művelet nem folytatható, mert a célként megadott lemezkép blobja {0} aktív bérlettel rendelkezik.   |
|  TargetDiskBlobAlreadyExists  |  A blob {0} már létezik. Adjon meg egy másik blob-URI-t célként a (z) "" lemez számára {1} .  |
|  TooManyVMRedeploymentRequests  |  Túl sok újratelepítési kérelem érkezett a (z) "" virtuális gépre {0} vagy a virtuális géppel azonos üzemmódú található virtuális gépekre. Próbálkozzon újra később.  |
|  VHDSizeInvalid  |  A (z) "" lemez megadott lemezének mérete {0} {1} érvénytelen a blobtal {2} . A lemez méretének és közé kell esnie {3} {4} .  |
|  VMAgentStatusCommunicationError  |  A (z) "" virtuális gép {0} nem jelentett állapotot a virtuálisgép-ügynökhöz vagy-bővítményekhez. Ellenőrizze, hogy a virtuális gép rendelkezik-e futó virtuálisgép-ügynökkel, és képes-e kimenő kapcsolatokat létesíteni az Azure Storage szolgáltatással.  |
|  VMArtifactRepositoryInternalError  |  Hiba történt az összetevő-adattárral folytatott kommunikáció során a virtuálisgép-összetevők részleteinek lekéréséhez.  |
|  VMArtifactRepositoryInternalError  |  Belső hiba történt a virtuális gép összetevőinek az összetevő-tárházból való beolvasása során.  |
|  VMExtensionHandlerNonTransientError  |  A (z) "" kezelője hibát jelentett a (z) "" virtuálisgép {0} -bővítmény számára {1} a következő terminál-hibakódtal {2} : {3} ""  |
|  VMExtensionManagementInternalError  |  Belső hiba történt a virtuálisgép-bővítmény () feldolgozásakor {0} .  |
|  VMExtensionManagementInternalError  |  Több hiba történt a virtuálisgép-bővítmények előkészítése során. Részletekért lásd a virtuálisgép-bővítmény példányának nézetét.  |
|  VMExtensionProvisioningError  |  A virtuális gép a (z) "" bővítmény feldolgozásakor hibát jelzett {0} . Hibaüzenet: " {1} ".  |
|  VMExtensionProvisioningError  |  A virtuális gépen több virtuálisgép-bővítményt nem sikerült kiépíteni. A részletekért tekintse meg a virtuálisgép-bővítmény példányának nézetét.  |
|  VMExtensionProvisioningTimeout  |  A (z) "" virtuálisgép-bővítmény üzembe helyezése túllépte az {0} időkorlátot. Előfordulhat, hogy a bővítmény telepítése túl hosszú ideig tart, vagy a bővítmény állapota nem szerezhető be.  |
|  VMMarketplaceInvalidInput  |  Ha nem Piactéri rendszerképből hoz létre virtuális gépet, nem szükséges megterveznie az adatokat, távolítsa el a csomag információit a kérelemben. Az operációsrendszer-lemez neve: {0} .  |
|  VMMarketplaceInvalidInput  |  A vásárlási adatok nem egyeznek. Nem lehet üzembe helyezni a piactér lemezképéről. Az operációsrendszer-lemez neve: {0} .  |
|  VMMarketplaceInvalidInput  |  A virtuális gépek Piactéri rendszerképből való létrehozásához a kérelemben meg kell tervezni a szükséges információkat. Az operációsrendszer-lemez neve: {0} .  |
|  VMNotFound  |  A (z {0} ) "" virtuális gép nem található.  |
|  VMRedeploymentFailed  |  A virtuális gép {0} újratelepítése belső hiba miatt nem sikerült. Próbálkozzon újra később.  |
|  VMRedeploymentTimedOut  |  A (z) "" virtuális gép újratelepítése {0} nem fejeződik be a megadott időn belül. Néhány perc múlva sikeresen befejeződik. Másik lehetőségként újra megpróbálkozhat a kéréssel.  |
|  VMStartTimedOut  |  A (z) "" virtuális gép {0} nem indult el a megadott időn belül. A virtuális gép továbbra is sikeresen elindulhat. Ellenőrizze a Power állapotot később.  |


## <a name="next-steps"></a>További lépések
Ha további segítségre van szüksége, vegye fel a kapcsolatot az Azure [-szakértőkkel az MSDN Azure-ban, és stack overflow fórumokon](https://azure.microsoft.com/support/forums/)is. Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése**lehetőséget.
