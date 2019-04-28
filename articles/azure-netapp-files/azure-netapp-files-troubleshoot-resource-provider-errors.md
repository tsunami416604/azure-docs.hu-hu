---
title: Azure NetApp fájlok erőforrás-szolgáltató hibáinak elhárítása |} A Microsoft Docs
description: Okait, a megoldások és a lehetséges megoldások a gyakori Azure NetApp fájlok erőforrás-szolgáltató által jelzett hibákat ismerteti.
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
ms.date: 03/25/2019
ms.author: b-juche
ms.openlocfilehash: d4e06429aa1efec7c3301c7d0f0e7e17800fd520
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2019
ms.locfileid: "63769436"
---
# <a name="troubleshoot-azure-netapp-files-resource-provider-errors"></a>Az Azure NetApp Files erőforrás-szolgáltatójának hibaelhárítása
Ez a cikk ismerteti a gyakori Azure NetApp fájlok erőforrás-szolgáltató hibák, azok okok, megoldásokat és megkerülő megoldások. 

<a name="error_01"></a>***Az Azure Key Vault nincs konfigurálva.***   
Az Azure Key Vault tárolja a szükséges hitelesítő adatok a mögöttes API eléréséhez. Ez a hiba azt jelzi, hogy az Azure Key Vault nem kapott a mögöttes API eléréséhez a teljes hitelesítő adatok.

* Ok  
Az Azure Key Vault nem kapott megfelelő hitelesítő adatokkal, vagy a hitelesítő adatok nem teljesek.  

* Megoldás   
Az Azure NetApp fájlok szolgáltatás az Azure Key Vault használ. Az Azure Key Vault az Azure Active Directoryból származó jogkivonat használatával hitelesíti magát. Ezért az alkalmazás tulajdonosának regisztrálnia kell az alkalmazást az Azure Active Directoryban.

* Áthidaló megoldás   
Nincs.  Az Azure Key Vault megfelelően az Azure Files-NetApp használatával kell állítani.  

<a name="error_02"></a>***Token létrehozása nem változtatható meg.***   
Ez a hiba akkor fordul elő, amikor megpróbálja módosítani a létrehozási token, a kötet létrehozása után.
Token létrehozása kell állítani, ha a kötet jön létre, és később nem módosítható.

* Ok   
Próbál módosítani a létrehozási token, a kötet létrehozása után, ami nem támogatott művelet.

* Megoldás   
A kötet létrehozása után fontolja meg, a paraméter eltávolítását a kérést hagyja figyelmen kívül a hibaüzenetet.

* Áthidaló megoldás   
A létrehozási token módosítani szeretné, hozzon létre egy új kötetet egy új létrehozás-jogkivonattal, és ezután telepítse át az adatokat az új kötetre.


<a name="error_03"></a>***Token létrehozása legalább 16 karakter hosszúságúnak kell lennie.***   
Ez a hiba akkor fordul elő, amikor a létrehozás token nem felel meg a megengedettnél. A létrehozási token hossza legalább 16 karakter hosszúnak kell lennie.

* Ok   
A létrehozási token nem felel meg a megengedettnél.  Ha egy kötetet hoz létre az API-val, a egy létrehozási tokenre szükség. Ha a portált használja, a jogkivonat automatikusan létrehozható.

* Megoldás   
Növelje a létrehozási token hosszát. Ha például egy másik szóban is hozzáadhat elején vagy végén a létrehozási jogkivonat.

* Áthidaló megoldás   
Minimálisan szükséges hossza a létrehozási jogkivonat nem mellőzhetők.  Elő- vagy utótag segítségével növelheti a létrehozási token hossza.


<a name="error_04"></a>***Hiba történt egy kötet, amely nem található a következő Azure NetApp fájlok törlése.***   
Ez a hiba oka, hogy a belső beállításjegyzék-erőforrások nincs szinkronizálva.

* Ok   
A kötet még aktívak maradhatnak, a portálon megjelenített egy kis ideig, a törlés után. Ha a kötet törli az API-val, lehetőség, hogy a kötet nem lett megfelelően megadva. Elavult böngésző gyorsítótárának is okozhatja a hibát.

* Megoldás   
Egyértelmű böngésző gyorsítótárát, ha a portált használja. Emellett van egy belső gyorsítótár, amely a 10 percenként frissül.  Próbálkozzon újra a gyorsítótár ürítése.  Ha a probléma továbbra is fennáll, 10 perc múlva, létrehozhat egy támogatási jegyet.

* Áthidaló megoldás   
Addig is használjon egy másik kötetre, és figyelmen kívül hagyja a meglévőt.


<a name="error_05"></a>***Hiba történt a szúr be egy új kötetet, az Azure Files-NetApp címen található.***   
Ez a hiba oka, hogy a belső beállításjegyzék-erőforrások nincs szinkronizálva.

* Ok   
A kötet némi várakozás után a rendszer törölte előfordulhat, hogy továbbra is megjelenjenek a portálon. Ha a kötet törli az API-val, lehetőség, hogy a kötet nem lett megfelelően megadva.

* Megoldás   
Ha a portált használja, a kötet már létrejött.  A kötet automatikusan meg kell jelennie. Ha a probléma nem szűnik meg, létrehozhat egy támogatási jegyet.

* Áthidaló megoldás   
Létrehozhat egy kötet egy másik nevet, és a egy másik létrehozási token.


<a name="error_06"></a>***A fájl elérési útja neve betűket, számokat és kötőjeleket tartalmazhat ("" – "") csak.***   
Ez a hiba akkor fordul elő, ha a fájl elérési útja nem támogatott karaktereket tartalmaz, például egy ideig ("."), vesszővel (","), aláhúzásjel ("\_"), vagy dollárjel ("$").

* Ok   
A fájl elérési útja nem támogatott karaktereket tartalmaz, például egy ideig ("."), vesszővel (","), aláhúzásjel ("\_"), vagy dollárjel ("$").

* Megoldás   
Távolítsa el a karaktereket, amelyek nem betűrend szerinti rendezés betűket, számokat és kötőjeleket tartalmazhat ("-") a megadott elérési útról.

* Áthidaló megoldás   
Cserélje le az aláhúzás kötőjel, vagy használja a kis-és nagybetűk tárolóhelyek helyett új szavakat (például "NewVolume" helyett "új kötet") kezdetét jelzi.


<a name="error_07"></a>***Kötet azonosítója nem módosítható.***   
Ez a hiba akkor fordul elő, amikor megpróbálja módosítani a kötet azonosítója.  A Kötetazonosító módosítása nem támogatott művelet.

* Ok   
A fájlrendszer azonosítója van beállítva, a kötet létrehozásakor. A Kötetazonosító ezt követően nem módosítható.

* Megoldás   
Nincs.

* Áthidaló megoldás   
Nincs.  A Kötetazonosító jön létre, ha a kötet jön létre, és ezt követően nem módosítható.


<a name="error_08"></a>***Érvénytelen érték "{0}" érkezett {1}.***   
Ez az üzenet azt jelzi, hogy a mezők RuleIndex, AllowedClients, UnixReadOnly, UnixReadWrite, Nfsv3 és Nfsv4.

* Ok   
A bemenet-ellenőrzés kérelme sikertelen a következő mezők egyikét: RuleIndex, AllowedClients, UnixReadOnly, UnixReadWrite, Nfsv3, and Nfsv4.

* Megoldás   
Ellenőrizze, hogy az összes kötelező és nem ütköző paraméterek beállítása a parancssorból. Például a UnixReadOnly és a UnixReadWrite paraméter nem állítható egyszerre.

* Áthidaló megoldás   
A megoldás című szakaszában talál.  


<a name="error_09"></a>***Hiányzó értéke "{0}".***   
Ez a hiba azt jelzi, hogy egy kötelező attribútum hiányzik a kérelemből a következő paraméterek közül legalább az egyik: RuleIndex, AllowedClients, UnixReadOnly, UnixReadWrite, Nfsv3, and Nfsv4.

* Ok   
A bemenet-ellenőrzés kérelme sikertelen a következő mezők egyikét: RuleIndex, AllowedClients, UnixReadOnly, UnixReadWrite, Nfsv3, and Nfsv4.

* Megoldás   
Ellenőrizze, hogy az összes kötelező és nem ütköző paraméterek beállítása a parancssorból. Ha például nem állítható be a UnixReadOnly és a UnixReadWrite paraméterek egyszerre

* Áthidaló megoldás   
A megoldás című szakaszában talál.  


<a name="error_10"></a> ***{0} már használatban van.***   
Ez a hiba azt jelzi, hogy az erőforrás neve már használatban van.

* Ok   
Hozzon létre egy kötetet, amelynek neve megegyezik egy meglévő kötetet próbál.

* Megoldás   
Adjon meg egy egyedi nevet kötet létrehozásakor.

* Áthidaló megoldás   
Ha szükséges, módosíthatja a meglévő kötet neve, hogy az új kötetet is használni kívánt nevét.


<a name="error_11"></a> ***{0} túl rövid.***   
Ez a hiba azt jelzi, hogy a kötet neve nem felel meg a minimálisan megengedettnél.

* Ok   
A kötet név túl rövid.

* Megoldás   
Növelje a kötet neve.  

* Áthidaló megoldás   
A kötet nevét egy közös elő- vagy utótag adhat hozzá.


<a name="error_12"></a>***Az Azure NetApp fájlok API nem érhető el.***   
Az Azure API-t az Azure NetApp fájlok API-t a kötetek kezelése támaszkodik.  Ez a hiba az API-kapcsolat problémáját jelzi.

* Ok   
A mögöttes API nem válaszol, belső hiba történt az eredményül kapott. Ez a hiba akkor valószínű, hogy ideiglenes.

* Megoldás   
A probléma oka valószínűsíthetően átmeneti.  A kérelem sikeres legyen egy kis idő múlva.

* Áthidaló megoldás   
Nincs. Kötetek kezelése a mögöttes API elengedhetetlen.  


<a name="error_13"></a>***Nincsenek hitelesítő adatok előfizetésben található '{0}".***   
Ez a hiba azt jelzi, hogy a megadott hitelesítő adatok érvénytelenek, vagy hogy nem lett megfelelően beállítva az előfizetés.

* Ok   
Hitelesítő adatok érvénytelenek, vagy helytelenül beállított kötetek kezelése a szolgáltatáshoz való hozzáférés megakadályozása.

* Megoldás   
Győződjön meg arról, hogy a hitelesítő adatok beállítása és helyesen adta meg a parancssorban.

* Áthidaló megoldás   
Nincs.  Hitelesítő adatok beállítása megfelelően elengedhetetlen az Azure Files-NetApp használatával.  


<a name="error_14"></a>***Nincs a művelet eredményazonosítója található "{0}".***   
Ez a hiba azt jelzi, hogy egy belső hiba megakadályozza a művelet végrehajtását.

* Ok   
Belső hiba történt, és megakadályozta a művelet végrehajtását.

* Megoldás   
Ez a hiba akkor valószínű, hogy ideiglenes.  Várjon néhány percet, majd próbálkozzon újra. Ha a probléma tartósan fennáll, hozzon létre egy jegyet technikai támogatás a probléma kivizsgálására.

* Áthidaló megoldás   
Várjon néhány percet, és ellenőrizze, hogy ha a probléma továbbra is fennáll-e.


<a name="error_15"></a>***A művelet "{0}" nem támogatott.***   
Ez a hiba azt jelzi, hogy a parancs nem érhető el az aktív előfizetést vagy az erőforrás.

* Ok   
Az előfizetés vagy az erőforrás nem érhető el a műveletet.

* Megoldás   
Győződjön meg arról, hogy a parancsot helyesen van-e megadva és az erőforrások és az előfizetés által használt érhető el.

* Áthidaló megoldás   
A megoldás című szakaszában talál.  


<a name="error_16"></a>***A javítási művelet nem támogatott az erőforrástípushoz.***   
Ez a hiba akkor fordul elő, amikor megpróbálja módosítani a csatlakoztatási célt vagy a pillanatkép.

* Ok   
A csatlakoztatási célt van meghatározva, a létrehozást, és ez a beállítás azt követően nem módosítható.

* Megoldás   
Nincs.  Nem lehet módosítani a csatlakoztatási célt, a kötet létrehozása után.

* Áthidaló megoldás   
Nincs.


<a name="error_17"></a>***Kapott egy csak olvasható tulajdonság értéke "{0}".***   
Ez akkor fordul elő, amikor nem lehet módosítani a tulajdonság értékének meghatározása. Például nem módosíthatja a kötet azonosítója.

* Ok   
Megpróbálta módosítani egy paraméter (például a kötet azonosítója), és nem módosítható.

* Megoldás   
Nincs. A paraméter a kötet azonosítója nem módosítható.

* Áthidaló megoldás   
A kötet azonosító módosításának nincs szükség.  Ezért egy megkerülő megoldás már nem szükséges.

<a name="error_18"></a>***A kért {0} nem található.***   
Ez a hiba történik, ha egy nem létező erőforráshoz, például egy kötet vagy a pillanatkép hivatkozni. Előfordulhat, hogy az erőforrást törölték vagy hibás szó erőforrás nevét.

* Ok   
Próbált egy nem létező erőforrás (például egy kötet vagy pillanatkép), amely már törölve van, vagy helytelenül viszonyt erőforrás neve.

* Megoldás   
Ellenőrizze a helyesírást, győződjön meg arról, hogy helyesen hivatkozik rá, hogy a kérelem.

* Áthidaló megoldás   
A megoldás című szakaszában talál.

<a name="error_19"></a>***Nem sikerült beolvasni a hitelesítő adatok előfizetés "{0}".***   
Ez a hiba azt jelzi, hogy a megadott hitelesítő adatok érvénytelenek, vagy helytelenül állította az előfizetésben.

* Ok   
Hitelesítő adatok érvénytelenek vagy nem megfelelően az előfizetés beállítása megakadályozza a hozzáférést a szolgáltatás kötetek kezeléséhez.

* Megoldás   
Győződjön meg arról, hogy a hitelesítő adatok beállítása és helyesen adta meg a parancssorban.

* Áthidaló megoldás   
Nincs.  Helyes hitelesítő adatok beállítása nélkülözhetetlenek az Azure Files-NetApp használatával.

<a name="error_20"></a>***Ismeretlen Azure NetApp fájlok hiba.***   
Az Azure API-t az Azure NetApp fájlok API-t a kötetek kezelése támaszkodik. A hiba azt jelzi, hogy a kommunikáció az API-hoz a problémát.

* Ok   
Ismeretlen hiba történt az alapul szolgáló API-t küld.  Ez a hiba akkor valószínű, hogy ideiglenes.

* Megoldás   
A problémát valószínűleg átmeneti, és a kérés sikeres legyen egy kis idő múlva. Ha a probléma tartósan fennáll, hozzon létre egy támogatási jegyet megvizsgálta a problémát.

* Áthidaló megoldás   
Nincs.  Kötetek kezelése a mögöttes API elengedhetetlen.

<a name="error_21"></a>***Ismeretlen tulajdonság kapott érték "{0}".***   
Ez a hiba akkor fordul elő, amikor nem létező tulajdonságai egy erőforráshoz, például a kötet, pillanatkép vagy csatlakoztatási célt szolgálnak.

* Ok   
A kérés rendelkezik, amely használható az egyes erőforrások tulajdonságait.  Minden nem létező tulajdonságot a kérelem nem adhat meg.

* Megoldás   
Győződjön meg arról, hogy az összes tulajdonság nevét helyesen írta be és a tulajdonságok érhetők el az előfizetésben és erőforráscsoportban.

* Áthidaló megoldás   
A tulajdonság a hibát okozó kiküszöbölése érdekében a kérésben megadott tulajdonságok számának csökkentése.


<a name="error_22"></a>***Frissítési művelet nem támogatott az erőforrástípushoz.***   
Csak a kötetek frissíthetők. Ez a hiba történik, ha egy nem támogatott frissítési művelet végrehajtásához, például pillanatkép frissítése.

* Ok   
A frissíteni kívánt erőforrás nem támogatja a frissítési műveletet.  Kötetek csak a módosított tulajdonságok rendelkezhet.

* Megoldás   
Nincs.  A frissíteni próbált erőforrás nem támogatja a frissítési műveletet. Ezért nem módosítható.

* Áthidaló megoldás   
Egy kötet hozzon létre egy új erőforrást helyben a frissítéssel, és az adatok áttelepítéséhez.


<a name="error_23"></a>***Azon elemek száma: {0} objektumhoz: {1} min-max-tartományon kívül van.***   
Ez a hiba akkor fordul elő, ha az exportálási szabályok nem tesz eleget a minimális vagy maximális tartomány követelmény.  Az exportálási házirend határozza meg, akkor rendelkeznie kell legalább egy exportálási felügyeletiházirend-szabálya és legfeljebb öt exportálási szabályok.

* Ok   
A megadott exportálási házirend nem felel meg a szükséges tartomány.  

* Megoldás   
Győződjön meg arról, hogy a rendszer nem használja már az indexet, és 1 és 5 közé eshet.

* Áthidaló megoldás   
Esetén nem kötelező exportálási házirend használatára a köteteken. Ezért kihagyhatja az exportálási házirend teljesen, ha nem szeretne exportálási szabályok rendelkezik.


<a name="error_24"></a>***Ismétlődő objektum értéke hibás {0}.***   
Ez akkor fordul elő, amikor az exportálási házirend nincs definiálva egyedi indexszel.  Exportálási házirendek határozza meg, ha minden kiviteli szabályok rendelkeznie kell egy egyedi index 1 és 5 közötti.

* Ok   
A megadott exportálási házirend nem felel meg az exportálási szabályok vonatkozó követelmény. Rendelkeznie kell legalább egy exportálási felügyeletiházirend-szabálya és legfeljebb öt exportálási szabályok.  

* Megoldás   
Győződjön meg arról, hogy az index nem már használja és, hogy a tartomány 1-től 5-re van.

* Áthidaló megoldás   
Egy másik index használja a beállítani kívánt szabályt.


