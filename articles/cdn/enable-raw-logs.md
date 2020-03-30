---
title: Azure CDN HTTP nyers naplók
description: Ez a cikk ismerteti az Azure CDN HTTP nyers naplók.
services: cdn
author: sohamnchatterjee
manager: danielgi
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2020
ms.author: sohamnc
ms.openlocfilehash: c6e8570746ae3dd0051dbec084c89d90580d28b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371631"
---
# <a name="azure-cdn-http-raw-logs"></a>Azure CDN HTTP nyers naplók
A nyers naplók gazdag információkat nyújtanak a naplózáshoz és a hibaelhárításhoz fontos műveletekről és hibákról. A nyers naplók eltérnek a tevékenységnaplóktól. A tevékenységnaplók betekintést nyújtanak az Azure-erőforrásokon végzett műveletekbe. A nyers naplók rögzítik az erőforrás műveleteit.

> [!IMPORTANT]
> A HTTP nyers naplók funkció érhető el az Azure CDN a Microsofttól.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené. 

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a .

## <a name="configuration"></a>Konfiguráció

Az Azure CDN nyers naplóinak konfigurálása a Microsoft-profilból: 

1. Az Azure Portal menüben válassza az **Összes erőforrás** >> **\<a CDN-profil>. **

2. A **Figyelés**csoportban válassza a **Diagnosztikai beállítások lehetőséget.**

3. Válassza **a + Diagnosztikai beállítás hozzáadása lehetőséget**.

    ![CDN diagnosztikai beállítás](./media/cdn-raw-logs/raw-logs-01.png)

    > [!IMPORTANT]
    > A nyers naplók csak a profil szinten érhetők el, míg az összesített http állapotkód naplók a végpont szintjén érhetők el.

4. A **Diagnosztikai beállítások csoportban**adja meg a diagnosztikai beállítás nevét a **Diagnosztikai beállítások neve területen.**

5. Válassza ki a **naplót,** és állítsa be a megőrzési napokban.

6. Válassza ki a **Cél részleteit**. A céllehetőségek a következők:
    * **Küldés a Log Analyticsnek**
        * Válassza ki az **Előfizetés és** a Log **Analytics munkaterületet.**
    * **Archiválás tárfiókba**
        * Válassza ki az **Előfizetés** és a **Tárfiók lehetőséget.**
    * **Streamelés eseményközpontba**
        * Válassza az **Előfizetés**, **Az eseményközpont névtere**, **az Eseményközpont neve (nem kötelező)** és **az Eseményközpont házirendjének nevét.**

    ![CDN diagnosztikai beállítás](./media/cdn-raw-logs/raw-logs-02.png)

7. Kattintson a **Mentés** gombra.

## <a name="raw-logs-properties"></a>Nyers naplók tulajdonságai

Az Azure CDN a Microsoft Service jelenleg nyers naplókat biztosít. A nyers naplók egyedi API-kérelmeket biztosítanak minden olyan bejegyzéshez, amely a következő sémával rendelkezik: 

| Tulajdonság              | Leírás                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Követési hivatkozás     | Az egyedi hivatkozási karakterlánc, amely azonosítja a Bejárati ajtó által kiszolgált kérelmet, amelyet X-Azure-Ref fejlécként is küldött az ügyfélnek. Egy adott kérelem hozzáférési naplóiban a részletek kereséséhez szükséges. |
| HttpMódszer            | A kérelem által használt HTTP-módszer.                                                                                                                                                                     |
| HttpVerzió           | A kérelem vagy kapcsolat típusa.                                                                                                                                                                   |
| RequestUri (Kérés)            | A fogadott kérelem URI-ja.                                                                                                                                                                         |
| Kérésbájt          | A HTTP-kérelem üzenet mérete bájtban, beleértve a kérelem fejlécek és a kérelem törzse.                                                                                                   |
| Válaszbájt         | A háttérkiszolgáló által válaszként küldött bájtok.                                                                                                                                                    |
| Useragent             | Az ügyfél által használt böngészőtípusa.                                                                                                                                                               |
| ClientIp (Ügyfél IP-címe)              | A kérést küldő ügyfél IP-címe.                                                                                                                                                  |
| Időtaken             | A művelet időtartama ezredmásodpercben.                                                                                                                                            |
| SecurityProtocol (Biztonsági protokoll)      | A kérés által használt TLS/SSL protokollverzió, vagy null, ha nincs titkosítás.                                                                                                                           |
| Végpont              | A CDN-végpontállomás a szülő CDN-profil alatt konfigurált.                                                                                                                                   |
| Háttérállomás neve     | Annak a háttérállomásnak vagy eredetnek a neve, amelybe a kérelmeket küldik.                                                                                                                                |
| Elküldve a forráspajzsra | Ha ez igaz, az azt jelenti, hogy a kérést az eredeti pajzs gyorsítótárából válaszolták meg a peremhálózati pop helyett. Az originális pajzs egy szülőgyorsítótár, amely a gyorsítótár találati arányának javítására szolgál.                                       |
| httpstatuskód        | A proxytól visszaadott HTTP-állapotkód.                                                                                                                                                        |
| httpStatusDetails     | A kérelem eredményül kapott állapota. Ennek a karakterlánc-értéknek a jelentése egy Állapothivatkozás-táblában található.                                                                                              |
| Pop                   | A peremhálózati pop, amely válaszolt a felhasználói kérelemre. A POP-ok rövidítései a megfelelő metrók repülőtéri kódjai.                                                                                   |
| Gyorsítótár állapota          | Azt jelzi, hogy az objektum a gyorsítótárból lett-e visszaadva, vagy az eredeti forrásból származik.                                                                                                             |
> [!IMPORTANT]
> A HTTP Raw naplók funkció automatikusan elérhető minden olyan profilhoz, amelyet **2020.** A korábban létrehozott CDN-profilok esetében a naplózás beállítása után frissíteni kell a CDN-végpontot. Például lehet navigálni a geoszűrés alatt CDN-végpontok és blokkolja bármely ország nem releváns a munkaterhelés és a mentés találatot. 

> [!NOTE]
> A naplók a Log Analytics-profil alatt tekinthető kontle. A mintalekérdezés úgy nézne ki, mint az AzureDiagnostics | ahol kategória == "AzureCdnAccessLog"

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben engedélyezte a HTTP nyers naplókat a Microsoft CDN szolgáltatáshoz.

Az Azure CDN-ről és a cikkben említett egyéb Azure-szolgáltatásokról a következő témakörben olvashat bővebben:

* [Elemzés](cdn-log-analysis.md) Azure CDN-használati minták.

* További információ az [Azure Monitorról.](https://docs.microsoft.com/azure/azure-monitor/overview)

* [A Log Analytics konfigurálása az Azure Monitorban.](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)
