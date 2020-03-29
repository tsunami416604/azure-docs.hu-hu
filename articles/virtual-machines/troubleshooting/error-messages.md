---
title: Gyakori virtuálisgép-hibakódok az Azure-ban | Microsoft dokumentumok
description: Ismerje meg a virtuális gépek Azure-beli kiépítése és kezelése során előforduló gyakori hibakódok némelyikét
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76543018"
---
# <a name="understand-common-error-messages-when-you-manage-virtual-machines-in-azure"></a>A gyakori hibaüzenetek ismertetése a virtuális gépek kezelésekor az Azure-ban

Ez a cikk ismerteti a leggyakoribb hibakódok és üzenetek, amelyek az Azure-ban virtuális gépek (VM-ek) létrehozásakor vagy kezelése során találkozhatnak.

>[!NOTE]
> Ezen az oldalon megjegyzéseket hagyhat visszajelzésért, vagy az [Azure visszajelzésein](https://feedback.azure.com/forums/216843-virtual-machines) keresztül #azerrormessage címkével.

## <a name="error-response-format"></a>Hibaválasz formátuma 
Az Azure virtuális gépek a következő JSON-formátumot használják a hibaelhárításhoz:

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

A hibaválasz mindig tartalmaz egy állapotkódot és egy hibaobjektumot. Minden hibaobjektum mindig tartalmaz egy hibakódot és egy üzenetet. Ha a virtuális gép sablonnal jön létre, a hibaobjektum egy olyan részletszakaszt is tartalmaz, amely a hibakódok és az üzenet belső szintjét tartalmazza. Normális esetben a legbelső hibaüzenet a gyökérhiba.


## <a name="common-virtual-machine-management-errors"></a>Gyakori virtuálisgép-kezelési hibák

Ez a szakasz a virtuális gépek kezelése során előforduló gyakori hibaüzeneteket sorolja fel:

|  Hibakód  |  Hibaüzenet  |  
|  :------| :-------------|  
|  AcquireDiskLeaseFailed  |  Nem sikerült beszerezni a{0}bérletet, miközben {1}lemezt hozott létre ' ' blob használatával URI-val. Blob már használatban van.  |  
|  Nem sikerült lefoglalás  |  A foglalás nem sikerült. Próbálja meg csökkenteni a virtuális gép méretét vagy a virtuális gépek számát, próbálkozzon később, vagy próbálja meg telepíteni egy másik rendelkezésre állási csoportra vagy más Azure-helyre.  |  
|  Nem sikerült lefoglalás  |  A virtuális gép foglalása belső hiba miatt nem sikerült. Próbálkozzon később, vagy próbálja meg egy másik helyre telepíteni.  |
|  Nem található összetevő  |  A "Publisher "{0}" című{1}virtuális gépbővítmény nem található{2}a helyen " ' .  |
|  Nem található összetevő  |  A kiterjesztés{0}a publisher{1}' ', type{2}' és type handler version ' " kiterjesztéssel nem található a bővítménytárházban.  |
|  ArtifactVersionNem található  |  Nem található olyan verzió a műterméktárban,{0}amely megfelelne a kért verziónak .  |
|  ArtifactVersionNem található  |  Nem található olyan verzió a műterméktárházban,{0}amely megfelelne a{1}kért verziónak{2}a " ' a virtuális gép bővítményhez a publisher ' ' és type ' ".No version found in the artifact repository that satisfies the requested version ' ' for VM extension with publisher ' ' and type ' '.  |
|  AttachDiskWhileBeingDetached  |  A ' '{0}adatlemez nem{1}csatolható a virtuális géphez ', mert a lemez levan választva. Várjon, amíg a lemez teljesen leválik, majd próbálkozzon újra.  |
|  BadRequest (Hibás kérés)  |  Az igazításhoz igazított rendelkezésre állási készletek még nem támogatottak ebben a régióban.  |
|  BadRequest (Hibás kérés)  |  Felügyelt lemezekkel rendelkező virtuális gép hozzáadása nem felügyelt rendelkezésre állási csoporthoz, vagy egy virtuális gép hozzáadása blob alapú lemezekkel a felügyelt rendelkezésre állási csoporthoz nem támogatott. Hozzon létre egy rendelkezésre állási készletet a "felügyelt" tulajdonságkészlettel, hogy felügyelt lemezekkel rendelkező virtuális gép hozzáadása.  |
|  BadRequest (Hibás kérés)  |  A felügyelt lemezek nem támogatottak ebben a régióban.  |
|  BadRequest (Hibás kérés)  |  Kezelőnként több VMExtension nem támogatott{0}az operációs rendszer típusa ' '. VMExtension{1}' '{2}' kezelővel ' ' , már hozzáadva vagy a bemenetben megadva.  |
|  BadRequest (Hibás kérés)  |  A{0}' " művelet nem{1}támogatott az erőforrásban ' ' a felügyelt lemezekkel.  |
|  CertificateHelytelenül formázott  |  A titkos JSON-ábrázolás, {0} amelyből lekért adatok nem megfelelően formázott PFX fájlból származnak, vagy a megadott jelszó nem dekódolja a PFX fájlt.  |
|  CertificateHelytelenül formázott  |  A beolvasott {0} adatok nem deszerializálhatók a JSON-ban.  |
|  Ütközés  |  A lemezátméretezés csak virtuális gép létrehozásakor engedélyezett, vagy ha a virtuális gép fel van terhelve.  |
|  Ütköző UserInput  |  A{0}Disk ' ' ' nem csatolható,{1}mert a lemez már a virtuális gép " tulajdonában van.  |
|  Ütköző UserInput  |  A forrás- és célerőforrás-csoportok megegyeznek.  |
|  Ütköző UserInput  |  A lemez {0} forrás- és céltárfiókjai eltérőek.  |
|  Konténermáronlease  |  Már van egy bérlet a tárolótárolón, {0}amely a blobot URI-val tárolja.  |
|  CrossSubscriptionmoveWithKeyVaultResources  |  Az Erőforrások áthelyezése kérelem olyan KeyVault-erőforrásokat {0}tartalmaz, amelyekre a kérelemben egy vagy több s hivatkozik. Ez jelenleg nem támogatott a Cross subscription Move. Ellenőrizze a KeyVault erőforrás-azonosítók hibarészleteit.  |
|  DiagnosticsOperationInternalError (Diagnosztikai műveletInternalError)  |  Belső hiba történt a virtuális gép {0}diagnosztikai profiljának feldolgozása közben.  |
|  DiskBlobAlreadyinusebyanotherDisk  |  Blob {0} már használatban van egy másik{1}lemez tartozó virtuális gép " .Blob már használatban van egy másik lemez hez tartozó virtuális gép ". Megvizsgálhatja a blob metaadatait a lemez hivatkozási információihoz.  |
|  DiskBlobNem található  |  Nem található a VHD {0} blob{1}URI-val a disk ' ' lemezhez.  |
|  DiskBlobNem található  |  Nem található a VHD {0}blob az URI-val.  |
|  DiskEncryptionKeySecretMissingTags  |  {0}A titokban nincsenek meg a {1} címkék. Frissítse a titkos verziót, adja hozzá a szükséges címkéket, és próbálkozzon újra.  |
|  DiskEncryptionKeySecretUnwrapFailedFailed  |  Titkos {0} érték kicsomagolása {1} a kulcs használatával nem sikerült.  |
|  DiskImageNotReady  |  A {0} lemezkép {1} állapota van. Próbálkozzon újra, ha a kép készen áll.  |
|  DiskPreparationError (DiskPreparationError)  |  Egy vagy több hiba történt a virtuális gép lemezek előkészítése közben. A részleteket lásd a lemezpéldány nézetben.  |
|  DiskProcessingError (Lemezfeldolgozási hiba)  |  A lemezfeldolgozás leállt, mivel a virtuális gép más lemezekkel rendelkezik a meghibásodott lemezeken.  |
|  ImageBlobNem található  |  Nem található a VHD {0} blob{1}URI-val a disk ' ' lemezhez.  |
|  ImageBlobNem található  |  Nem található a VHD {0}blob az URI-val.  |
|  IncorrectDiskBlobType típus  |  A lemezblobok csak lapblob típusúak lehetnek. Blob {0} lemezhez{1}' ' típusú blokkblob.  |
|  IncorrectDiskBlobType típus  |  A lemezblobok csak lapblob típusúak lehetnek. A {0} blob típusa{1}' '.  |
|  IncorrectImageBlobType  |  A lemezblobok csak lapblob típusúak lehetnek. Blob {0} lemezhez{1}' ' típusú blokkblob.  |
|  IncorrectImageBlobType  |  A lemezblobok csak lapblob típusúak lehetnek. A {0} blob típusa{1}' '.  |
|  InternalOperationError (Belső művelethiba)  |  A tárfiók {0}nem oldható fel. Győződjön meg arról, hogy a tárolási erőforrás-szolgáltatón keresztül jött létre a számítási erőforrással azonos helyen.  |
|  InternalOperationError (Belső művelethiba)  |  {0}a célkereső feladatok nem sikerültek.  |
|  InternalOperationError (Belső művelethiba)  |  Hiba történt a virtuális gép hálózati profiljának{0}érvényesítése során.  |
|  InvalidAccountType  |  Az AccountType {0} érvénytelen.  |
|  InvalidParameter paraméter  |  A paraméter {0} értéke érvénytelen.  |
|  InvalidParameter paraméter  |  A megadott rendszergazdai jelszó nem engedélyezett.  |
|  InvalidParameter paraméter  |  "A megadott jelszónak {0}{1} a - karakterek között {2} kell lennie, és meg kell felelnie legalább a jelszó összetettségére vonatkozó követelményeknek a következőkből: <ol><li> Nagybetűs karaktert tartalmaz</li><li>Kisbetűs karaktert tartalmaz</li><li>Numerikus számjegyet tartalmaz</li><li>Különleges karaktert tartalmaz.</li></ol>  |
|  InvalidParameter paraméter  |  A megadott rendszergazdai felhasználónév nem engedélyezett.  |
|  InvalidParameter paraméter  |  Meglévő operációsrendszer-lemez csatlakoztatása, ha a virtuális gép platformról vagy felhasználói lemezképből jön létre.  |
|  InvalidParameter paraméter  |  A {0} tároló neve érvénytelen. A tárolónevek nek 3-63 karakter hosszúságúak nak kell lenniük, és csak kisbetűs alfanumerikus karaktereket és kötőjelet tartalmazhatnak. A kötőjelet alfanumerikus karakternek kell megelőznie és követnie.  |
|  InvalidParameter paraméter  |  Az {0} URL-címtároló {1} neve érvénytelen. A tárolónevek nek 3-63 karakter hosszúságúak nak kell lenniük, és csak kisbetűs alfanumerikus karaktereket és kötőjelet tartalmazhatnak. A kötőjelet alfanumerikus karakternek kell megelőznie és követnie.  |
|  InvalidParameter paraméter  |  A blob neve {0} az URL-ben tartalmaz egy perjelet. Ez jelenleg nem támogatott lemezek.  |
|  InvalidParameter paraméter  |  Az {0} URI nem tűnik helyes blob URI.The URI does not look to be correct blob URI.  |
|  InvalidParameter paraméter  |  A '{0}' ' nevű lemez {1}már ugyanazt a logikai alnevet használja: .  |
|  InvalidParameter paraméter  |  Már létezik{0}' ' nevű lemez.  |
|  InvalidParameter paraméter  |  A megadott képhivatkozásban már definiált lemezhez nem adhatók meg felhasználói képfelülírások.  |
|  InvalidParameter paraméter  |  A '{0}' ' nevű lemez {1}már ugyanazt a virtuális merevlemez URL-címét használja.  |
|  InvalidParameter paraméter  |  A megadott tartaléktartomány-számnak {0} a {1} {2}tartományba kell esnie.  |
|  InvalidParameter paraméter  |  A licenctípus {0} érvénytelen. Az érvényes licenctípusok a következők: Windows_Client vagy Windows_Server, a kis- és nagybetűk megkülönböztetése.  |
|  InvalidParameter paraméter  |  A Linux állomásnév nem haladhatja meg {0} {1}a karakterekhosszát, és nem tartalmazhatja a következő karaktereket: .  |
|  InvalidParameter paraméter  |  Az Ssh nyilvános kulcsok célelérési útja {0} jelenleg az alapértelmezett értékre korlátozódik a Linux-kiépítési ügynök ismert problémája miatt.  |
|  InvalidParameter paraméter  |  Már létezik {0} lemez a logikai és erőforrás-ásznál.  |
|  InvalidParameter paraméter  |  A {0} kérelem előfizetésének meg {1} kell egyeznie a felügyelt lemezazonosítóban található előfizetéssel.  |
|  InvalidParameter paraméter  |  Az OSProfile rendszerben az egyéni adatoknak Base64 kódolásban és legfeljebb karakterhosszúságúnak {0} kell lenniük.  |
|  InvalidParameter paraméter  |  Az URL-ben {0} lévő{1}blobnévnek ' ' kiterjesztéssel kell végződnie.  |
|  InvalidParameter paraméter  |  {0}' nem érvényes rögzített virtuális merevlemez-blob névelőtagja. Az érvényes előtag megegyezik a regex ' ' (regex '{1}' .  |
|  InvalidParameter paraméter  |  Tanúsítványok nem adhatók hozzá a virtuális géphez, ha a virtuálisgép-ügynök nincs kiépítve.  |
|  InvalidParameter paraméter  |  Már létezik {0} lemez a logikai és erőforrás-ásznál.  |
|  InvalidParameter paraméter  |  Nem lehet létrehozni a virtuális {0} gép, mert a kért méret nem érhető el a fürtben, ahol a rendelkezésre állási csoport jelenleg lefoglalt. A rendelkezésre {1}álló méretek a következők: . További információ a virtuális gép https://aka.ms/azure-resizevmátméretezési stratégiájáról a ban.  |
|  InvalidParameter paraméter  |  A kért virtuális {0} gép mérete nem érhető el az aktuális régióban. Az aktuális régióban elérhető {1}méretek a következők: . További információ az egyes régiókban elérhető https://aka.ms/azure-regionsvirtuálisgép-méretekről a.  |
|  InvalidParameter paraméter  |  A kért virtuális {0} gép mérete nem érhető el az aktuális régióban. További információ az egyes régiókban elérhető https://aka.ms/azure-regionsvirtuálisgép-méretekről a.  |
|  InvalidParameter paraméter  |  A Windows rendszergazdai felhasználóneve nem lehet hosszabb {0} karakternél hosszú, pont(.) végződésű, és nem tartalmazhatja a következő karaktereket: {1}.  |
|  InvalidParameter paraméter  |  A Windows számítógépneve {0} nem lehet hosszabb karakternél hosszú, nem {1}lehet teljesen numerikus, és nem tartalmazhat a következő karaktereket: .  |
|  MissingMoveDependentResources  |  Az áthelyezési erőforrások kérése nem tartalmazza az összes függő erőforrást. Ellenőrizze a hiányzó erőforrásazonosítók hibarészleteit.  |
|  MoveResourcesHaveInvalidState  |  Az Erőforrások áthelyezése kérelem érvénytelen tárfiókokhoz társított virtuális gépeket tartalmaz. Ellenőrizze az erőforrás-azonosítók és a hivatkozott tárfióknevek adatait.  |
|  MoveResourcesHavePendingOperations  |  Az áthelyezési erőforrások ra vonatkozó kérelem olyan erőforrásokat tartalmaz, amelyekhez művelet van függőben. Ellenőrizze az erőforrás-azonosítók adatait. Próbálkozzon újra a művelettel, amint a függőben lévő műveletek befejeződtek.  |
|  Az áthelyezési erőforrások nem találhatók  |  Az áthelyezési erőforrások ra vonatkozó kérelem nem található erőforrásokat tartalmaz. Ellenőrizze az erőforrás-azonosítók adatait.  |
|  NetworkingInternalOperationError (HálózattalInternalOperationHiba)  |  Ismeretlen hálózati foglalási hiba.  |
|  NetworkingInternalOperationError (HálózattalInternalOperationHiba)  |  Ismeretlen hálózati foglalási hiba  |
|  NetworkingInternalOperationError (HálózattalInternalOperationHiba)  |  Belső hiba történt a virtuális gép hálózati profiljának feldolgozása során.  |
|  NotFound  |  A rendelkezésre állási készlet {0} nem található.  |
|  NotFound  |  A kérelemben{0}megadott Source Virtual Machine ' ' nem létezik ezen az Azure-helyen.  |
|  NotFound  |  Az azonosítóval {0} rendelkező bérlő nem található.  |
|  NotFound  |  A {0} kép nem található.  |
|  Nem támogatott  |  A licenc {0}típusa , de {1} a lemezképblob nem a helyszíni.  |
|  A művelet nem engedélyezett  |  Az {0} elérhetőségi csoport nem törölhető. A rendelkezésre állási csoport törlése előtt győződjön meg arról, hogy nem tartalmaz virtuális gép.  |
|  A művelet nem engedélyezett  |  A rendelkezésre állási készlet termékváltozatának "Igazított" és "Klasszikus" között történő módosítása nem engedélyezett.  |
|  A művelet nem engedélyezett  |  Nem lehet módosítani a bővítményeket a virtuális gépben, ha a virtuális gép nem fut.  |
|  A művelet nem engedélyezett  |  A rögzítési művelet csak blob alapú lemezekkel rendelkező virtuális gépeken támogatott. Kérjük, használja a "Lemezkép" erőforrás API-k at egy felügyelt virtuális gépről lemezkép létrehozásához.  |
|  A művelet nem engedélyezett  |  Az {0} erőforrás nem hozható {1} létre a Kép lemezképből, amíg a Kép létrehozása nem történt meg.  |
|  A művelet nem engedélyezett  |  A titkosítás frissítéseBeállítások nem engedélyezett, ha a virtuális gép le van foglalva, kérjük, próbálja meg újra a virtuális gép felszabadított  |
|  A művelet nem engedélyezett  |  A felügyelt lemez hozzáadása a blob alapú lemezekkel rendelkező virtuális géphez nem támogatott.  |
|  A művelet nem engedélyezett  |  Az ilyen méretű virtuális géphez csatolható adatlemezek maximális {0}száma a.  |
|  A művelet nem engedélyezett  |  A blob alapú lemez hozzáadása a virtuális gép hez felügyelt lemezekkel nem támogatott.  |
|  A művelet nem engedélyezett  |  A{0}' művelet nem engedélyezett{1}a "Kép" képen, mivel a kép törlésre van megjelölve. Csak újra próbálkozhat a Törlés művelettel (vagy megvárhatja egy folyamatban lévő művelet befejezését).  |
|  A művelet nem engedélyezett  |  Operation{0}' ' nem engedélyezett{1}a virtuális gép ', mivel a virtuális gép általánossá.  |
|  A művelet nem engedélyezett  |  A{0}' " művelet nem engedélyezett{1}visszaállítási pont gyűjteményként ' ' törlésre van megjelölve.  |
|  A művelet nem engedélyezett  |  A{0}' " művelet nem engedélyezett{1}a virtuális gép bővítményén '' , mivel törlésre van megjelölve. Csak újra próbálkozhat a Törlés művelettel (vagy megvárhatja egy folyamatban lévő művelet befejezését).  |
|  A művelet nem engedélyezett  |  A{0}' " művelet nem engedélyezett, mivel a virtuális gépek '{1}' kiépítés alatt állnak a "Image "{2}használatával.  |
|  A művelet nem engedélyezett  |  A{0}' " művelet nem engedélyezett,{1}mivel a Virtual Machine{2}ScaleSet ' ' jelenleg a " image ' ".  |
|  A művelet nem engedélyezett  |  Operation{0}' ' nem engedélyezett{1}a virtuális gép ', mert a virtuális gép törlésre van megjelölve. Csak újra próbálkozhat a Törlés művelettel (vagy megvárhatja egy folyamatban lévő művelet befejezését).  |
|  A művelet nem engedélyezett  |  A{0}' operation ' nem{1}engedélyezett a virtuális gépen ", mivel a virtuális gép felvan terhelve, vagy felszabadítottként van megjelölve.  |
|  A művelet nem engedélyezett  |  Operation{0}' ' nem engedélyezett{1}a virtuális gép ' , mivel a virtuális gép fut. Kérjük, kapcsolja ki kifejezetten abban az esetben, ha leállítja a virtuális gép belülről a vendég operációs rendszer.  |
|  A művelet nem engedélyezett  |  Operation{0}' ' nem engedélyezett{1}a virtuális gép ', mert a virtuális gép nem felszabadított.  |
|  A művelet nem engedélyezett  |  Az{0}' " művelet nem{1}engedélyezett a virtuális gép{2}" ' számára, mivel a virtuális gép kiterjesztése ' ' sikertelen állapotban van.  |
|  A művelet nem engedélyezett  |  A{0}' művelet nem engedélyezett{1}a virtuális gépen ', mert egy másik művelet van folyamatban.  |
|  A művelet nem engedélyezett  |  A művelet{0}' ' '{1}a virtuális gép " ' általánossá kell tenni.  |
|  A művelet nem engedélyezett  |  A művelet megköveteli, hogy a virtuális gép fut (vagy futtatásra van beállítva).  |
|  A művelet nem engedélyezett  |  A GB {0}méretű lemez, amely kisebb, mint a kép megfelelő lemezének GB mérete, {1}nem engedélyezett.  |
|  A művelet nem engedélyezett  |  A kezelő ' '{0}' virtuális gép méretezési készletbővítményei csak a virtuális gép méretezési készletének létrehozásakor adhatók hozzá.  |
|  A művelet nem engedélyezett  |  A kezelő ' '{0}' virtuális gép méretezési készletbővítményei csak a Virtuálisgép-méretezési készlet törlésekor törölhetők.  |
|  A művelet nem engedélyezett  |  A virtuális{0}gép ' ' már felügyelt lemezeket használ.  |
|  A művelet nem engedélyezett  |  A VM '{0}' a 'Classic' rendelkezésre állási készlethez{1}tartozik. Frissítse a rendelkezésre állási készletet az "Igazított" termékváltozat használatához, majd próbálkozzon újra a Konvertálással.  |
|  A művelet nem engedélyezett  |  A rendszerképből létrehozott virtuális gép nem rendelkezhet blob alapú lemezekkel. Minden lemeznek felügyelt lemezeknek kell lennie.  |
|  A művelet nem engedélyezett  |  A rögzítési művelet nem hajtható végre, mert a virtuális gép nincs általánosítva.  |
|  A művelet nem engedélyezett  |  A virtuális gép '{0}' felügyeleti műveletei nem engedélyezettek, mert a virtuálisgép-lemezek et felügyelt lemezekké konvertálja.  |
|  A művelet nem engedélyezett  |  Egy folyamatban lévő művelet megváltoztatja {0} {1}a virtuális gép energiaállapotát. Egy idő {2} után hajtsa végre a műveletet.  |
|  A művelet nem engedélyezett  |  Nem lehet hozzáadni vagy frissíteni a virtuális gép. Előfordulhat, hogy {0} a kért virtuális gép mérete nem érhető el a meglévő foglalási egységben. További információ a virtuális gép https://aka.ms/azure-resizevmátméretezési stratégiájáról a ban.  |
|  A művelet nem engedélyezett  |  Nem lehet átméretezni a virtuális {0} gép, mert a kért méret nem érhető el a fürtben, ahol a rendelkezésre állási készlet jelenleg lefoglalt. A rendelkezésre {1}álló méretek a következők: . További információ a virtuális gép https://aka.ms/azure-resizevmátméretezési stratégiájáról a ban.  |
|  A művelet nem engedélyezett  |  Nem lehet átméretezni a virtuális {0} gép, mert a kért méret nem érhető el a fürtben, ahol a virtuális gép jelenleg le van foglalva. A virtuális gép átméretezése, hogy {1} kérjük, felszabadítsa (ez a Stop művelet az Azure Portalon), és próbálja meg újra az átméretezési műveletet. További információ a virtuális gép https://aka.ms/azure-resizevmátméretezési stratégiájáról a ban.  |
|  OSProvisioningClientError  |  Az operációs rendszer kiépítése nem{0}sikerült a virtuális gép ' ' mert a vendég operációs rendszer kiépítése folyamatban van.  |
|  OSProvisioningClientError  |  A virtuális gép "{0}' operációs rendszer kiépítése nem sikerült. Hiba részletei: {1} Ellenőrizze, hogy a kép megfelelően van-e előkészítve (általános). <ul><li>Útmutató a Windows rendszerhez:https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/  </li></ul> |
|  OSProvisioningClientError  |  Az SSH gazdakulcs-generálása nem sikerült. Hiba részletei: {0}. A probléma megoldásához ellenőrizze, hogy a Linux-ügynök megfelelően van-e beállítva. <ul><li>Az utasításokat a következő helyen teheti meg:https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux/ </li></ul> |
|  OSProvisioningClientError  |  A virtuális géphez megadott felhasználónév érvénytelen ehhez a Linux-disztribúcióhoz. Hiba részletei: {0}.  |
|  OSProvisioningInternalError  |  Az operációs rendszer kiépítése belső{0}hiba miatt nem sikerült a virtuális gép " számára.  |
|  OSProvisioningTimedOut  |  A virtuális gép "{0}' operációs rendszer kiépítése nem fejeződött be a megadott időben. A virtuális gép továbbra is befejezheti a kiépítést. Ellenőrizze később a kiépítési állapotot.  |
|  OSProvisioningTimedOut  |  A virtuális gép "{0}' operációs rendszer kiépítése nem fejeződött be a megadott időben. A virtuális gép továbbra is befejezheti a kiépítést. Ellenőrizze később a kiépítési állapotot. Győződjön meg arról is, hogy a kép megfelelően előkészített (általános).   <ul><li>Útmutató a Windows rendszerhez:https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Utasítások a Linux:https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OSProvisioningTimedOut  |  A virtuális gép "{0}' operációs rendszer kiépítése nem fejeződött be a megadott időben. Azonban a virtuális gép vendégügynök e futás a rendszer. Ez azt sugallja, hogy a vendég operációs rendszer nem megfelelően készült fel a virtuális gép lemezképeként való használathoz (createOption=FromImage). A probléma megoldásához használja a virtuális merevlemezt a CreateOption=Attach fájlhoz, vagy készítse elő megfelelően lemezképként való használatra:   <ul><li>Útmutató a Windows rendszerhez:https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Utasítások a Linux:https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OverConstrainedAllocationRequest  |  A szükséges virtuális gép mérete jelenleg nem érhető el a kiválasztott helyen.  |
|  ResourceUpdateBlockedOnPlatformUpdate  |  Az erőforrás jelenleg nem frissíthető a platform folyamatos frissítése miatt. Próbálkozzon újra később.  |
|  StorageAccountLimitation  |  Tárfiók{0}" ' nem támogatja a lemezek létrehozásához szükséges lapblobokat.  |
|  StorageAccountLimitation  |  A tárfiók "{0}' túllépte a lefoglalt kvótát.  |
|  StorageAccountLocationMismatch  |  A tárfiók {0}nem oldható fel. Győződjön meg arról, hogy a tárolási erőforrás-szolgáltatón keresztül jött létre a számítási erőforrással azonos helyen.  |
|  StorageAccountNem alapítva  |  A {0} tárfiók nem található. Győződjön meg arról, hogy a tárfiók nem törlődik, és ugyanahhoz az Azure-helyhez tartozik, mint a virtuális gép.  |
|  A storageaccountnem ismerhető fel  |  Használjon a storage-szolgáltató által kezelt tárfiókot. {0} Használata nem támogatott.  |
|  StorageAccountOperationInternalError  |  Belső hiba történt a tárfiók elérése közben. {0}  |
|  StorageAccountSubscriptionMismatch  |  A {0} tárfiók nem tartozik {1}az előfizetéshez.  |
|  StorageAccountTooBusy  |  Tárfiók{0}' ' túl foglalt jelenleg. Fontolja meg egy másik fiók használatát.  |
|  StorageAccountTypeNotSupported  |  A {0} {1} lemez egy Blob-tárfiókot használ. Próbálkozzon újra az általános célú tárfiókkal.  |
|  StorageAccountTypeNotSupported  |  A {0} tárfiók {1} típusa van. A Rendszerindítási {2} diagnosztika támogatja a tárfióktípusokat.  <ul><li>Ez a hiba akkor fordul elő, ha a prémium szintű tárfiókot használja a rendszerindítási diagnosztikához. További információt a [Rendszerindítási diagnosztika használata című témakörben talál.](boot-diagnostics.md) </li></ul> |
|  ElőfizetésNotAuthorizedForImage  |  Az előfizetés nincs engedélyezve.  |
|  A TargetDiskBlobMár létezik  |  Blob {0} már létezik. Adjon meg egy másik blob URI-t{1}egy új üres adatlemez " létrehozásához.  |
|  A TargetDiskBlobMár létezik  |  A rögzítési művelet nem {0} folytatható, mert a célképblob már létezik, és a VHD-blobok felülírására jelző nincs beállítva. Törölje a blobot, vagy állítsa be a jelzőt a VHD blobok felülírására és újrapróbálkozására.  |
|  A TargetDiskBlobMár létezik  |  A rögzítési művelet nem {0} folytatható, mert a célképblobon aktív címbérlet van.   |
|  A TargetDiskBlobMár létezik  |  Blob {0} már létezik. Adjon meg egy másik blob URI-t a lemez{1}" " célként.  |
|  TooManyVMRedeploymentRequests  |  Túl sok újratelepítési kérelmek érkeztek{0}a virtuális gép ' vagy a virtuális gépek ugyanabban a rendelkezésre állási halmazban ezzel a virtuális gép. Próbálkozzon később.  |
|  VHDSizeIn érvénytelen  |  A blobgal {0} {2} rendelkező lemez "{1}' lemezméret megadott lemezméret-értéke érvénytelen. A lemezméretének {3} {4}a és a között kell lennie.  |
|  VmAgentStatusCommunicationerror  |  Virtuális gép{0}' ' nem jelentett állapotot a virtuális gép ügynöke vagy bővítményei számára. Ellenőrizze, hogy a virtuális gép rendelkezik-e egy futó virtuálisgép-ügynökkel, és létrehozhat kimenő kapcsolatokat az Azure storage-ba.  |
|  VMArtifactRepositoryInternalError  |  Hiba történt a műterméktárházzal való kommunikáció során a virtuális gép összetevő részleteinek beolvasása közben.  |
|  VMArtifactRepositoryInternalError  |  Belső hiba történt a virtuális gép må±termÃ©k-tárolóból való lekérése közben.  |
|  VMExtensionHandlerNonTransientError  |  Kezelő{0}' ' hibát jelentett a{1}VM extension{2}' ' '{3}esetében, terminálhibakóddal ' és a következő hibaüzenettel: '  |
|  VMExtensionManagementInternalError  |  Belső hiba történt a virtuálisgép-bővítmény "{0}feldolgozása közben.  |
|  VMExtensionManagementInternalError  |  Több hiba történt a virtuálisgép-bővítmények előkészítése közben. A részleteket lásd a VM-bővítménypéldány nézetben.  |
|  VMExtensionProvisioningError  |  A virtuális gép hibát jelentett{0}a ' " bővítmény feldolgozásakor. Hibaüzenet: "{1}".  |
|  VMExtensionProvisioningError  |  Több virtuálisgép-bővítmények nem sikerült kiépíteni a virtuális gép. A részleteket tekintse meg a Virtuálisgép-bővítmény példánynézetben.  |
|  VMExtensionProvisioningTimeout  |  A virtuálisgép-bővítmény{0}' ' kiszolgáltatása időtúljeles. Lehet, hogy a bővítmény telepítése túl sokáig tart, vagy nem sikerült megszerezni a bővítmény állapotát.  |
|  VMMarketplaceInvalidInput  |  Virtuális gép létrehozása egy nem Marketplace-lemezkép nem kell terv információkat, távolítsa el a terv információkat a kérelemben. Az operációs {0}rendszer lemezneve .  |
|  VMMarketplaceInvalidInput  |  A beszerzési adatok nem egyeznek. Nem lehet telepíteni a Piactérről lemezkép. Az operációs {0}rendszer lemezneve .  |
|  VMMarketplaceInvalidInput  |  Virtuális gép létrehozása a Marketplace-lemezkép hez terv adatokat a kérelemben. Az operációs {0}rendszer lemezneve .  |
|  VMNem található  |  A virtuális{0}gép ' ' nem található.  |
|  VMRedeploymentFailed  |  Virtuális gép{0}' ' átcsoportosítása belső hiba miatt nem sikerült. Próbálkozzon később.  |
|  VMRedeploymentTimedOut  |  A virtuális gép{0}' ' újratelepítése nem fejeződött be a megadott időben. Lehet, hogy egyszer sikeresen befejezi. Máskülönben újra próbálkozhat a kéréssel.  |
|  VMStartTimedOut  |  A VM '{0}' nem a megadott időben kezdődött. A virtuális gép továbbra is sikeresen elindulhat. Ellenőrizze az áramellátás állapotát később.  |


## <a name="next-steps"></a>További lépések
Ha további segítségre van szüksége, kapcsolatba léphet az Azure szakértőivel [az MSDN Azure és a Stack Overflow fórumokon.](https://azure.microsoft.com/support/forums/) Másik lehetőségként benyújthat egy Azure-támogatási incidenst. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza **a Támogatás beszerezni lehetőséget.**
