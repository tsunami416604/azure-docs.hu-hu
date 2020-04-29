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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
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
|  AcquireDiskLeaseFailed  |  Nem sikerült beszerezni a bérletet a{0}(z) "" {1}lemez létrehozásakor a blob és az URI használatával. A blob már használatban van.  |  
|  AllocationFailed  |  A foglalás nem sikerült. Próbálja meg csökkenteni a virtuális gép méretét vagy virtuális gépek számát, próbálkozzon újra később, vagy próbálja meg telepíteni egy másik rendelkezésre állási készletet vagy egy másik Azure-helyet.  |  
|  AllocationFailed  |  A virtuális gép kiosztása belső hiba miatt nem sikerült. Próbálkozzon újra később, vagy próbálja meg egy másik helyre telepíteni.  |
|  ArtifactNotFound  |  Nem található a (z){0}"" közzétevővel{1}és típussal rendelkező virtuálisgép-bővítmény a{2}(z) "" helyen.  |
|  ArtifactNotFound  |  A bővítmény nem található{0}a (z){1}"" közzétevővel, a ({2}z) "" típussal és a (z) "" típusú kezelő-verzióval.  |
|  ArtifactVersionNotFound  |  Nem található olyan verzió az összetevő-tárházban, amely megfelel a kért{0}"" verziónak.  |
|  ArtifactVersionNotFound  |  Nem található olyan verzió az összetevő-tárházban, amely megfelel a ({0}z) közzétevővel{1}és típussal{2}rendelkező virtuálisgép-bővítményhez szükséges "" verziónak.  |
|  AttachDiskWhileBeingDetached  |  A (z) "{0}" adatlemez nem{1}csatolható a (z) virtuális géphez, mert a lemez jelenleg le van választva. Várjon, amíg befejeződik a lemez teljes leválasztása, majd próbálkozzon újra.  |
|  BadRequest  |  Az igazított "rendelkezésre állási csoportok még nem támogatottak ebben a régióban.  |
|  BadRequest  |  A felügyelt lemezekkel rendelkező virtuális gépek hozzáadása nem felügyelt rendelkezésre állási készlethez, illetve a felügyelt rendelkezésre állási készlethez blob-alapú lemezekkel rendelkező virtuális gépek hozzáadása nem támogatott. Hozzon létre egy "felügyelt" tulajdonsággal rendelkező rendelkezésre állási készletet a felügyelt lemezekkel rendelkező virtuális gépek hozzáadásához.  |
|  BadRequest  |  A Managed Disks ebben a régióban nem támogatottak.  |
|  BadRequest  |  Több bővítményekhez nem támogatott a (z) operációsrendszer-{0}típus esetében. {1}A (z) ""{2}VMExtension a (z) "" kezelővel már hozzáadva vagy megadva a bemenetben.  |
|  BadRequest  |  A ({0}z) "" művelet nem támogatott{1}a felügyelt lemezekkel rendelkező "" erőforráson.  |
|  CertificateImproperlyFormatted  |  A fájlból {0} LEkért JSON-ábrázolás olyan adatmezővel rendelkezik, amely nem megfelelően formázott pfx-fájl, vagy a megadott jelszó nem dekódolja helyesen a pfx-fájlt.  |
|  CertificateImproperlyFormatted  |  A rendszerből beolvasott {0} adatok nem deszerializálható JSON-ba.  |
|  Ütközés  |  A lemezek átméretezése csak a virtuális gépek létrehozásakor vagy a virtuális gép kiosztása esetén engedélyezett.  |
|  ConflictingUserInput  |  A ({0}z) "" lemez nem csatolható, mert a ({1}z) "" virtuális gép már tulajdonosa a lemeznek.  |
|  ConflictingUserInput  |  A forrás-és a célként megadott erőforráscsoportok azonosak.  |
|  ConflictingUserInput  |  A lemez {0} forrás-és cél Storage-fiókjai eltérőek.  |
|  ContainerAlreadyOnLease  |  Már van olyan bérlet a tárolón, amely az URI {0}-val rendelkező blobot tartja.  |
|  CrossSubscriptionMoveWithKeyVaultResources  |  Az erőforrások áthelyezése kérelem olyan kulcstartó-erőforrásokat tartalmaz, amelyeket a kérelem egy vagy {0}több oka hivatkozik. Ez jelenleg nem támogatott az előfizetések közötti Áthelyezésben. Tekintse át a kulcstartó erőforrás-azonosítóinak részleteit.  |
|  DiagnosticsOperationInternalError  |  Belső hiba történt a virtuális gép {0}diagnosztikai profiljának feldolgozása során.  |
|  DiskBlobAlreadyInUseByAnotherDisk  |  A {0} blobot már egy másik, a ({1}z) virtuális géphez tartozó lemez használja. Megvizsgálhatja a lemezre vonatkozó hivatkozási információk blob-metaadatait.  |
|  DiskBlobNotFound  |  Nem található a (z) " {0} {1}" lemez URI-azonosítóját tartalmazó VHD-blob.  |
|  DiskBlobNotFound  |  A VHD-blob nem található URI {0}-val.  |
|  DiskEncryptionKeySecretMissingTags  |  {0}a {1} titkos kód nem rendelkezik címkékkel. Frissítse a titkos verziót, adja hozzá a szükséges címkéket, majd próbálkozzon újra.  |
|  DiskEncryptionKeySecretUnwrapFailed  |  Nem sikerült kicsomagolni a {0} titkos {1} értéket a kulcs használatával.  |
|  DiskImageNotReady  |  A {1} lemezkép {0} állapotban van. Próbálkozzon újra, ha a rendszerkép elkészült.  |
|  DiskPreparationError  |  Egy vagy több hiba történt a VM-lemezek előkészítése során. Részletekért lásd a lemez példányának nézetét.  |
|  DiskProcessingError  |  A lemez feldolgozása megszakadt, mert a virtuális gép más lemezekkel rendelkezik a meghibásodott lemezeken.  |
|  ImageBlobNotFound  |  Nem található a (z) " {0} {1}" lemez URI-azonosítóját tartalmazó VHD-blob.  |
|  ImageBlobNotFound  |  A VHD-blob nem található URI {0}-val.  |
|  IncorrectDiskBlobType  |  A lemezes Blobok csak oldal típusú Blobok lehetnek. A {0} (z){1}"" lemezhez tartozó blob blokk típusú blob.  |
|  IncorrectDiskBlobType  |  A lemezes Blobok csak oldal típusú Blobok lehetnek. A {0} blob típusa "{1}".  |
|  IncorrectImageBlobType  |  A lemezes Blobok csak oldal típusú Blobok lehetnek. A {0} (z){1}"" lemezhez tartozó blob blokk típusú blob.  |
|  IncorrectImageBlobType  |  A lemezes Blobok csak oldal típusú Blobok lehetnek. A {0} blob típusa "{1}".  |
|  InternalOperationError  |  Nem oldható fel a Storage {0}-fiók. Győződjön meg arról, hogy a tárolási erőforrás-szolgáltatón keresztül lett létrehozva a számítási erőforrással megegyező helyen.  |
|  InternalOperationError  |  {0}nem sikerült felkeresni a feladatokat.  |
|  InternalOperationError  |  Hiba történt a ({0}z) "" virtuális gép hálózati profiljának ellenőrzésekor.  |
|  InvalidAccountType  |  A AccountType {0} érvénytelen.  |
|  InvalidParameter  |  A paraméter {0} értéke érvénytelen.  |
|  InvalidParameter  |  A megadott rendszergazdai jelszó nem engedélyezett.  |
|  InvalidParameter  |  "A megadott jelszónak {0}-{1} karakter hosszúságú kell lennie, és {2} legalább a jelszó bonyolultságára vonatkozó követelménynek kell megfelelnie a következők közül: <ol><li> Nagybetűs karaktert tartalmaz</li><li>Kisbetűs karaktert tartalmaz</li><li>Numerikus számjegyet tartalmaz</li><li>Speciális karaktert tartalmaz.</li></ol>  |
|  InvalidParameter  |  A megadott rendszergazdai Felhasználónév nem engedélyezett.  |
|  InvalidParameter  |  Meglévő operációsrendszer-lemez nem csatolható, ha a virtuális gép egy platformról vagy egy felhasználói rendszerképből lett létrehozva.  |
|  InvalidParameter  |  A tároló {0} neve érvénytelen. A tárolók nevének 3-63 karakter hosszúnak kell lennie, és csak kisbetűket és kötőjeleket tartalmazhat. A kötőjel előtt és után alfanumerikus karakternek kell szerepelnie.  |
|  InvalidParameter  |  Érvénytelen a {0} tároló neve {1} az URL-címben. A tárolók nevének 3-63 karakter hosszúnak kell lennie, és csak kisbetűket és kötőjeleket tartalmazhat. A kötőjel előtt és után alfanumerikus karakternek kell szerepelnie.  |
|  InvalidParameter  |  Az URL-címben {0} szereplő blob neve perjelet tartalmaz. A lemezek jelenleg nem támogatottak.  |
|  InvalidParameter  |  Az URI {0} nem megfelelő blob URI-ként jelenik meg.  |
|  InvalidParameter  |  A (z){0}"" nevű lemez már ugyanazt a {1}LUN-t használja:.  |
|  InvalidParameter  |  Már létezik egy "{0}" nevű lemez.  |
|  InvalidParameter  |  A megadott lemezkép-hivatkozásban már definiált lemezhez nem adható meg a felhasználói lemezkép felülbírálásai.  |
|  InvalidParameter  |  A (z){0}"" nevű lemez már ugyanazt a {1}VHD URL-címet használja.  |
|  InvalidParameter  |  A tartalék tartományok megadott számának {0} a tartományba {1} kell esnie. {2}  |
|  InvalidParameter  |  A licenc típusa {0} érvénytelen. Az érvényes licencfeltételek a következők: Windows_Client vagy Windows_Server, megkülönböztetve a kis-és nagybetűket.  |
|  InvalidParameter  |  A Linux-állomásnév nem {0} lehet hosszabb karakternél, vagy a következő karaktereket {1}tartalmazza:.  |
|  InvalidParameter  |  Az SSH nyilvános kulcsok céljának elérési útja jelenleg az alapértelmezett értékre {0} van korlátozva, a Linux-kiépítési ügynök ismert hibája miatt.  |
|  InvalidParameter  |  Már létezik lemez a {0} LUN-ban.  |
|  InvalidParameter  |  A {0} kérelem előfizetésének meg kell egyeznie a felügyelt lemez azonosítójában szereplő előfizetéssel {1} .  |
|  InvalidParameter  |  A OSProfile lévő egyéni adatmennyiségnek Base64 kódolással kell rendelkeznie, és legfeljebb {0} karakter hosszúnak kell lennie.  |
|  InvalidParameter  |  Az URL-címben {0} szereplő blob nevének a{1}(z) "" kiterjesztéssel kell végződnie.  |
|  InvalidParameter  |  {0}"nem érvényes rögzített VHD-blob nevének előtagja. Egy érvényes előtag megfelel A "{1}" regexnek.  |
|  InvalidParameter  |  Nem adhatók hozzá tanúsítványok a virtuális géphez, ha a virtuálisgép-ügynök nincs kiépítve.  |
|  InvalidParameter  |  Már létezik lemez a {0} LUN-ban.  |
|  InvalidParameter  |  A virtuális gép nem hozható létre, mert a {0} kért méret nem érhető el abban a fürtben, ahol a rendelkezésre állási csoport jelenleg le van foglalva. A rendelkezésre álló méretek a {1}következők:. További információk a virtuális gépek átméretezésével https://aka.ms/azure-resizevmkapcsolatos stratégiáról:.  |
|  InvalidParameter  |  A kért VM- {0} méret nem érhető el az aktuális régióban. Az aktuális régióban elérhető méretek a következők: {1}. Tudjon meg többet az egyes régiókban elérhető virtuálisgép-méretekről https://aka.ms/azure-regionsa következő helyen:.  |
|  InvalidParameter  |  A kért VM- {0} méret nem érhető el az aktuális régióban. Tudjon meg többet az egyes régiókban elérhető virtuálisgép-méretekről https://aka.ms/azure-regionsa következő helyen:.  |
|  InvalidParameter  |  A Windows-rendszergazda felhasználóneve nem lehet hosszabb {0} karakternél, egy ponttal (.) végződik, vagy a következő karaktereket tartalmazza: {1}.  |
|  InvalidParameter  |  A Windows-számítógép neve nem lehet {0} hosszabb karakternél, nem lehet teljesen numerikus, vagy a következő karaktereket {1}tartalmazza:.  |
|  MissingMoveDependentResources  |  Az erőforrások áthelyezése kérelem nem tartalmazza az összes függő erőforrást. Tekintse meg a hiányzó erőforrás-azonosítók hibáinak részleteit.  |
|  MoveResourcesHaveInvalidState  |  Az erőforrások áthelyezése kérelem olyan virtuális gépeket tartalmaz, amelyek érvénytelen Storage-fiókokhoz vannak társítva. Tekintse át ezeket az erőforrás-azonosítókat és a hivatkozott Storage-fiókok nevét.  |
|  MoveResourcesHavePendingOperations  |  Az erőforrások áthelyezése kérelem olyan erőforrásokat tartalmaz, amelyeken függőben van egy művelet. Tekintse át az erőforrás-azonosítók részleteit. A függőben lévő műveletek befejeződése után próbálja megismételni a műveletet.  |
|  MoveResourcesNotFound  |  Az erőforrások áthelyezése kérelem olyan erőforrásokat tartalmaz, amelyek nem találhatók. Tekintse át az erőforrás-azonosítók részleteit.  |
|  NetworkingInternalOperationError  |  Ismeretlen hálózati foglalási hiba történt.  |
|  NetworkingInternalOperationError  |  Ismeretlen hálózati foglalási hiba  |
|  NetworkingInternalOperationError  |  Belső hiba történt a virtuális gép hálózati profiljának feldolgozása során.  |
|  NotFound  |  A rendelkezésre állási csoport {0} nem található.  |
|  NotFound  |  A kérelemben megadott{0}forrás virtuális gép nem létezik ebben az Azure-helyen.  |
|  NotFound  |  Nem található az {0} azonosítóval rendelkező bérlő.  |
|  NotFound  |  A rendszerkép {0} nem található.  |
|  NotSupported  |  A licenc típusa {0}, de a rendszerkép-blob {1} nem a helyszínen található.  |
|  OperationNotAllowed  |  A rendelkezésre {0} állási csoport nem törölhető. A rendelkezésre állási csoport törlése előtt győződjön meg arról, hogy nem tartalmaz virtuális gépet.  |
|  OperationNotAllowed  |  A rendelkezésre állási készlet SKU-jának a "klasszikus" értékre való módosítása nem engedélyezett.  |
|  OperationNotAllowed  |  A virtuális gép bővítményei nem módosíthatók, ha a virtuális gép nem fut.  |
|  OperationNotAllowed  |  A rögzítési művelet csak blob-alapú lemezekkel rendelkező virtuális gépeken támogatott. A "rendszerkép" erőforrás-API-k segítségével hozzon létre egy rendszerképet egy felügyelt virtuális gépről.  |
|  OperationNotAllowed  |  Az erőforrás {0} csak a rendszerkép sikeres létrehozása {1} után hozható létre a rendszerképből.  |
|  OperationNotAllowed  |  A encryptionSettings frissítései nem engedélyezettek a virtuális gép lefoglalásakor, próbálkozzon újra a virtuális gép kiosztása után  |
|  OperationNotAllowed  |  Felügyelt lemez hozzáadása egy blob-alapú lemezekkel rendelkező virtuális géphez nem támogatott.  |
|  OperationNotAllowed  |  Az ilyen méretű virtuális géphez csatlakoztatható adatlemezek maximális száma {0}.  |
|  OperationNotAllowed  |  BLOB-alapú lemez hozzáadása a felügyelt lemezekkel rendelkező virtuális géphez nem támogatott.  |
|  OperationNotAllowed  |  A ({0}z) "" művelet nem engedélyezett{1}a (z) "" rendszerképen, mert a rendszerkép meg van jelölve törlésre. Csak újra próbálkozhat a törlési művelettel (vagy megvárhatja, amíg folyamatban van egy Befejezés).  |
|  OperationNotAllowed  |  A ({0}z) "" művelet nem engedélyezett{1}a (z) "" virtuális gépen, mert a virtuális gép általánosítva van.  |
|  OperationNotAllowed  |  A ({0}z) "" művelet nem engedélyezett, mert{1}a (z) "" visszaállítási pont gyűjteménye törlésre van megjelölve.  |
|  OperationNotAllowed  |  A ({0}z) "" művelet nem engedélyezett a{1}(z) "" virtuálisgép-bővítményen, mert a törlésre van megjelölve. Csak újra próbálkozhat a törlési művelettel (vagy megvárhatja, amíg folyamatban van egy Befejezés).  |
|  OperationNotAllowed  |  A ({0}z) "" művelet nem engedélyezett,{1}mert a (z) "" Virtual Machines a ({2}z) "" rendszerkép használatával lett kiépítve.  |
|  OperationNotAllowed  |  A ({0}z) "" művelet nem engedélyezett, mert a{1}(z) "" virtuális gép{2}méretezési csoport jelenleg a (z) "" rendszerképet használja.  |
|  OperationNotAllowed  |  A ({0}z) "" művelet nem engedélyezett{1}a (z) "" virtuális gépen, mert a virtuális gép meg van jelölve törlésre. Csak újra próbálkozhat a törlési művelettel (vagy megvárhatja, amíg folyamatban van egy Befejezés).  |
|  OperationNotAllowed  |  A ({0}z) "" művelet nem engedélyezett{1}a (z) "" virtuális gépen, mert a virtuális gép le van foglalva vagy fel van jelölve a kiosztásra.  |
|  OperationNotAllowed  |  A ({0}z) "" művelet nem engedélyezett{1}a (z) "" virtuális gépen, mert a virtuális gép fut. Ha a virtuális gépet a vendég operációs rendszeren belülről állítja le, kapcsolja ki explicit módon.  |
|  OperationNotAllowed  |  A ({0}z) "" művelet nem engedélyezett{1}a (z) "" virtuális gépen, mert a virtuális gép nincs lefoglalva.  |
|  OperationNotAllowed  |  A ({0}z) "" művelet nem engedélyezett{1}a (z) ""{2}virtuális gépen, mert a virtuális gép "" kiterjesztése sikertelen állapotban van.  |
|  OperationNotAllowed  |  A ({0}z) "" művelet nem engedélyezett{1}a (z) "" virtuális gépen, mert egy másik művelet van folyamatban.  |
|  OperationNotAllowed  |  A (z{0}) "" művelethez a{1}(z) "" virtuális gép általánosítása szükséges.  |
|  OperationNotAllowed  |  A művelet végrehajtásához a virtuális gépnek futnia kell (vagy a futtatásra van állítva).  |
|  OperationNotAllowed  |  A GB méretű {0}lemez, amely kisebb, mint a lemezképben lévő megfelelő lemez mérete {1}(GB), nem megengedett.  |
|  OperationNotAllowed  |  A (z) "{0}" kezelő virtuálisgép-méretezési csoportjának bővítményei csak a virtuálisgép-méretezési csoport létrehozásakor vehetők fel.  |
|  OperationNotAllowed  |  A (z) "{0}" kezelő virtuálisgép-méretezési csoportjának bővítményei csak a virtuálisgép-méretezési csoport törlésének időpontjában törölhetők.  |
|  OperationNotAllowed  |  A ({0}z) "" virtuális gép már felügyelt lemezeket használ.  |
|  OperationNotAllowed  |  A ({0}z) "" virtuális gép a "klasszikus{1}" rendelkezésre állási csoporthoz tartozik. Frissítse a rendelkezésre állási készletet az "igazított" SKU használatára, majd próbálja megismételni az átalakítást.  |
|  OperationNotAllowed  |  A rendszerképből létrehozott virtuális gép nem rendelkezhet blob-alapú lemezekkel. Minden lemeznek felügyelt lemeznek kell lennie.  |
|  OperationNotAllowed  |  A rögzítési művelet nem hajtható végre, mert a virtuális gép nincs általánosítva.  |
|  OperationNotAllowed  |  A ({0}z) virtuális gépen lévő felügyeleti műveletek nem megengedettek, mert a virtuálisgép-lemezek felügyelt lemezekre lettek konvertálva.  |
|  OperationNotAllowed  |  Egy folyamatban lévő művelet megváltoztatja a virtuális gép {0} energiaellátási állapotát {1}a következőre:. Némi idő elteltével {2} végezze el a műveletet.  |
|  OperationNotAllowed  |  A virtuális gép nem adható hozzá vagy nem frissíthető. Előfordulhat, hogy a {0} kért VM-méret nem érhető el a meglévő foglalási egységben. További információk a virtuális gépek átméretezésével https://aka.ms/azure-resizevmkapcsolatos stratégiáról:.  |
|  OperationNotAllowed  |  Nem lehet átméretezni a virtuális gépet, mert {0} a kért méret nem érhető el abban a fürtben, ahol a rendelkezésre állási csoport jelenleg le van foglalva. A rendelkezésre álló méretek a {1}következők:. További információk a virtuális gépek átméretezésével https://aka.ms/azure-resizevmkapcsolatos stratégiáról:.  |
|  OperationNotAllowed  |  Nem lehet átméretezni a virtuális gépet, mert {0} a kért méret nem érhető el abban a fürtben, amelyben a virtuális gép jelenleg le van foglalva. Ha szeretné átméretezni a {1} virtuális gépet, hogy felszabadítsa a lefoglalást (ez a leállítási művelet a Azure Portal), majd próbálja megismételni az átméretezési műveletet. További információk a virtuális gépek átméretezésével https://aka.ms/azure-resizevmkapcsolatos stratégiáról:.  |
|  OSProvisioningClientError  |  Az operációs rendszer üzembe helyezése nem sikerült{0}a (z) "" virtuális géphez, mert a vendég operációs rendszer jelenleg ki van építve.  |
|  OSProvisioningClientError  |  A (z) "{0}" virtuális gép operációs rendszerének üzembe helyezése nem sikerült. Hiba részletei: {1} győződjön meg arról, hogy a rendszerkép megfelelően előkészített (általánosított). <ul><li>Windows-utasítások:https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/  </li></ul> |
|  OSProvisioningClientError  |  Az SSH-gazda kulcsának létrehozása sikertelen volt. Hiba részletei: {0}. A probléma megoldásához ellenőrizze, hogy a Linux-ügynök megfelelően van-e beállítva. <ul><li>Az utasításokat a következő helyen tekintheti meg:https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux/ </li></ul> |
|  OSProvisioningClientError  |  A virtuális géphez megadott Felhasználónév érvénytelen ehhez a Linux-disztribúcióhoz. Hiba részletei: {0}.  |
|  OSProvisioningInternalError  |  Belső hiba miatt nem sikerült a (z{0}) "" virtuális gép operációs rendszerének üzembe helyezése.  |
|  OSProvisioningTimedOut  |  A (z) "{0}" virtuális gép operációs rendszer általi üzembe helyezése nem fejeződik be a megadott időn belül. A virtuális gép továbbra is befejezheti a kiépítés sikerességét. A kiépítési állapotot később ellenőrizze.  |
|  OSProvisioningTimedOut  |  A (z) "{0}" virtuális gép operációs rendszer általi üzembe helyezése nem fejeződik be a megadott időn belül. A virtuális gép továbbra is befejezheti a kiépítés sikerességét. A kiépítési állapotot később ellenőrizze. Győződjön meg arról is, hogy a rendszerkép megfelelően előkészített (általánosított).   <ul><li>Windows-utasítások:https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Linux-utasítások:https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OSProvisioningTimedOut  |  A (z) "{0}" virtuális gép operációs rendszer általi üzembe helyezése nem fejeződik be a megadott időn belül. A rendszer azonban a virtuális gép vendég ügynökét észlelte. Ez azt sugallja, hogy a vendég operációs rendszer nem lett megfelelően felkészülve a virtuálisgép-rendszerképként való használatra (CreateOption = FromImage). A probléma megoldásához használja a VHD-t a következő módon: CreateOption = csatolja vagy készítse el megfelelően a rendszerképként való használatra:   <ul><li>Windows-utasítások:https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Linux-utasítások:https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OverConstrainedAllocationRequest  |  A virtuális gép szükséges mérete jelenleg nem érhető el a kiválasztott helyen.  |
|  ResourceUpdateBlockedOnPlatformUpdate  |  Az erőforrás jelenleg nem frissíthető, mert folyamatban van a platform frissítése. Próbálkozzon újra később.  |
|  StorageAccountLimitation  |  A (z{0}) "" Storage-fiók nem támogatja a lemezek létrehozásához szükséges Blobok használatát.  |
|  StorageAccountLimitation  |  A (z{0}) "" Storage-fiók túllépte a lefoglalt kvótát.  |
|  StorageAccountLocationMismatch  |  Nem oldható fel a Storage {0}-fiók. Győződjön meg arról, hogy a tárolási erőforrás-szolgáltatón keresztül lett létrehozva a számítási erőforrással megegyező helyen.  |
|  StorageAccountNotFound  |  A Storage {0} -fiók nem található. Győződjön meg arról, hogy a Storage-fiók nincs törölve, és ugyanahhoz az Azure-helyhez tartozik, mint a virtuális gép.  |
|  StorageAccountNotRecognized  |  Használjon tárolási erőforrás-szolgáltató által kezelt Storage-fiókot. A {0} használata nem támogatott.  |
|  StorageAccountOperationInternalError  |  Belső hiba történt a Storage-fiók {0}elérésekor.  |
|  StorageAccountSubscriptionMismatch  |  A Storage {0} -fiók nem tartozik {1}az előfizetéshez.  |
|  StorageAccountTooBusy  |  A (z{0}) "" Storage-fiók jelenleg túl elfoglalt. Vegye fontolóra egy másik fiók használatát.  |
|  StorageAccountTypeNotSupported  |  A {0} lemez {1} a blob Storage-fiókot használja. Próbálkozzon újra az általános célú Storage-fiókkal.  |
|  StorageAccountTypeNotSupported  |  A {1} Storage {0} -fiók típusa. A rendszerindítási diagnosztika támogatja {2} a Storage-fiókok típusait.  <ul><li>Ez a hiba akkor fordul elő, ha a Premium Storage-fiókot használja a rendszerindítási diagnosztika számára. További információ: [a rendszerindítási diagnosztika használata](boot-diagnostics.md). </li></ul> |
|  SubscriptionNotAuthorizedForImage  |  Az előfizetés nincs engedélyezve.  |
|  TargetDiskBlobAlreadyExists  |  A {0} blob már létezik. Adjon meg egy másik blob URI-t egy új, üres adatlemez{1}létrehozásához.  |
|  TargetDiskBlobAlreadyExists  |  A rögzítési művelet nem folytatható, {0} mert a célként megadott lemezkép-blob már létezik, és a VHD-Blobok felülírására szolgáló jelző nincs beállítva. Törölje a blobot, vagy állítsa be a jelzőt a VHD-Blobok felülírásához, majd próbálkozzon újra.  |
|  TargetDiskBlobAlreadyExists  |  A rögzítési művelet nem folytatható, {0} mert a célként megadott lemezkép blobja aktív bérlettel rendelkezik.   |
|  TargetDiskBlobAlreadyExists  |  A {0} blob már létezik. Adjon meg egy másik blob-URI-t célként a{1}(z) "" lemez számára.  |
|  TooManyVMRedeploymentRequests  |  Túl sok újratelepítési kérelem érkezett a (z){0}"" virtuális gépre vagy a virtuális géppel azonos üzemmódú található virtuális gépekre. Próbálkozzon újra később.  |
|  VHDSizeInvalid  |  A (z) " {0} {1}" lemez megadott lemezének mérete érvénytelen {2} a blobtal. A lemez méretének és {3} {4}közé kell esnie.  |
|  VMAgentStatusCommunicationError  |  A ({0}z) "" virtuális gép nem jelentett állapotot a virtuálisgép-ügynökhöz vagy-bővítményekhez. Ellenőrizze, hogy a virtuális gép rendelkezik-e futó virtuálisgép-ügynökkel, és képes-e kimenő kapcsolatokat létesíteni az Azure Storage szolgáltatással.  |
|  VMArtifactRepositoryInternalError  |  Hiba történt az összetevő-adattárral folytatott kommunikáció során a virtuálisgép-összetevők részleteinek lekéréséhez.  |
|  VMArtifactRepositoryInternalError  |  Belső hiba történt a virtuális gép összetevőinek az összetevő-tárházból való beolvasása során.  |
|  VMExtensionHandlerNonTransientError  |  A ({0}z) "" kezelője hibát jelentett{1}a (z) ""{2}virtuálisgép-bővítmény számára a következő{3}terminál-hibakódtal: ""  |
|  VMExtensionManagementInternalError  |  Belső hiba történt a virtuálisgép-bővítmény ({0}) feldolgozásakor.  |
|  VMExtensionManagementInternalError  |  Több hiba történt a virtuálisgép-bővítmények előkészítése során. Részletekért lásd a virtuálisgép-bővítmény példányának nézetét.  |
|  VMExtensionProvisioningError  |  A virtuális gép a (z) "{0}" bővítmény feldolgozásakor hibát jelzett. Hibaüzenet: "{1}".  |
|  VMExtensionProvisioningError  |  A virtuális gépen több virtuálisgép-bővítményt nem sikerült kiépíteni. A részletekért tekintse meg a virtuálisgép-bővítmény példányának nézetét.  |
|  VMExtensionProvisioningTimeout  |  A ({0}z) "" virtuálisgép-bővítmény üzembe helyezése túllépte az időkorlátot. Előfordulhat, hogy a bővítmény telepítése túl hosszú ideig tart, vagy a bővítmény állapota nem szerezhető be.  |
|  VMMarketplaceInvalidInput  |  Ha nem Piactéri rendszerképből hoz létre virtuális gépet, nem szükséges megterveznie az adatokat, távolítsa el a csomag információit a kérelemben. Az {0}operációsrendszer-lemez neve:.  |
|  VMMarketplaceInvalidInput  |  A vásárlási adatok nem egyeznek. Nem lehet üzembe helyezni a piactér lemezképéről. Az {0}operációsrendszer-lemez neve:.  |
|  VMMarketplaceInvalidInput  |  A virtuális gépek Piactéri rendszerképből való létrehozásához a kérelemben meg kell tervezni a szükséges információkat. Az {0}operációsrendszer-lemez neve:.  |
|  VMNotFound  |  A (z{0}) "" virtuális gép nem található.  |
|  VMRedeploymentFailed  |  {0}A virtuális gép újratelepítése belső hiba miatt nem sikerült. Próbálkozzon újra később.  |
|  VMRedeploymentTimedOut  |  A (z) "{0}" virtuális gép újratelepítése nem fejeződik be a megadott időn belül. Néhány perc múlva sikeresen befejeződik. Másik lehetőségként újra megpróbálkozhat a kéréssel.  |
|  VMStartTimedOut  |  A ({0}z) "" virtuális gép nem indult el a megadott időn belül. A virtuális gép továbbra is sikeresen elindulhat. Ellenőrizze a Power állapotot később.  |


## <a name="next-steps"></a>További lépések
Ha további segítségre van szüksége, vegye fel a kapcsolatot az Azure [-szakértőkkel az MSDN Azure-ban, és stack overflow fórumokon](https://azure.microsoft.com/support/forums/)is. Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése**lehetőséget.
