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

|  Hibakód:  |  Hibaüzenet  |  
|  :------| :-------------|  
|  AcquireDiskLeaseFailed  |  Nem sikerült bérleti jogot szerezni a(z) "{0}' URI {1} rendelkező blob használatával lemez létrehozásakor. A BLOB már használatban van.  |  
|  AllocationFailed  |  A lefoglalás sikertelen. Próbálja meg csökkenteni a virtuális gép méretét vagy a virtuális gépek számát, próbálkozzon újra később, vagy próbálkozzon egy másik rendelkezésre állási csoportban vagy más Azure-beli hely telepítése.  |  
|  AllocationFailed  |  A virtuális gép foglalása belső hiba miatt sikertelen. Próbálkozzon újra később, vagy próbálja üzembe helyezni a másik helyet.  |
|  ArtifactNotFound  |  A Virtuálisgép-bővítmény a közzétevő "{0}" és a típus "{1}" nem "{2}" helyen található.  |
|  ArtifactNotFound  |  A közzétevő "{0}", kiterjesztés írja be a "{1}", és típuskezelő verziója "{2}" nem található a bővítménytárban.  |
|  ArtifactVersionNotFound  |  Nem található olyan verzió az összetevőtárban, amely megfelelne a kért verziónak ({0}).  |
|  ArtifactVersionNotFound  |  Nincs verzió az összetevőtárban, amely eleget tesz a kért verzió: "{0}' Virtuálisgép-bővítmény a közzétevő"{1}", írja be a"{2}".  |
|  AttachDiskWhileBeingDetached  |  Adatlemez a(z) "{0}" "{1}" virtuális gép nem csatolható, mert a lemez van jelenleg leválasztás alatt áll. Várjon, amíg a lemez leválasztása, és próbálkozzon újra.  |
|  BadRequest  |  Illesztett "rendelkezésre állási készletek még nem támogatottak ebben a régióban.  |
|  BadRequest  |  A virtuális gépek a rendelkezésre állási csoport nem felügyelt kezelt lemezek hozzáadása vagy a virtuális gép és a felügyelt rendelkezésre állási csoport blobalapú lemezek hozzáadása nem támogatott. Hozzon létre egy rendelkezésre állási csoport "kezelt" tulajdonsága felügyelt lemezzel rendelkező virtuális gép hozzá.  |
|  BadRequest  |  Felügyelt lemezek nem támogatottak ebben a régióban.  |
|  BadRequest  |  Kezelőnként több operációs rendszer nem támogatott írja be a "{0}". VMExtension "{1}" leírójú "{2}" már hozzáadott, vagy a megadott bemeneti adatok.  |
|  BadRequest  |  "{0}" művelet nem támogatott az erőforrás "{1}" felügyelt lemezzel.  |
|  CertificateImproperlyFormatted  |  A kulcshoz lekért JSON-megjelenítés (forrás: {0}) olyan adatmezővel rendelkezik, amely nem megfelelően formázott PFX-fájl, vagy a megadott jelszó nem dekódolja megfelelően a PFX-fájlt.  |
|  CertificateImproperlyFormatted  |  A(z) {0} forrásból beolvasott adat nem deszerializálható JSON formátumba.  |
|  Ütközés  |  A lemezek átméretezése csak virtuális gép létrehozásakor vagy felszabadított virtuális gép esetén engedélyezett.  |
|  ConflictingUserInput  |  Lemez "{0}" nem lehet csatolni, mert a lemez már van tulajdonosa a virtuális gép "{1}".  |
|  ConflictingUserInput  |  A forrásként és a célként megadott erőforráscsoport megegyezik.  |
|  ConflictingUserInput  |  A(z) {0} forrás és cél tárfiókjai különbözők.  |
|  ContainerAlreadyOnLease  |  Már vonatkozik bérleti jog a(z) {0} URI-vel rendelkező blobot tartalmazó tárolóra.  |
|  CrossSubscriptionMoveWithKeyVaultResources  |  Az áthelyezési kérelem tartalmazza a Key Vault-erőforrásokat, amelyek egy vagy több {0} s a kérésben hivatkozott. Ez nem támogatott a jelenleg Áthelyezés az előfizetések közötti. Ellenőrizze a hiba részleteit a KeyVault erőforrás-azonosítók.  |
|  DiagnosticsOperationInternalError  |  Belső hiba történt a(z) {0} virtuális gép diagnosztikai profiljának feldolgozása során.  |
|  DiskBlobAlreadyInUseByAnotherDisk  |  A BLOB {0} már használja egy másik, "{1}" virtuális Géphez tartozó lemez által. Láthatja, hogy a lemez útmutatót a blob metaadatait.  |
|  DiskBlobNotFound  |  Nem található a VHD-blob URI {0} lemez "{1}".  |
|  DiskBlobNotFound  |  Nem található a VHD-blob URI-azonosítóhoz: {0}.  |
|  DiskEncryptionKeySecretMissingTags  |  {0} titkos kulcs nem rendelkezik a {1} címkék. Frissítse a titkos, adja hozzá a szükséges címkéket, és próbálja meg újra.  |
|  DiskEncryptionKeySecretUnwrapFailed  |  Való kibontása nem sikerült kulcs {1} használatával titkos {0} értéket.  |
|  DiskImageNotReady  |  Lemez kép {0} {1} állapotban van. Próbálkozzon újra, amikor készen áll a lemezképet.  |
|  DiskPreparationError  |  Egy vagy több hiba történt a Virtuálisgép-lemezek előkészítése során. Tekintse meg a lemez példányait tartalmazó nézetet.  |
|  DiskProcessingError  |  Lemez feldolgozás leállt a virtuális gép levő többi lemezzel rendelkezik, a hibás lemezeket.  |
|  ImageBlobNotFound  |  Nem található a VHD-blob URI {0} lemez "{1}".  |
|  ImageBlobNotFound  |  Nem található a VHD-blob URI-azonosítóhoz: {0}.  |
|  IncorrectDiskBlobType  |  Az oldalakra vonatkozó blob típusú lemezblobok csak lehet. Lemez "{1}" {0} BLOB blokkblob típusú paraméter.  |
|  IncorrectDiskBlobType  |  Az oldalakra vonatkozó blob típusú lemezblobok csak lehet. A BLOB {0} paraméter típusa "{1}".  |
|  IncorrectImageBlobType  |  Az oldalakra vonatkozó blob típusú lemezblobok csak lehet. Lemez "{1}" {0} BLOB blokkblob típusú paraméter.  |
|  IncorrectImageBlobType  |  Az oldalakra vonatkozó blob típusú lemezblobok csak lehet. A BLOB {0} paraméter típusa "{1}".  |
|  InternalOperationError  |  Nem sikerült feloldani a tárfiók {0}. Ellenőrizze, hogy a számítási erőforrással azonos helyen a tárolási erőforrás-szolgáltató használatával hozták létre.  |
|  InternalOperationError  |  {0} célkeresési feladat sikertelen volt.  |
|  InternalOperationError  |  Hiba történt a(z) {0} virtuális gép hálózati profiljának érvényesítése során.  |
|  InvalidAccountType  |  A AccountType {0} érvénytelen.  |
|  InvalidParameter  |  A(z) {0} paraméter értéke érvénytelen.  |
|  InvalidParameter  |  A megadott Rendszergazdai jelszó nem engedélyezett.  |
|  InvalidParameter  |  "A megadott jelszónak kell lennie a(z) {0} közötti-\ {1\} karakterből állnak, és meg kell felelnie a legalább {2} összetettségi követelménynek a következő: <ol><li> Nagybetűs karaktert tartalmaz</li><li>A kisbetűs karaktert tartalmaz</li><li>Egy numerikus számjegyet tartalmaz</li><li>Speciális karaktert tartalmaz.</li></ol>  |
|  InvalidParameter  |  A megadott Rendszergazdai felhasználónév nem engedélyezett.  |
|  InvalidParameter  |  Ha a virtuális gépet platform vagy felhasználói rendszerkép alapján hozza létre, nem csatolhat hozzá meglévő operációsrendszer-lemezt.  |
|  InvalidParameter  |  Érvénytelen a tároló neve {0}. A tároló neve 3 – 63 karakter hosszúságúnak kell lennie, és csak kisbetűs alfanumerikus karaktereket és kötőjelet tartalmazhat. Kötőjel előtt és után alfanumerikus karakternek.  |
|  InvalidParameter  |  Tároló neve {0} {1} URL-cím érvénytelen. A tároló neve 3 – 63 karakter hosszúságúnak kell lennie, és csak kisbetűs alfanumerikus karaktereket és kötőjelet tartalmazhat. Kötőjel előtt és után alfanumerikus karakternek.  |
|  InvalidParameter  |  A blob nevének az URL-cím {0} perjelet tartalmaz. Ez jelenleg nem támogatott a lemezeket.  |
|  InvalidParameter  |  A(z) {0} URI nem tűnik megfelelő blob URI-nek.  |
|  InvalidParameter  |  A lemez "{0}" nevű már használja ugyanezt a LUN: {1}.  |
|  InvalidParameter  |  A lemez "{0}" nevű már létezik.  |
|  InvalidParameter  |  A megadott lemezkép-hivatkozásban már definiált lemezek nem bírálhatók felül felhasználói lemezképekkel.  |
|  InvalidParameter  |  A "{0}" nevű már lemez ugyanahhoz a virtuális merevlemez URL-cím {1} használ.  |
|  InvalidParameter  |  A megadott tartalék tartományt száma {0} való {2} a tartomány {1} kell lennie.  |
|  InvalidParameter  |  A licenc típusa {0} érvénytelen. Érvényes licenctípusok a következők: Windows_Client vagy Windows_Server, és a nagybetűk között.  |
|  InvalidParameter  |  Linux-gazdagépnév nem lehet {0} karakternél hosszabb és nem tartalmazhatja a következő karaktereket: {1}.  |
|  InvalidParameter  |  A Linux-telepítőügynök ismert hibája miatt az SSH nyilvános kulcsok célja jelenleg csak az alapértelmezett elérési út ({0}) lehet.  |
|  InvalidParameter  |  Már létezik lemez a(z) {0} LUN-értékkel.  |
|  InvalidParameter  |  A kérelem előfizetés {0} meg kell egyeznie az előfizetés {1} a felügyelt lemezazonosítóban szereplő.  |
|  InvalidParameter  |  Az operációsrendszer-profilban (OSProfile) szereplő egyéni adatoknak a Base64 kódolást kell követniük, és legfeljebb {0} karakteresek lehetnek.  |
|  InvalidParameter  |  Az URL-cím {0} BLOB neve "{1}" kiterjesztést kell végződnie.  |
|  InvalidParameter  |  {0} "értéke nem egy érvényes rögzített VHD blob előtagja. Egy érvényes előtagnak meg regex "{1}".  |
|  InvalidParameter  |  Nem vehetők fel tanúsítványok a virtuális gépre, ha a virtuálisgép-ügynök nincs telepítve.  |
|  InvalidParameter  |  Már létezik lemez a(z) {0} LUN-értékkel.  |
|  InvalidParameter  |  Nem sikerült létrehozni a virtuális Gépet, mert a kért méret {0} nem érhető el a fürt, ahol a rendelkezésre állási csoport jelenleg lefoglalt. Az elérhető értékek: {1}. Olvassa el a következő https://aka.ms/azure-resizevm stratégia átméretezése további a virtuális gép.  |
|  InvalidParameter  |  A kért VM mérete {0} nem érhető el az aktuális régióban. A jelenlegi régióban elérhető méret: {1}. További információkért a rendelkezésre álló Virtuálisgép-méretek a következő https://aka.ms/azure-regions minden régióban.  |
|  InvalidParameter  |  A kért VM mérete {0} nem érhető el az aktuális régióban. További információkért a rendelkezésre álló Virtuálisgép-méretek a következő https://aka.ms/azure-regions minden régióban.  |
|  InvalidParameter  |  Windows Rendszergazda felhasználóneve nem lehet több mint {0} karakter hosszú, végződhet ponttal (.), vagy tartalmazhatja a következő karaktereket: {1}.  |
|  InvalidParameter  |  Windows-számítógép neve nem lehet több mint {0} karakter hosszú, nem állhat csak számokból, vagy tartalmazhatja a következő karaktereket: {1}.  |
|  MissingMoveDependentResources  |  Az áthelyezési kérelem nem tartalmaz a tőle függő összes erőforrásról. Ellenőrizze a hiba részleteit a hiányzó erőforrás-azonosítók.  |
|  MoveResourcesHaveInvalidState  |  Az áthelyezési kérelem tartalmazza a virtuális gépek, amely érvénytelen storage-fiókok kapcsolódik. A részletekben ezen erőforrás-azonosítók és a hivatkozott tárfiókok neve.  |
|  MoveResourcesHavePendingOperations  |  Az áthelyezési kérelem tartalmazza az erőforrásokat, amelyhez egy művelet folyamatban. Részletekben ezen erőforrás-azonosítók. Próbálja megismételni a műveletet, a függőben lévő műveletek befejeződése után.  |
|  MoveResourcesNotFound  |  Az áthelyezési kérelem olyan erőforrásokat tartalmaz, nem található. Részletekben ezen erőforrás-azonosítók.  |
|  NetworkingInternalOperationError  |  Ismeretlen hálózatfoglalási hiba.  |
|  NetworkingInternalOperationError  |  Ismeretlen hálózatfoglalási hiba  |
|  NetworkingInternalOperationError  |  Belső hiba történt a virtuális gép hálózati profiljának feldolgozása során.  |
|  notFound  |  A(z) {0} rendelkezésre állási csoport nem található.  |
|  notFound  |  Az Azure ezen a helyen nem létezik a forrás virtuális gép a kérelemben megadott "{0}".  |
|  notFound  |  Nem található {0} azonosítóval rendelkező bérlő.  |
|  notFound  |  A(z) {0} lemezkép nem található.  |
|  NotSupported  |  A licenc típusa {0}, de a lemezkép blob {1} nem helyszíni.  |
|  OperationNotAllowed  |  Rendelkezésre állási készlet {0} nem lehet törölni. Rendelkezésre állási csoport törlése előtt győződjön meg arról, hogy a virtuális gép nem tartalmaz.  |
|  OperationNotAllowed  |  A rendelkezésre állási csoport "Klasszikus" a "Igazított" Termékváltozat nem módosítható.  |
|  OperationNotAllowed  |  A virtuális gép bővítményei nem módosíthatók, ha a virtuális gép nem fut.  |
|  OperationNotAllowed  |  A rögzítési művelet csak egy virtuális gépen a blobalapú lemezek támogatott. Egy felügyelt virtuális gépről a képfájl létrehozásához használja a "Kép" erőforrás API-k.  |
|  OperationNotAllowed  |  A erőforrás {0} nem hozható létre kép {1} alapján, amíg a kép létrehozása sikeresen befejeződött.  |
|  OperationNotAllowed  |  Az encryptionSettings elem frissítése nem engedélyezett, ha a virtuális gép le van foglalva. Próbálkozzon újra a virtuális gép felszabadítása után  |
|  OperationNotAllowed  |  A felügyelt lemezek egy blobalapú lemezekkel rendelkező virtuális géphez való hozzáadása nem támogatott.  |
|  OperationNotAllowed  |  Az adatlemezek ekkora egy virtuális géphez csatolása engedélyezett maximális számának a(z) {0}.  |
|  OperationNotAllowed  |  A blobalapú lemezek egy felügyelt lemezekkel rendelkező virtuális géphez való hozzáadása nem támogatott.  |
|  OperationNotAllowed  |  "{0}" művelet nem engedélyezett a lemezkép "{1}", mert a kép be van jelölve törlésre. Csak próbálkozhat a törlési művelet (vagy várjon, amíg a folyamatban lévő egyikét végrehajtásához).  |
|  OperationNotAllowed  |  "{0}" művelet nem engedélyezett a virtuális gép "{1}", mert a virtuális gép általánosítva van.  |
|  OperationNotAllowed  |  "{0}" művelet nem lehetséges, mert a törlésre megjelölt visszaállítási pont gyűjtemény "{1}".  |
|  OperationNotAllowed  |  "{0}" művelet nem engedélyezett a Virtuálisgép-bővítmény "{1}", mert meg van jelölve törlésre. Csak próbálkozhat a törlési művelet (vagy várjon, amíg a folyamatban lévő egyikét végrehajtásához).  |
|  OperationNotAllowed  |  "{0}" művelet nem engedélyezett, mert a virtuális gépek "{1}" alatt törlődnek a lemezkép "{2}".  |
|  OperationNotAllowed  |  "{0}" művelet nem engedélyezett, mert a virtuális gép ScaleSet "{1}" jelenleg használ a kép "{2}".  |
|  OperationNotAllowed  |  "{0}" művelet nem engedélyezett a virtuális gép "{1}", mert a virtuális gép ki van jelölve törlésre. Csak próbálkozhat a törlési művelet (vagy várjon, amíg a folyamatban lévő egyikét végrehajtásához).  |
|  OperationNotAllowed  |  "{0}" művelet nem engedélyezett a virtuális gép "{1}", mert a virtuális gép van szabadítva, vagy meg van jelölve felszabadításra.  |
|  OperationNotAllowed  |  "{0}" művelet nem engedélyezett a virtuális gép "{1}", mert a virtuális gép fut-e. Kérjük, energiagazdálkodási ki explicit módon abban az esetben, ha leállítja a virtuális gép vendég operációs rendszerében.  |
|  OperationNotAllowed  |  "{0}" művelet nem engedélyezett a virtuális gép "{1}", mert a virtuális gép nem felszabadítása.  |
|  OperationNotAllowed  |  "{0}" művelet nem engedélyezett a virtuális gép "{1}", mert a virtuális gép kiterjesztése "{2}" hibaállapotban van.  |
|  OperationNotAllowed  |  "{0}" művelet nem engedélyezett a virtuális gép "{1}", mert egy másik művelet van folyamatban.  |
|  OperationNotAllowed  |  A(z) "{0}" művelethez a virtuális gép "{1}" általánosítva.  |
|  OperationNotAllowed  |  A művelethez a virtuális gépnek futnia kell (vagy be kell lennie állítva futásra).  |
|  OperationNotAllowed  |  Lemez méretével, {0} GB, amely kisebb, mint {1}GB megfelelő lemez a lemezkép nem engedélyezett.  |
|  OperationNotAllowed  |  A(z) {0} kezelőhöz tartozó virtuális gépek méretezési csoportjának bővítményei a virtuális gépek méretezési csoportjának létrehozásakor adhatók hozzá.  |
|  OperationNotAllowed  |  A(z) {0} kezelőhöz tartozó virtuális gépek méretezési csoportjának bővítményei a virtuális gépek méretezési csoportjának törlésekor törölhetők.  |
|  OperationNotAllowed  |  Virtuális gép "{0}" felügyelt lemezek már használja.  |
|  OperationNotAllowed  |  Virtuális gép "{0}" "Klasszikus" rendelkezésre állási készlet "{1}" tartozik. Frissítse a rendelkezésre állási csoportot használja a "Igazított" SKU, majd próbálkozzon újra az átalakításhoz.  |
|  OperationNotAllowed  |  VM-lemezkép alapján létre blobalapú lemezek nem rendelkezhet. Az összes lemez kell felügyelt lemezek.  |
|  OperationNotAllowed  |  A rögzítési művelet nem hajtható végre, mert a virtuális gép nincs általánosítva.  |
|  OperationNotAllowed  |  Felügyeleti műveletek a virtuális gép "{0}", mert a virtuális gépek lemezei felügyelt lemezek konvertálása nem engedélyezettek.  |
|  OperationNotAllowed  |  A folyamatban lévő művelet van módosítása a virtuális gép: {0} energiaállapot {1}. Hajtson végre műveletet {2} egy kis idő múlva.  |
|  OperationNotAllowed  |  Nem lehet hozzáadni, vagy frissítse a virtuális Gépet. A kért VM mérete {0} nem a meglévő foglalási egység érhetők el. Olvassa el a következő https://aka.ms/azure-resizevm stratégia átméretezése további a virtuális gép.  |
|  OperationNotAllowed  |  Nem sikerült átméretezni a virtuális Gépet, mert a kért méret {0} nem érhető el a fürt, ahol a rendelkezésre állási csoport jelenleg lefoglalt. Az elérhető értékek: {1}. Olvassa el a következő https://aka.ms/azure-resizevm stratégia átméretezése további a virtuális gép.  |
|  OperationNotAllowed  |  Nem sikerült átméretezni a virtuális gép, mert a kért méret {0} nem érhető el a fürt, ahol a virtuális gép jelenleg lefoglalt. Átméretezni a virtuális Gépet {1} adjon felszabadítani (Ez a Stop műveletet az Azure portálon), és próbálkozzon újra az átméretezés. Olvassa el a következő https://aka.ms/azure-resizevm stratégia átméretezése további a virtuális gép.  |
|  OSProvisioningClientError  |  Sikertelen volt az operációs rendszer telepítése a(z) {0} virtuális gépen, mert jelenleg folyamatban van a vendég operációs rendszer telepítése.  |
|  OSProvisioningClientError  |  Nem sikerült az operációs rendszer üzembe helyezni a virtuális gép "{0}". Hiba legutolsó részletes adatai: {1} győződjön meg arról, hogy a lemezkép megfelelően elő van készítve (általánosítva van). <ul><li>Windows vonatkozó utasításokat: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/  </li></ul> |
|  OSProvisioningClientError  |  SSH állomás kulcs létrehozása sikertelen volt. Hiba részletei: {0}. Oldja meg a probléma ellenőrizze Ha Linux-ügynök megfelelően van-e beállítva. <ul><li>Ellenőrizheti a következő utasításokat: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-agent-user-guide/ </li></ul> |
|  OSProvisioningClientError  |  A virtuális géphez megadott felhasználónévnek a Linux-disztribúció érvénytelen. Hiba részletei: {0}.  |
|  OSProvisioningInternalError  |  Belső hiba miatt sikertelen volt az operációs rendszer telepítése a(z) {0} virtuális gépen.  |
|  OSProvisioningTimedOut  |  A virtuális gép "{0}" operációs rendszer telepítése nem fejeződött be a megadott időn belül. A virtuális gép továbbra is lehetséges, hogy befejezése sikeres. Ellenőrizze később a telepítés állapotát.  |
|  OSProvisioningTimedOut  |  A virtuális gép "{0}" operációs rendszer telepítése nem fejeződött be a megadott időn belül. A virtuális gép továbbra is lehetséges, hogy befejezése sikeres. Ellenőrizze később a telepítés állapotát. Bizonyosodjon meg arról, hogy a lemezkép megfelelően elő van készítve (általánosítva van).   <ul><li>Windows vonatkozó utasításokat: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Útmutatás Linux rendszerhez: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OSProvisioningTimedOut  |  A virtuális gép "{0}" operációs rendszer telepítése nem fejeződött be a megadott időn belül. Azonban a virtuális gép vendégügynökének futtató volt észlelhető. Ez azt sugallja, a vendég operációs rendszer nem lett megfelelően előkészített Virtuálisgép-lemezképet használ (az CreateOption = FromImage). A probléma megoldásához, vagy használja a virtuális Merevlemezt, mert CreateOption a csatolás = vagy képként használatra megfelelően előkészítése:   <ul><li>Windows vonatkozó utasításokat: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Útmutatás Linux rendszerhez: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OverConstrainedAllocationRequest  |  A virtuális gép előírt mérete jelenleg nem érhető el a kijelölt helyszínen.  |
|  ResourceUpdateBlockedOnPlatformUpdate  |  Erőforrás nem frissíthető, mert a platform folyamatban lévő frissítés. Később próbálja meg újra.  |
|  StorageAccountLimitation  |  A(z) {0} tárfiók nem támogatja a lapblobokat, amelyek azonban szükségesek a lemezek létrehozásához.  |
|  StorageAccountLimitation  |  A(z) {0} tárfiók túllépte a számára lefoglalt kvótát.  |
|  StorageAccountLocationMismatch  |  Nem sikerült feloldani a tárfiók {0}. Ellenőrizze, hogy a számítási erőforrással azonos helyen a tárolási erőforrás-szolgáltató használatával hozták létre.  |
|  StorageAccountNotFound  |  Tárolási fiók {0} nem található. Győződjön meg arról, a tárfiók nem törlődik, és a virtuális Gépet a azonos Azure-beli helyhez tartozik.  |
|  StorageAccountNotRecognized  |  A szolgáltató által kezelt tárfiókot használja. : {0} használata nem támogatott.  |
|  StorageAccountOperationInternalError  |  A(z) {0} tárfiók elérése során hiba történt.  |
|  StorageAccountSubscriptionMismatch  |  Tárolási fiók {0} előfizetés {1} nem tartozik.  |
|  StorageAccountTooBusy  |  "{0}" tárfiók jelenleg túlságosan elfoglalt. Érdemes lehet egy másik fiókot.  |
|  StorageAccountTypeNotSupported  |  Lemez {0} {1}, amely egy Blob storage-fiókot használ. Próbálja meg újra egy általános célú tárfiókkal.  |
|  StorageAccountTypeNotSupported  |  Tárolási fiók {0} {1} típusa nem. Rendszerindítási diagnosztika {2} tárfióktípusokat támogatja.  |
|  SubscriptionNotAuthorizedForImage  |  Az előfizetés nem rendelkezik megfelelő jogosultsággal.  |
|  TargetDiskBlobAlreadyExists  |  A BLOB {0} már létezik. Adjon meg egy másik blob URI-t hozzon létre egy új üres adatok lemez "{1}".  |
|  TargetDiskBlobAlreadyExists  |  Rögzítési művelet nem folytatható, mert a cél lemezképet blob {0} már létezik, és a VHD-blobok felülírására vonatkozó jelölő nincs beállítva. A blob törölje vagy állítsa be a jelzőt, amely a VHD-blobok felülírására, és próbálkozzon újra.  |
|  TargetDiskBlobAlreadyExists  |  A rögzítési művelet nem folytatódhat, mert a cél lemezképblobra ({0}) egy aktív bérleti jog vonatkozik.   |
|  TargetDiskBlobAlreadyExists  |  A BLOB {0} már létezik. Adjon meg egy másik blob URI célként lemez "{1}".  |
|  TooManyVMRedeploymentRequests  |  Túl sok újbóli üzembe helyezése kérést kapott a virtuális gép "{0}" vagy a virtuális gépek azonos availabilityset rendelkező virtuális Gépet. Próbálkozzon újra később.  |
|  VHDSizeInvalid  |  A megadott lemez méret: {0} lemez a blob {2} "{1}" értéke érvénytelen. Lemez méretének {3}, {4} és között kell lennie.  |
|  VMAgentStatusCommunicationError  |  Virtuális gép ({0}) nem küldött állapotjelentést a Virtuálisgép-ügynök vagy a bővítmények. Ellenőrizze, hogy a virtuális gép futó Virtuálisgép-ügynök, és az Azure storage kimenő kapcsolatok hozhatnak létre.  |
|  VMArtifactRepositoryInternalError  |  Hiba történt az összetevőtárral folytatott kommunikáció során, amely a virtuális gép összetevőadatainak lekérésére irányult.  |
|  VMArtifactRepositoryInternalError  |  Belső hiba történt a virtuális gép összetevőadatainak az összetevőtárból való lekérésére során.  |
|  VMExtensionHandlerNonTransientError  |  Eseménykezelő "{0}" kapcsolatos hibát jelzett Virtuálisgép-bővítmény "{1}" Terminálszolgáltatások hiba kód "{2}" és a hibaüzenet: "{3}"  |
|  VMExtensionManagementInternalError  |  Belső hiba történt a(z) {0} virtuálisgép-bővítmény feldolgozása során.  |
|  VMExtensionManagementInternalError  |  A Virtuálisgép-bővítmények előkészítése során több hiba történt. Tekintse meg a virtuális gép bővítmény példányait tartalmazó nézetet.  |
|  VMExtensionProvisioningError  |  Virtuális gép hibát jelzett a(z) "{0}' bővítmény feldolgozása során. Hibaüzenet: "{1}".  |
|  VMExtensionProvisioningError  |  Több Virtuálisgép-bővítmény telepítése sikertelen volt a virtuális Gépen. Tekintse meg a virtuális gép bővítmény példányait tartalmazó nézetet.  |
|  VMExtensionProvisioningTimeout  |  A(z) "{0}' Virtuálisgép-bővítmény telepítése túllépte az időkorlátot. Bővítmény telepítése előfordulhat, hogy túl sokáig tart, vagy a bővítmény állapota nem határozható meg.  |
|  VMMarketplaceInvalidInput  |  A virtuális gép létrehozása egy nem Piactéri rendszerképből nem kell tervezze meg információkat, távolítsa el a terv adatai a kérelemben. Az operációs rendszer lemezének neve a következő: {0}.  |
|  VMMarketplaceInvalidInput  |  A vásárlási információk nem egyezik. Nem lehet üzembe helyezést végezni a Piactéri lemezképhez. Az operációs rendszer lemezének neve a következő: {0}.  |
|  VMMarketplaceInvalidInput  |  Terv adatainak a kérelem egy virtuális gép létrehozásához a Piactéri lemezképből van szükség. Az operációs rendszer lemezének neve a következő: {0}.  |
|  VMNotFound  |  Nem található a virtuális gép "{0}".  |
|  VMRedeploymentFailed  |  Virtuális gép a(z) "{0}' újbóli üzembe helyezése belső hiba miatt sikertelen. Próbálkozzon újra később.  |
|  VMRedeploymentTimedOut  |  Virtuális gép a(z) "{0}' újbóli üzembe helyezése nem fejeződött be a megadott időn belül. Akkor lehet, hogy a Befejezés sikeresen egy kis ideig. Más esetben újra a kérelmet.  |
|  VMStartTimedOut  |  Virtuális gép "{0}" nem indult el a megadott időn belül. A virtuális gép továbbra is kezdheti el sikeresen. Az áramellátási állapot később próbálja meg.  |


## <a name="next-steps"></a>Következő lépések
Ha további segítségre van szüksége, forduljon az Azure-szakértők a [az MSDN Azure és a Stack Overflow fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként is fájl az Azure támogatási incidens. Lépjen a [az Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatja az beszerzése**.