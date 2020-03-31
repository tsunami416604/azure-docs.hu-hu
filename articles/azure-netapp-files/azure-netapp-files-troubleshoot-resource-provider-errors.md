---
title: Az Azure NetApp-fájlok erőforrás-szolgáltatóhibáinak elhárítása | Microsoft dokumentumok
description: Az Azure NetApp-fájlok erőforrás-szolgáltatóval kapcsolatos gyakori hibák okait, megoldásait és kerülő megoldásait ismerteti.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: b-juche
ms.openlocfilehash: 62e67d4965444df0e731b4387808ed3b89e4673a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72597211"
---
# <a name="troubleshoot-azure-netapp-files-resource-provider-errors"></a>Az Azure NetApp Files erőforrás-szolgáltatójának hibaelhárítása 

Ez a cikk ismerteti az Azure NetApp-fájlok erőforrás-szolgáltató gyakori hibáit, azok okait, megoldásait és kerülő megoldásait (ha elérhető).

## <a name="common-azure-netapp-files-resource-provider-errors"></a>Gyakori Azure NetApp-fájlerőforrás-szolgáltatói hibák

***BareMetalTenantId nem módosítható.***  

Ez a hiba akkor fordul elő, amikor `BaremetalTenantId` megpróbál frissíteni vagy javítani egy kötetet, és a tulajdonság értéke megváltozott.

* Ok:   
Egy kötetet próbál frissíteni, `BaremetalTenantId` és a tulajdonság értéke eltér az Azure-ban tárolt értéktől.
* Megoldás:   
Ne szerepeljen `BaremetalTenantId` a javításban és a frissítési (put) kérelemben. Másik lehetőségként `BaremetalTenantId` győződjön meg arról, hogy a kérelemben is megegyezik.

***A ServiceLevel nem módosítható.***  

Ez a hiba akkor fordul elő, ha egy másik szolgáltatási szinttel rendelkező kapacitáskészletet próbál frissíteni vagy javítani, ha a kapacitáskészletben már vannak kötetek.

* Ok:   
A kapacitáskészlet szolgáltatási szintjét akkor próbálja frissíteni, ha a készlet köteteket tartalmaz.
* Megoldás:   
Törölje az összes kötetet a kapacitáskészletből, majd módosítsa a szolgáltatási szintet.
* Megkerülő megoldás:   
Hozzon létre egy másik kapacitáskészletet, majd hozza létre újra a köteteket az új kapacitáskészletben.

***A PoolId nem módosítható***  

Ez a hiba akkor fordul elő, amikor egy módosított `PoolId` tulajdonsággal rendelkező kapacitáskészletet próbál frissíteni vagy javítani.

* Ok:   
Egy kapacitáskészlet `PoolId` tulajdonságot próbál frissíteni. A `PoolId` tulajdonság írásvédett tulajdonság, és nem módosítható.
* Megoldás:   
Ne szerepeljen `PoolId` a javításban és a frissítési (put) kérelemben.  Másik lehetőségként `PoolId` győződjön meg arról, hogy a kérelemben is megegyezik.

***A CreationToken nem módosítható.***

Ez a hiba akkor fordul elő,`CreationToken`amikor a kötet létrehozása után megpróbálja módosítani a fájl elérési útját ( ) . A kötet`CreationToken`létrehozásakor be kell állítani a fájl elérési útját ( ) , és később nem módosítható.

* Ok:   
A kötet létrehozása után próbálja`CreationToken`módosítani a fájl elérési útját ( ) , ami nem támogatott művelet. 
* Megoldás:   
Ha nincs szükség a fájl elérési útjának módosítására, fontolja meg a paraméter eltávolítását a hibaüzenet elvetéséhez.
* Megkerülő megoldás:   
Ha módosítania kell a`CreationToken`fájl elérési útját ( ), új kötetet hozhat létre egy új fájlelérési úttal, majd áttelepítheti az adatokat az új kötetre.

***A CreationToken elemnek legalább 16 karakter hosszúnak kell lennie.***

Ez a hiba akkor fordul`CreationToken`elő, ha a fájl elérési útja ( ) nem felel meg a hosszkövetelménynek. A fájl elérési útjának hosszának legalább egy karakter hosszúságúnak kell lennie.

* Ok:   
A fájl elérési útja üres.  Amikor létrehoz egy kötetet az API használatával, létre kell hoznia egy létrehozási jogkivonatot. Ha az Azure Portalon, a fájl elérési útja automatikusan jön létre.
* Megoldás:   
Írjon be legalább egy karaktert fájlelérési útként (`CreationToken`).

***A tartománynév nem módosítható.***

Ez a hiba akkor fordul elő, amikor megpróbálja módosítani a tartománynevet az Active Directoryban.

* Ok:   
A tartománynév tulajdonságot próbálja frissíteni.
* Megoldás:    
Nincs. A tartománynév nem módosítható.
* Megkerülő megoldás:   
Törölje az összes kötetet az Active Directory konfigurációjával. Ezután törölje az Active Directory konfigurációját, és hozza létre újra a köteteket.

***Az ExportPolicy.Rules[RuleIndex] objektum duplikált értékhibája.***

Ez a hiba akkor fordul elő, ha az exportálási házirend nincs egyedi indexszel definiálva. Az exportházirendek definiálásakor minden exportálási házirendszabálynak egyedi indexszel kell rendelkeznie 1 és 5 között.

* Ok:   
A definiált exportpolitika nem felel meg az exportpolitikai szabályokra vonatkozó követelménynek. Legalább egy exportálási házirendszabálynak kell lennie, a maximumon pedig öt exportházirend-szabálynak kell lennie.
* Megoldás:   
Győződjön meg arról, hogy az index még nincs használatban, és hogy az 1 és 5 közötti tartományban van.
* Megkerülő megoldás:   
Használjon másik indexet a beállítani kívánt szabályhoz.

***Hiba: {action} {resourceTypeName}***

Ez a hiba akkor jelenik meg, ha más hibakezelés nem tudta kezelni a hibát egy erőforráson végrehajtott művelet végrehajtása közben.   Ez magában foglalja a szöveg "Hiba". A `{action}` lehet bármelyik`getting`( `creating` `updating`, `deleting`, , vagy ).  A `{resourceTypeName}` a `resourceTypeName` `netAppAccount`(például, `capacityPool` `volume`, , és így tovább).

* Ok:   
Ez a hiba egy nem kezelt kivétel, ahol az ok nem ismert.
* Megoldás:   
Lépjen kapcsolatba az Azure Támogatási Központtal a naplókban való részletes ok jelentéséhez.
* Megkerülő megoldás:   
Nincs.

***A fájl elérési útja csak betűket, számokat és kötőjeleket ("-"") tartalmazhat.***

Ez a hiba akkor fordul elő, ha a fájl elérési útja nem támogatott karaktereket tartalmaz, például egy pont ("."), vessző (","), aláhúzás ("_"), vagy dollárjel ("$").

* Ok:   
A fájl elérési útja nem támogatott karaktereket tartalmaz, például egy pont ("."), vessző (","), aláhúzás ("_"), vagy dollárjel ("$").
* Megoldás:   
Távolítsa el a nem betűbetűs karaktereket, számokat vagy kötőjeleket ("-") a megadott fájlelérési útról.
* Megkerülő megoldás:   
Az aláhúzást kötőjellel helyettesítheti, vagy szóközök helyett nagybetűvel jelezheti az új szavak kezdetét.  Például használja a "NewVolume" helyett "új kötet".

***A FileSystemId nem módosítható.***

Ez a hiba a módosítás `FileSystemId`megkísérlésekor fordul elő.  A `FileSystemdId` módosítás nem támogatott művelet. 

* Ok:   
A fájlrendszer azonosítója a kötet létrehozásakor van beállítva. `FileSystemId`később nem módosítható.
* Megoldás:   
Ne szerepeljen `FileSystemId` a javítási és frissítési (put) kérelemben.  Másik lehetőségként `FileSystemId` győződjön meg arról, hogy ez megegyezik a kérelemben.

***A következő azonosítójú ActiveDirectory nem létezik.***

A `{string}` rész az Active Directory-kapcsolat `ActiveDirectoryId` tulajdonságában megadott érték.

* Ok:   
Amikor létrehozott egy fiókot az Active Directory konfigurációjával, olyan értéket adott `ActiveDirectoryId` meg, amelynek üresnek kellene lennie.
* Megoldás:   
Ne szerepeljen `ActiveDirectoryId` a create (put) kérelemben.

***Érvénytelen api-verzió.***

Az API-verzió vagy nincs elküldve, vagy érvénytelen értéket tartalmaz.

* Ok:   
A lekérdezési paraméter `api-version` értéke érvénytelen értéket tartalmaz.
* Megoldás:   
Használja a helyes API-verzióértékét.  Az erőforrás-szolgáltató számos API-verziót támogat. Az érték yyyy-mm-dd formátumú.

***Érvénytelen érték érkezett a(z) "{value}" értékhez. {1}***

Ez az `RuleIndex`üzenet a , `AllowedClients`, `UnixReadOnly`, `UnixReadWrite` `Nfsv3`, `Nfsv4`, és a mezőiben hibát jelez.

* Ok:   
A bemeneti érvényesítési kérelem a következő mezők `RuleIndex` `AllowedClients`legalább `UnixReadOnly` `UnixReadWrite`egyikénél sikertelen: , , , `Nfsv`3 és `Nfsv4`.
* Megoldás:   
Győződjön meg arról, hogy a parancssorban minden szükséges és nem ütköző paramétert beállított. Nem állíthatja be például egyszerre mind a `UnixReadOnly` paramétereket, mind `UnixReadWrite` a paramétereket.
* Megkerülő megoldás:   
Lásd a fenti megoldást.

***A {0} vlan {1} {2} IP-tartománya már használatban van***

Ez a hiba azért fordul elő, mert a használt IP-tartományok belső rekordjai ütköznek az újonnan hozzárendelt IP-címmel.

* Ok:   
A kötet létrehozásához rendelt IP-cím már regisztrálva van.
Ennek oka lehet egy korábbi sikertelen kötet létrehozása.
* Megoldás:   
Lépjen kapcsolatba az Azure támogatási központjával.

***Hiányzik a(z) "{property}" értéke.***

Ez a hiba azt jelzi, hogy egy szükséges tulajdonság hiányzik a kérelemből. A(z) {property} karakterlánc tartalmazza a hiányzó tulajdonság nevét.

* Ok:   
A bemeneti érvényesítési kérelem legalább egy tulajdonságesetében sikertelen.
* Megoldás:   
Győződjön meg arról, hogy a kérelemben az összes szükséges és nem ütköző tulajdonságot, különösen a tulajdonságot a hibaüzenetből állítja be.

***A MountTargets nem módosítható.***

Ez a hiba akkor fordul elő, ha a felhasználó megpróbálja frissíteni vagy javítani a kötet MountTargets tulajdonságot.

* Ok:   
A kötet `MountTargets` tulajdonságát próbálja frissíteni. A tulajdonság módosítása nem támogatott.
* Megoldás:   
Ne szerepeljen `MountTargets` a javítási és frissítési (put) kérelemben.  Másik lehetőségként győződjön meg arról, hogy `MountTargets` ez megegyezik a kérelemben.

***A név már használatban van.***

Ez a hiba azt jelzi, hogy az erőforrás neve már használatban van.

* Ok:   
Egy meglévő erőforráshoz használt nevű erőforrást próbál létrehozni.
* Megoldás:   
Az erőforrás létrehozásakor használjon egyedi nevet.

***A fájl elérési útja már használatban van.***

Ez a hiba azt jelzi, hogy a kötet elérési útja már használatban van.

* Ok:   
Olyan kötetet próbál létrehozni, amelynek elérési útja megegyezik egy meglévő kötettel.
* Megoldás:   
A kötet létrehozásakor használjon egyedi fájlelérési utat.

***A név túl hosszú.***

Ez a hiba azt jelzi, hogy az erőforrás neve nem felel meg a maximális hosszkövetelménynek.

* Ok:   
Az erőforrás neve túl hosszú.
* Megoldás:   
Használjon rövidebb nevet az erőforráshoz.

***A fájl elérési útja túl hosszú.***

Ez a hiba azt jelzi, hogy a kötet fájlelérési útja nem felel meg a maximális hosszkövetelménynek.

* Ok:   
A kötetfájl elérési útja túl hosszú.
* Megoldás:   
Használjon rövidebb fájlelérési utat.

***A név túl rövid.***

Ez a hiba azt jelzi, hogy az erőforrás neve nem felel meg a minimális hosszkövetelménynek.

* Ok:   
Az erőforrás neve túl rövid.
* Megoldás:   
Használjon hosszabb nevet az erőforráshoz.

***A fájl elérési útja túl rövid.***

Ez a hiba azt jelzi, hogy a kötetfájl elérési útja nem felel meg a minimális hosszkövetelménynek.

* Ok:   
A kötetfájl elérési útja túl rövid.
* Megoldás:   
Növelje a kötetfájl elérési útjának hosszát.

***Az Azure NetApp Files API nem érhető el.***

Az Azure API az Azure NetApp Files API-ra támaszkodik a kötetek kezeléséhez. Ez a hiba az API-kapcsolat hibáját jelzi.

* Ok:   
Az alapul szolgáló API nem válaszol, ami belső hibát eredményez. Ez a hiba valószínűleg átmeneti.
* Megoldás:   
A probléma valószínűleg átmeneti lesz. A kérelem nek egy idő után sikeresnek kell lennie.
* Megkerülő megoldás:   
Nincs. Az alapul szolgáló API elengedhetetlen a kötetek kezeléséhez.

***Nem található a művelet{0}eredményazonosítója a ' '- hoz.***

Ez a hiba azt jelzi, hogy egy belső hiba megakadályozza a művelet befejezését.

* Ok:   
Belső hiba történt, és megakadályozta a művelet befejezését.
* Megoldás:   
Ez a hiba valószínűleg átmeneti. Várjon néhány percet, és próbálkozzon újra. Ha a probléma továbbra is fennáll, hozzon létre egy jegyet, hogy a technikai támogatás vizsgálja meg a problémát.
* Megkerülő megoldás:   
Várjon néhány percet, és ellenőrizze, hogy a probléma továbbra is fennáll-e.

***Nem szabad cifs és NFS protokolltípusokat keverni***

Ez a hiba akkor fordul elő, amikor kötetet próbál létrehozni, és a kötet tulajdonságai között vannak a CIFS (SMB) és az NFS protokolltípusok is.

* Ok:   
A kötet tulajdonságai ban mind a CIFS (SMB) , mind az NFS protokolltípusok használatosak.
* Megoldás:   
Távolítsa el az egyik protokolltípust.
* Megkerülő megoldás:   
Hagyja üresen vagy nullként hagyja a protokolltípus-tulajdonságot.

***Elemek száma: {value} az objektumhoz: Az ExportPolicy.Rules[RuleIndex] kívül esik a min-max tartományon.***

Ez a hiba akkor fordul elő, ha az exportházirend-szabályok nem felelnek meg a minimális vagy maximális tartományra vonatkozó követelménynek. Ha definiálja az exportházirendet, akkor legalább egy exportálási házirendszabálynak kell lennie, a maximumon pedig öt exportházirend-szabálynak.

* Ok:   
A megadott exportálási házirend nem felel meg a szükséges tartománynak.
* Megoldás:   
Győződjön meg arról, hogy az index még nincs használatban, és ez az 1 és 5 közötti tartományban van.
* Megkerülő megoldás:   
Nem kötelező exportházirendet használni a köteteken. Az exportálási házirendet teljesen kihagyhatja, ha nem kell exportszabályzat-szabályokat használnia.

***Csak egy active directory engedélyezett***

Ez a hiba akkor fordul elő, amikor megpróbál létrehozni egy Active Directory-konfigurációt, és egy már létezik az előfizetéshez a régióban. A hiba akkor is előfordulhat, ha egynél több Active Directory-konfigurációt próbál létrehozni.

* Ok:   
Active directoryt próbál létrehozni (nem frissíteni), de egy már létezik.
* Megoldás:   
Ha az Active Directory konfigurációja nincs használatban, akkor először törölheti a meglévő konfigurációt, majd próbálkozzon újra a létrehozási művelettel.
* Megkerülő megoldás:   
Nincs. Csak egy Active Directory engedélyezett.

***A(z) "{operation}" művelet nem támogatott.***

Ez a hiba azt jelzi, hogy a művelet nem érhető el az aktív előfizetéshez vagy erőforráshoz.

* Ok:   
A művelet nem érhető el az előfizetéshez vagy az erőforráshoz.
* Megoldás:   
Győződjön meg arról, hogy a művelet helyesen van megadva, és hogy elérhető-e a használt erőforrás és előfizetés számára.

***OwnerId nem módosítható***

Ez a hiba akkor fordul elő, amikor megpróbálja módosítani a kötet OwnerId tulajdonságát. A Tulajdonosazonosító módosítása nem támogatott művelet. 

* Ok:   
A `OwnerId` tulajdonság a kötet létrehozásakor van beállítva. A tulajdonság később nem módosítható.
* Megoldás:   
Ne szerepeljen `OwnerId` a javítási és frissítési (put) kérelemben. Másik lehetőségként győződjön meg arról, hogy `OwnerId` ez megegyezik a kérelemben.

***A szülőkészlet nem található***

Ez a hiba akkor fordul elő, amikor kötetet próbál létrehozni, és a kapacitáskészlet, amelyben a kötetet létrehozza, nem található.

* Ok:   
A kötetet létrehozó kapacitáskészlet nem található.
* Megoldás:   
Valószínűleg a készlet nem lett teljesen létrehozva, vagy már törölték a kötet létrehozásakor.

***Ehhez az erőforrástípushoz nem támogatott a javítási művelet.***

Ez a hiba akkor fordul elő, amikor megpróbálja módosítani a csatlakoztatási célt vagy pillanatképet.

* Ok:   
A csatlakoztatási cél a létrehozáskor van definiálva, és később nem módosítható.
A pillanatképek nem tartalmaznak olyan tulajdonságokat, amelyek módosíthatók.
* Megoldás:   
Nincs. Ezek az erőforrások nem rendelkeznek módosítható tulajdonságokkal.

***A készlet mérete túl kicsi a teljes kötetmérethez képest.***

Ez a hiba akkor fordul elő, amikor frissíti a kapacitáskészlet méretét, és a méret kisebb, mint az adott kapacitáskészlet összes kötetének összértéke. `usedBytes`  Ez a hiba akkor is előfordulhat, ha új kötetet hoz létre vagy egy meglévő kötetet átméretezi, és az új kötetmérete meghaladja a kapacitáskészletben lévő szabad területet.

* Ok:   
A kapacitáskészletet a kapacitáskészlet összes kötetében használt bájtnál kisebb méretre próbálja frissíteni.  Vagy olyan kötetet próbál létrehozni, amely nagyobb, mint a kapacitáskészlet szabad területe.  Másik lehetőségként egy kötet átméretezésével próbálkozik, és az új méret meghaladja a kapacitáskészlet szabad területét.
* Megoldás:   
Állítsa a kapacitáskészlet méretét nagyobb értékre, vagy hozzon létre egy kisebb kötetet.
* Megkerülő megoldás:   
Távolítson el elegendő kötetet, hogy a kapacitáskészlet mérete erre a méretre frissíthető legyen.

***A tulajdonság: A Pillanatkép helyének meg kell egyeznie a Kötettel.***

Ez a hiba akkor fordul elő, ha a pillanatképet birtokló kötettől eltérő helyen készít pillanatképet.

* Ok:   
Érvénytelen érték a pillanatkép Hely tulajdonságában.
* Megoldás:   
Állítson be érvényes karakterláncot a Hely tulajdonságban.

***A(z) {resourceType} névnek meg kell egyeznie az erőforrás-azonosító nevével.***

Ez a hiba akkor fordul elő, amikor létrehoz egy erőforrást, és a `resourceId`névtulajdonságot a név tulajdonságáttól eltérő értékkel tölti ki.

* Ok:   
Erőforrás létrehozásakor érvénytelen érték a name tulajdonságban.
* Megoldás:   
Hagyja üresen a name tulajdonságot, vagy hagyja, hogy ugyanazt az értéket használja, mint a `resourceId`name tulajdonság (az utolsó fordított perjel "/" és a kérdőjel "?" között) a ban.

***A(z) {value} protokolltípus nem ismert***

Ez a hiba akkor fordul elő, ha ismeretlen protokolltípusú kötetet hoz létre.  Érvényes értékek: "NFSv3", "NFSv4" és "CIFS".

* Ok:   
Érvénytelen értéket próbál beállítani a kötet `protocolType` tulajdonságban.
* Megoldás:   
Állítson be `protocolType`érvényes karakterláncot a ban.
* Megkerülő megoldás:   
Null `protocolType` értékre állítva.

***A protokolltípusok nem módosíthatók***

Ez a hiba akkor fordul elő, amikor egy kötetet próbál frissíteni vagy javítani. `ProtocolType`  A ProtocolType módosítása nem támogatott művelet.

* Ok:   
A `ProtocolType` tulajdonság a kötet létrehozásakor van beállítva.  Nem frissíthető.
* Megoldás:   
Nincs.
* Megkerülő megoldás:   
Hozzon létre egy másik kötetet új protokolltípusokkal.

***A(z) {resourceType} típusú erőforrás létrehozása meghaladja a {resourceType} típusú {quot} erőforrások {parentResourceType} kvótáját. Az aktuális erőforrásszám {currentCount}, töröljön néhány ilyen típusú erőforrást, mielőtt újat hozna létre.***

Ez a hiba akkor fordul elő,`NetAppAccount`amikor `CapacityPool` `Volume`erőforrást `Snapshot`( , , , vagy akvótát próbál létrehozni, de a kvóta elérte a korlátot.

* Ok:   
Erőforrást próbál létrehozni, de a kvótakorlát elérése (például `NetAppAccounts` `CapacityPools` előfizetésenként vagy előfizetésenként) `NetAppAccount`történik.
* Megoldás:   
Növelje a kvótakorlátot.
* Megkerülő megoldás:   
Törölje az azonos típusú, fel nem használt erőforrásokat, és hozza létre őket újra.

***Kapott egy értéket a(z) "{propertyName}" írásvédett tulajdonsághoz.***

Ez a hiba akkor fordul elő, ha olyan tulajdonság értékét határozza meg, amely nem módosítható. A kötetazonosító például nem módosítható.

* Ok:   
Olyan paramétert (például a kötetazonosítót) próbál módosítani, amely nem módosítható.
* Megoldás:   
Ne módosítsa a tulajdonság értékét.

***A kért {erőforrás} nem található.***

Ez a hiba akkor fordul elő, amikor nem létező erőforrásra, például kötetre vagy pillanatképre próbál hivatkozni. Lehet, hogy törölték az erőforrást, vagy hibásan írt erőforrásneve van.

* Ok:   
Olyan nem létező erőforrásra (például kötetre vagy pillanatképre) próbál hivatkozni, amelyet már töröltek, vagy hibásan írt erőforrásnévvel rendelkezik.
* Megoldás:   
Ellenőrizze a helyesírási hibákra vonatkozó kérést, és ellenőrizze, hogy helyesen hivatkozik-e rá.
* Megkerülő megoldás:   
Lásd a fenti Megoldás című szakaszt.

***A(z) "{volumeServiceLevel}" szolgáltatásszint magasabb, mint a(z) "{poolServiceLevel}" szülő.***

Ez a hiba akkor fordul elő, amikor kötetet hoz létre vagy frissít, és a szolgáltatási szintet magasabb szintre állította be, mint az azt tartalmazó kapacitáskészletet.

* Ok:   
Magasabb rangsorolt szolgáltatási szinttel rendelkező kötetet próbál létrehozni vagy frissíteni, mint a szülőkapacitáskészlet.
* Megoldás:   
Állítsa a szolgáltatási szintet azonos vagy alacsonyabb rangra, mint a szülőkapacitáskészlet.
* Megkerülő megoldás:   
Hozza létre a kötetet egy másik kapacitáskészletben a megfelelő szolgáltatási szinttel. Másik lehetőségként törölje az összes kötetet a kapacitáskészletből, és állítsa magasabb rangra a kapacitáskészlet szolgáltatási szintjét.

***Az SMB-kiszolgáló neve nem lehet hosszabb 10 karakternél.***

Ez a hiba akkor fordul elő, amikor egy fiókhoz Active Directory-konfigurációt hoz létre vagy frissít.

* Ok:   
Az SMB-kiszolgáló nevének hossza meghaladja a 10 karaktert.
* Megoldás:   
Használjon rövidebb kiszolgálónevet. A maximális hossz 10 karakter.
* Megkerülő megoldás:   
Nincs.  Lásd a fenti megoldást. 

***Az alhálózati azonosító nem módosítható.***

Ez a hiba akkor fordul `subnetId` elő, amikor a kötet létrehozása után megpróbálja módosítani.  `SubnetId`a kötet létrehozásakor be kell állítani, és később nem módosítható.

* Ok:   
A kötet létrehozása `subnetId` után próbálja módosítani, ami nem támogatott művelet. 
* Megoldás:   
Ha a `subnetId` módosításra nincs szükség, fontolja meg a paraméter eltávolítását a kérelemből a hibaüzenet elvetéséhez.
* Megkerülő megoldás:   
Ha módosítania kell `subnetId`a at, új kötetet `subnetId`hozhat létre egy új sal, majd áttelepítheti az adatokat az új kötetre.

***Az alhálózatazonosító formátuma érvénytelen.***

Ez a hiba akkor fordul elő, amikor `subnetId` új `resourceId` kötetet próbál létrehozni, de az nem alhálózathoz való.

* Ok:   
Ez a hiba akkor fordul elő, amikor `subnetId` új kötetet próbál létrehozni, de ez nem `resourceId` alhálózathoz való. 
* Megoldás:   
Ellenőrizze az érték, `subnetId` hogy megbizonyosodjon `resourceId` arról, hogy tartalmaz egy az alhálózat használt.
* Megkerülő megoldás:   
Nincs. Lásd a fenti megoldást. 

***Az alhálózatnak "Microsoft.NetApp/volumes" delegálással kell rendelkeznie.***

Ez a hiba akkor fordul elő, amikor kötetet hoz `Microsoft.NetApp/volumes`létre, és a kijelölt alhálózat nem delegálva van a rendszernek.

* Ok:   
Megpróbált létrehozni egy kötetet, és olyan alhálózatot `Microsoft.NetApp/volumes`választott, amely nincs delegálva a rendszerre.
* Megoldás:   
Jelöljön ki egy másik `Microsoft.NetApp/volumes`alhálózatot, amely delegált.
* Megkerülő megoldás:   
Adjon hozzá megfelelő delegálást az alhálózathoz.

***A megadott erőforrástípus ismeretlen/nem alkalmazható.***

Ez a hiba akkor fordul elő, ha névellenőrzést kértek egy nem alkalmazható erőforrástípuson vagy egy ismeretlen erőforrástípuson.

* Ok:   
Ismeretlen vagy nem támogatott erőforrástípus névellenőrzését kérték.
* Megoldás:   
Ellenőrizze, hogy a kérelemben végző erőforrás támogatott-e, vagy nem tartalmaz-e helyesírási hibát.
* Megkerülő megoldás:   
Lásd a fenti megoldást.

***Ismeretlen Azure NetApp-fájlok hiba.***

Az Azure API az Azure NetApp Files API-ra támaszkodik a kötetek kezeléséhez. A hiba az API-val való kommunikáció ban észlelt problémát jelez.

* Ok:   
Az alapul szolgáló API ismeretlen hibát küld. Ez a hiba valószínűleg átmeneti.
* Megoldás:   
A probléma valószínűleg átmeneti, és a kérelem nek egy idő után sikeresnek kell lennie. Ha a probléma továbbra is fennáll, hozzon létre egy támogatási jegyet a probléma kivizsgálásához.
* Megkerülő megoldás:   
Nincs. Az alapul szolgáló API elengedhetetlen a kötetek kezeléséhez.

***Ismeretlen "{propertyName}" tulajdonsághoz kapott érték.***

Ez a hiba akkor fordul elő, ha nem létező tulajdonságok vannak megadva egy erőforráshoz, például a kötethez, a pillanatképhez vagy a csatlakoztatási célhoz.

* Ok:   
A kérelem az egyes erőforrásokhoz használható tulajdonságok készletével rendelkezik. Nem létező tulajdonságok nem szerepelhetnek a kérelemben.
* Megoldás:   
Győződjön meg arról, hogy az összes tulajdonságnév helyesen van beírva, és hogy a tulajdonságok elérhetők-e az előfizetéshez és az erőforráshoz.
* Megkerülő megoldás:   
Csökkentse a kérelmet a hibát okozó tulajdonság megszüntetésére irányuló kérelemben meghatározott tulajdonságok számát.

***Ehhez az erőforrástípushoz nem támogatott a frissítési művelet.***

Csak kötetek frissíthetők. Ez a hiba akkor fordul elő, ha nem támogatott frissítési műveletet próbál végrehajtani, például egy pillanatkép frissítése.

* Ok:   
A frissíteni kívánt erőforrás nem támogatja a frissítési műveletet. Csak a kötetek tulajdonságai módosíthatók.
* Megoldás:   
Nincs. A frissíteni kívánt erőforrás nem támogatja a frissítési műveletet. Ezért nem lehet megváltoztatni.
* Megkerülő megoldás:   
Kötet esetén hozzon létre egy új erőforrást a frissítéssel a helyén, és telepítse át az adatokat.

***A kötet nem hozható létre olyan készletben, amely nem volt sikeres állapotban.***

Ez a hiba akkor fordul elő, ha olyan kötetet próbál létrehozni egy készletben, amely nem a sikeres állapotban van. Valószínűleg a kapacitáskészlet létrehozási művelete valamilyen okból nem sikerült.

* Ok:   
Az új kötetet tartalmazó kapacitáskészlet hibás állapotban van.
* Megoldás:   
Ellenőrizze, hogy a kapacitáskészlet sikeresen létrejött-e, és hogy nem hibás állapotban van-e.
* Megkerülő megoldás:   
Hozzon létre egy új kapacitáskészletet, és hozza létre a kötetet az új készletben.

***A kötet létrehozása folyamatban van, és jelenleg nem törölhető.***

Ez a hiba akkor fordul elő, amikor megpróbál törölni egy még létrekívánt kötetet.

* Ok:   
A kötet törlésekor a kötet továbbra is létrejön.
* Megoldás:   
Várjon, amíg a kötet létrehozása befejeződik, majd próbálkozzon újra a törléssel.
* Megkerülő megoldás:   
Lásd a fenti megoldást.

***A kötet törlése folyamatban van, és jelenleg nem törölhető.***

Ez a hiba akkor fordul elő, amikor megpróbál törölni egy kötetet, amikor az már törlés alatt áll.

* Ok:   
A kötet törlésekor egy kötet már törlésre kerül.
* Megoldás:   
Várjon, amíg az aktuális törlési művelet befejeződik.
* Megkerülő megoldás:   
Lásd a fenti megoldást.

***A kötet frissítése folyamatban van, és jelenleg nem törölhető.***

Ez a hiba akkor fordul elő, amikor egy frissítve lévő kötetet próbál törölni.

* Ok:   
A kötet törlésekor egy kötet frissül.
* Megoldás:   
Várjon, amíg a frissítési művelet befejeződik, majd próbálkozzon újra a törléssel.
* Megkerülő megoldás:   
Lásd a fenti megoldást.

***A kötet nem található, vagy nem sikerült létrehozni.***

Ez a hiba akkor fordul elő, ha a kötet létrehozása sikertelen volt, és megpróbálja módosítani a kötetet, vagy hozzon létre egy pillanatképet a kötethez.

* Ok:   
A kötet nem létezik, vagy a létrehozás sikertelen.
* Megoldás:   
Ellenőrizze, hogy a megfelelő kötetet módosítja-e, és hogy a kötet létrehozása sikeres volt-e. Vagy ellenőrizze, hogy létezik-e az a kötet, amelyhez pillanatképet hoz létre.
* Megkerülő megoldás:   
Nincs.  Lásd a fenti megoldást. 

***A megadott létrehozási jogkivonat már létezik.***

Ez a hiba akkor fordul elő, amikor kötetet próbál létrehozni, és olyan létrehozási jogkivonatot (exportálási útvonalat) ad meg, amelyhez már létezik kötet.

* Ok:   
A kötet létrehozása során megadott létrehozási jogkivonat (exportálási útvonal) már társítva van egy másik kötethez. 
* Megoldás:   
Válasszon egy másik létrehozási jogkivonatot.  Másik lehetőségként törölje a másik kötetet.

***A megadott létrehozási jogkivonat le van foglalva***

Ez a hiba akkor fordul elő, amikor megpróbál létrehozni egy kötetet, és fájlelérési útként (létrehozási jogkivonat) az "alapértelmezett" vagy "nincs" értéket adja meg.

* Ok:    
Kötetet próbál létrehozni, és az "alapértelmezett" vagy a "nincs" értéket adja meg fájlelérési útként (létrehozási jogkivonat).
* Megoldás:   
Válasszon másik fájlelérési utat (létrehozási jogkivonat).
 
***Az Active Directory hitelesítő adatai használatban vannak***

Ez a hiba akkor fordul elő, amikor megpróbálja törölni az Active Directory konfigurációját egy olyan fiókból, ahol még legalább egy SMB-kötet létezik.  Az SMB-kötet et a törölni kívánt Active Directory-konfigurációval hozták létre.

* Ok:   
Az Active Directory konfigurációját egy fiókból próbálja törölni, de még mindig létezik legalább egy SMB-kötet, amelyet eredetileg az Active Directory konfigurációjával hoztak létre. 
* Megoldás:   
Először törölje az Active Directory konfigurációjával létrehozott összes SMB-kötetet.  Ezután próbálkozzon újra a konfiguráció törlésével.

***A szervezeti egység hozzárendelése nem módosítható, ha a hitelesítő adatok használatban vannak.***

Ez a hiba akkor fordul elő, ha megpróbálja módosítani az Active Directory-konfiguráció szervezeti egységét, de legalább egy SMB kötet még létezik.  Az SMB-kötetet a törölni kívánt Active Directory-konfiguráció val hozták létre.

* Ok:   
Egy Active Directory-konfiguráció szervezeti egységét próbálja módosítani.  De legalább egy SMB kötet még mindig létezik, amelyet eredetileg az Active Directory konfigurációjával hoztak létre.
* Megoldás:   
 Először törölje az Active Directory konfigurációjával létrehozott összes SMB-kötetet.  Ezután próbálkozzon újra a konfiguráció törlésével. 

***Az Active Directory frissítése már folyamatban van***

Ez a hiba akkor fordul elő, amikor olyan Active Directory-konfigurációt próbál meg szerkeszteni, amelynek szerkesztési művelete már folyamatban van.

* Ok:   
Az Active Directory konfigurációját próbálja szerkeszteni, de egy másik szerkesztési művelet már folyamatban van.
* Megoldás:   
Várjon, amíg az éppen futó szerkesztési művelet befejeződik.

***Az összes kötet törlése először a kijelölt hitelesítő adatokkal***

Ez a hiba akkor fordul elő, amikor megpróbálja törölni az Active Directory konfigurációját, de legalább egy SMB kötet továbbra is létezik.  Az SMB-kötet et a törölni kívánt Active Directory-konfigurációval hozták létre.

* Ok:   
Az Active Directory-konfigurációt próbálja törölni, de még mindig létezik legalább egy SMB-kötet, amelyet eredetileg az Active Directory-konfiguráció használatával hoztak létre.
* Megoldás:   
Először törölje az Active Directory konfigurációjával létrehozott összes SMB-kötetet.  Ezután próbálkozzon újra a konfiguráció törlésével. 

***Nem található Active Directory-hitelesítő adat a régióban***

Ez a hiba akkor fordul elő, amikor SMB-kötetet próbál létrehozni, de a régió fiókjához nem adott hozzá Active Directory-konfigurációt.

* Ok:   
SMB-kötetet próbál létrehozni, de a fiókhoz nem lett Active Directory-konfiguráció. 
* Megoldás:   
SMB-kötet létrehozása előtt adjon hozzá Active Directory-konfigurációt a fiókhoz.

***Nem lehet lekérdezni a DNS-kiszolgálót. Ellenőrizze, hogy a hálózati konfiguráció megfelelő-e, és hogy rendelkezésre állnak-e DNS-kiszolgálók.***

Ez a hiba akkor fordul elő, amikor SMB-kötetet próbál létrehozni, de egy DNS-kiszolgáló (amely az Active Directory konfigurációjában van megadva) nem érhető el. 

* Ok:   
SMB-kötetet próbál létrehozni, de egy DNS-kiszolgáló (az Active Directory konfigurációjában megadva) nem érhető el.
* Megoldás:   
Tekintse át az Active Directory konfigurációját, és győződjön meg arról, hogy a DNS-kiszolgáló IP-címei helyesek és elérhetők.
Ha nincs probléma a DNS-kiszolgáló IP-címével, ellenőrizze, hogy nincsenek-e tűzfalak a hozzáférés blokkolására.

***Túl sok egyidejű munkahely***

Ez a hiba akkor fordul elő, amikor megpróbál létrehozni egy pillanatképet, amikor három másik pillanatkép-létrehozási művelet már folyamatban van az előfizetéshez.

* Ok:   
Megpróbál létrehozni egy pillanatképet, amikor három másik pillanatkép-létrehozási művelet már folyamatban van az előfizetéshez. 
* Megoldás:   
Pillanatkép-létrehozási feladatok legutolsó pillanatnyi befejezéséhez.  Várjon néhány másodpercet, és próbálkozzon újra a pillanatkép-létrehozási művelettel.

***További feladatok nem hozhatók létre. Várjon, amíg a folyamatban lévő feladatok befejeződnek, majd próbálkozzon újra***

Ez a hiba akkor fordulhat elő, ha bizonyos körülmények között megpróbál létrehozni vagy törölni egy kötetet.

* Ok:   
Bizonyos körülmények között kötetet próbál létrehozni vagy törölni.
* Megoldás:   
Várjon egy percet, majd próbálkozzon újra a művelettel.

***A kötet már átvált az állapotok között***

Ez a hiba akkor fordulhat elő, ha olyan kötetet próbál törölni, amely jelenleg átalakuló állapotban van (azaz jelenleg létrehozási, frissítési vagy törlési állapotban van).

* Ok:   
Olyan kötetet próbál törölni, amely jelenleg átalakuló állapotban van.
* Megoldás:   
Várjon, amíg az éppen futó (állapotátmenet) művelet befejeződik, majd próbálkozzon újra a művelettel.

***Nem sikerült felosztani az új kötetet a forráskötet pillanatképéből***

 Ez a hiba akkor fordulhat elő, ha pillanatképből próbál létrehozni egy kötetet.  

* Ok:   
Megpróbál létrehozni egy kötetet egy pillanatképből, és a kötet hibaállapotban végződik.
* Megoldás:   
Törölje a kötetet, majd próbálja meg újra a kötet létrehozásának műveletét a pillanatképből.

 
## <a name="next-steps"></a>További lépések

* [Fejlesztés az Azure NetApp-fájlokhoz REST API-val](azure-netapp-files-develop-with-rest-api.md)
