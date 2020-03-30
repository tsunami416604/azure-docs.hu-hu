---
title: Javaslatok kiújulása az Azure Security Centerben | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan lehet kiújítani az Azure Security Center ben az erőforrások védelme és a biztonsági szabályzatoknak való megfelelés érdekében javaslatokat.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 6863f4bbfc6aabc4e7bf4314ad26dd4590d31362
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603501"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Helyreállítási javaslatok az Azure Security Centerben

A javaslatok javaslatokat tesz javaslatokat az erőforrások jobb védelmére. Egy ajánlást valósít meg a javaslatban megadott javítási lépések végrehajtásával.

## <a name="remediation-steps"></a>Javítási lépések<a name="remediation-steps"></a>

Miután áttekintette az összes ajánlást, döntse el, hogy melyiket orvosolja először. Azt javasoljuk, hogy használja a [biztonságos pontszám hatása,](security-center-recommendations.md#monitor-recommendations) hogy segítsen rangsorolni, hogy mi az első teendő.

1. A listából kattintson a javaslatra.

1. Kövesse a **Szervizelési lépések szakasz utasításait.** Minden ajánlás saját utasításkészletet tartalmaz. A következő képernyőképen az alkalmazások csak HTTPS-en keresztüli forgalom engedélyezésének helyreállítási lépéseit mutatja be.

    ![Ajánlás részletei](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. A befejeződést követően megjelenik egy értesítés, amely tájékoztatja Önt, ha a javítás sikeres volt.

## <a name="quick-fix-remediation"></a>Gyorsjavítás szervizelése<a name="one-click"></a>

A Gyorsjavítás lehetővé teszi, hogy gyorsan kijavítsa a több erőforrásra vonatkozó javaslatot. Csak konkrét javaslatok esetén érhető el. A Gyorsjavítás leegyszerűsíti a szervizelést, és lehetővé teszi a biztonságos pontszám gyors növelését, növelve a környezet biztonságát.

A gyorsjavítás szervizelésének megvalósítása:

1. A **gyorsjavítást** tartalmazó ajánlások listájából! címkéjén kattintson az ajánlásra.

    [![Válassza a Gyorsjavítás!](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png#lightbox)

1. A **Nem megfelelő állapotú erőforrások** lapon válassza ki azokat az erőforrásokat, amelyeken végre kívánja hajtani a javaslatot, majd kattintson a **Javítás gombra.**

    > [!NOTE]
    > Előfordulhat, hogy a felsorolt erőforrások egy része le van tiltva, mert nem rendelkezik a módosításukhoz szükséges engedélyekkel.

1. A megerősítő mezőben olvassa el a javítás részleteit és következményeit.

    ![Gyors javítás](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > A következmények a Javítás **ablak** szürke mezőjében találhatók, amely a **Javítás**gombra kattintás után nyílik meg. Felsorolják, hogy milyen változások történnek a gyorsjavítás szervizelésének folytatásakor.

1. Szükség esetén adja meg a megfelelő paramétereket, és hagyja jóvá a szervizelést.

    > [!NOTE]
    > A szervizelés befejezése után több percig is eltarthat, amíg a **Kifogástalan erőforrások** lapon megjelennek az erőforrások. A javítási műveletek megtekintéséhez ellenőrizze a [tevékenységnaplót.](#activity-log)

1. A befejeződést követően megjelenik egy értesítés, amely tájékoztatja Önt, ha a javítás sikeres volt.

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>Gyorsjavítás-javítás naplózása a tevékenységnaplóban<a name="activity-log"></a>

A szervizelési művelet egy sablon központi telepítése vagy REST PATCH API-hívás az erőforrás on a konfiguráció alkalmazása. Ezek a műveletek az [Azure-tevékenységnaplóba](../azure-resource-manager/management/view-activity-logs.md)kerülnek.


## <a name="recommendations-with-quick-fix-remediation"></a>Javaslatok a gyorsjavítás szervizelésével

|Ajánlás|Vonzatok|
|---|---|
|Az SQL-kiszolgálókon történő naplózást engedélyezni kell|Ez a művelet lehetővé teszi az SQL naplózását ezeken a kiszolgálókon és azok adatbázisain. <br>**Megjegyzés:** <ul><li>A kijelölt SQL-kiszolgálók minden régiójában a naplónaplók mentéséhez egy tárfiókot hoz létre és oszt meg az adott régió összes kiszolgálója.</li><li>A megfelelő naplózás érdekében ne törölje vagy nevezze át az erőforráscsoportot vagy a tárfiókokat.</li></ul>|
|A speciális adatbiztonságot engedélyezni kell az SQL által felügyelt példányokon|Ez a művelet engedélyezi az SQL Advanced Data Security (ADS) szolgáltatást a kiválasztott SQL felügyelt példányokon. <br>**Megjegyzés:** <ul><li>A kijelölt SQL felügyelt példányok minden régiójában és erőforráscsoportjában a vizsgálati eredmények mentéséhez a rendszer létrehozza és megosztja a vizsgálat eredményeinek mentéséhez.</li><li> Az ADS díja SQL-kezelt példányonként $15.</li></ul>|
|A biztonsági rés felmérését engedélyezni kell az SQL által kezelt példányokon|Ez a művelet lehetővé teszi az SQL biztonsági rés felmérését a kiválasztott SQL felügyelt példányokon. <br>**Megjegyzés:**<ul><li>Az SQL biztonsági rés felmérése az SQL Advanced Data Security (ADS) csomag része. Ha az ADS még nincs engedélyezve, akkor automatikusan engedélyezve lesz a felügyelt példányon.</li><li>A kijelölt SQL felügyelt példányok minden régiójában és erőforráscsoportjában a bevizsgálati eredmények tárolására szolgáló tárfiókot az adott régió összes példánya létrehozza és osztja meg.</li><li>Az ADS díja SQL-kiszolgálónként $15.</li></ul>||
|A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon|Ez a művelet lehetővé teszi a speciális adatbiztonságot (ADS) ezeken a kijelölt kiszolgálókon és azok adatbázisain. <br>**Megjegyzés:**<ul><li>A kijelölt SQL-kiszolgálók minden régiójához és erőforráscsoportjához létrejön és megoszt egy tárfiókot a vizsgálati eredmények tárolására.<</li><li>Az ADS díja SQL-kiszolgálónként $15.</li></ul>||
|A biztonsági rés felmérését engedélyezni kell az SQL-kiszolgálókon|Ez a művelet lehetővé teszi az SQL biztonsági rés felmérését ezeken a kiválasztott kiszolgálókon és azok adatbázisain. <br>**Megjegyzés:**<ul><li>Az SQL biztonsági rés felmérése az SQL Advanced Data Security (ADS) csomag része. Ha az ADS még nincs engedélyezve, akkor automatikusan engedélyezve lesz az SQL-kiszolgálón.</li><li>A kijelölt SQL-kiszolgálók minden régiójához és erőforráscsoportjához létrejön és megoszt egy tárfiókot a vizsgálati eredmények tárolására az adott régió összes példánya.</li><li>Az ADS díja SQL-kiszolgálónként $15.</li></ul>||
|Az SQL-adatbázisok transzparens adattitkosítását engedélyezni kell|Ez a művelet engedélyezi az SQL Database Transzparens adattitkosítást (TDE) a kijelölt adatbázisokon. <br>**Megjegyzés:** Alapértelmezés szerint a szolgáltatás által felügyelt TDE-kulcsok lesznek használva.
|Engedélyezni kell a tárfiókokba történő biztonságos átvitelt|Ez a művelet frissíti a tárfiók biztonságát, hogy csak biztonságos kapcsolatokon elérhető kérelmekengedélyezése legyen. (HTTPS). <br>**Megjegyzés:**<ul><li>A HTTP protokollt használó kérelmeket a rendszer elutasítja.</li><li>Ha az Azure files service használata, a kapcsolat titkosítás nélkül sikertelen lesz, beleértve a forgatókönyvek smb 2.1, SMB 3.0 titkosítás nélkül, és néhány ízek a Linux SMB-ügyfél. További információk.</li></ul>|
|A webalkalmazás csak HTTPS-en keresztül érhető el|Ez a művelet átirányítja az összes forgalmat a HTTP-ről HTTPS-re a kijelölt erőforrásokon. <br>**Megjegyzés:**<ul><li>Egy HTTPS-végpont, amely nem rendelkezik SSL-tanúsítvánnyal, "Adatvédelmi hibával" jelenik meg a böngészőben. Így az egyéni tartománnyal rendelkező felhasználóknak ellenőrizniük kell, hogy ssl-tanúsítványt állítottak-e be.</li><li>Győződjön meg arról, hogy az alkalmazásszolgáltatást védő csomag- és webalkalmazás-tűzfalak engedélyezik a HTTPS-munkamenetek továbbítását.</li></ul>|
|A Függvényalkalmazás csak HTTPS-en keresztül érhető el|Ez a művelet átirányítja az összes forgalmat a HTTP-ről HTTPS-re a kijelölt erőforrásokon. <br>**Megjegyzés:**<ul><li>Egy HTTPS-végpont, amely nem rendelkezik SSL-tanúsítvánnyal, "Adatvédelmi hibával" jelenik meg a böngészőben. Így az egyéni tartománnyal rendelkező felhasználóknak ellenőrizniük kell, hogy ssl-tanúsítványt állítottak-e be.</li><li>Győződjön meg arról, hogy az alkalmazásszolgáltatást védő csomag- és webalkalmazás-tűzfalak engedélyezik a HTTPS-munkamenetek továbbítását.</li></ul>|
|Az API-alkalmazás csak HTTPS-en keresztül érhető el|Ez a művelet átirányítja az összes forgalmat a HTTP-ről HTTPS-re a kijelölt erőforrásokon. <br>**Megjegyzés:**<ul><li>Egy HTTPS-végpont, amely nem rendelkezik SSL-tanúsítvánnyal, "Adatvédelmi hibával" jelenik meg a böngészőben. Így az egyéni tartománnyal rendelkező felhasználóknak ellenőrizniük kell, hogy ssl-tanúsítványt állítottak-e be.</li><li>Győződjön meg arról, hogy az alkalmazásszolgáltatást védő csomag- és webalkalmazás-tűzfalak engedélyezik a HTTPS-munkamenetek továbbítását.</li></ul>|
|A távoli hibakeresést ki kell kapcsolni a webalkalmazáshoz|Ez a művelet letiltja a távoli hibakeresést.|
|A távoli hibakeresést ki kell kapcsolni a Függvényalkalmazásban|Ez a művelet letiltja a távoli hibakeresést.|
|A távoli hibakeresést ki kell kapcsolni az API-alkalmazáshoz|Ez a művelet letiltja a távoli hibakeresést.|
|A CORS nem teszi lehetővé, hogy minden erőforrás hozzáférjen a webalkalmazáshoz|Ez a művelet megakadályozza, hogy más tartományok hozzáférjenek a webalkalmazáshoz. Bizonyos tartományok engedélyezéséhez írja be őket az Engedélyezett eredet mezőbe (vesszővel elválasztva). <br>**Megjegyzés:** Ha üresen hagyja a mezőt, az blokkolja az összes kereszteredetű hívást.'Param mező címe: 'Allowed origins'|
|A CORS nem teszi lehetővé, hogy minden erőforrás hozzáférjen a Függvényalkalmazáshoz|Ez a művelet megakadályozza, hogy más tartományok hozzáférjenek a függvényalkalmazáshoz. Bizonyos tartományok engedélyezéséhez írja be őket az Engedélyezett eredet mezőbe (vesszővel elválasztva). <br>**Megjegyzés:** Ha üresen hagyja a mezőt, az blokkolja az összes kereszteredetű hívást.'Param mező címe: 'Allowed origins'|
|A CORS nem teszi lehetővé, hogy minden erőforrás hozzáférjen az API-alkalmazáshoz|Ez a művelet megakadályozza, hogy más tartományok hozzáférjenek az API-alkalmazáshoz. Bizonyos tartományok engedélyezéséhez írja be őket az Engedélyezett eredet mezőbe (vesszővel elválasztva). <br>**Megjegyzés:** Ha üresen hagyja a mezőt, az blokkolja az összes kereszteredetű hívást.'Param mező címe: 'Allowed origins'|
|A figyelési ügynöknek engedélyeznie kell a virtuális gépeken|Ez a művelet egy figyelési ügynököt telepít a kiválasztott virtuális gépekre. Válassza ki azt a munkaterületet, amelynek az ügynöknek jelentést szeretne adni.<ul><li>Ha a frissítési házirend automatikus, akkor új meglévő példányokon fog üzembe helyezni.</li><li>Ha a frissítési házirend manuálisra van állítva, és az ügynököt meglévő példányokra szeretné telepíteni, jelölje be a jelölőnégyzetet. [További információ](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set)</li></ul>|
|A Key Vault diagnosztikai naplóit engedélyezni kell|Ez a művelet lehetővé teszi a diagnosztikai naplók a key vaults. A diagnosztikai naplók és metrikák a kiválasztott munkaterületen kerülnek mentésre.|
|A service-busz diagnosztikai naplóit engedélyezni kell|Ez a művelet lehetővé teszi a diagnosztikai naplók a szolgáltatásbuszon. A diagnosztikai naplók és metrikák a kiválasztott munkaterületen kerülnek mentésre.|

## <a name="next-steps"></a>További lépések

Ebben a dokumentumban a Security Centerben a javaslatok kijavítása. A Biztonsági központról az alábbi témakörökben olvashat bővebben:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](tutorial-security-policy.md) – Megtudhatja, hogyan konfigurálhatja az Azure-előfizetések és erőforráscsoportok biztonsági szabályzatait.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md) – Ismerje meg, hogyan figyelheti az Azure-erőforrások állapotát.
