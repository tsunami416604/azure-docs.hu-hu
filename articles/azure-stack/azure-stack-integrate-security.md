---
title: "Az Azure verem datacenter integrációs - biztonsági"
description: "Útmutató: Azure verem biztonsági integrálása a datacenter-biztonság"
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 02/28/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: 
ms.openlocfilehash: 8ce9045a3e4fd12d61e9b1600ee98880762bc544
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="azure-stack-datacenter-integration---security"></a>Az Azure verem datacenter integrációs - biztonsági
Az Azure verem tervezték és a biztonságot szem előtt tartva a beépített. Azure verem rendszer zárolt, így biztonsági ügynök telepítése nem támogatott.

Ez a cikk segít Azure verem biztonsági funkciók integrálása a biztonsági megoldások már telepítették az adatközpontban található.

## <a name="security-logs"></a>A biztonsági naplók

Azure verem összegyűjti az operációs rendszer és az infrastruktúra-szerepkörök és a skálázási egység csomópontok biztonsági események két percenként. A naplók tárolási fiók blob tárolók vannak tárolva.

Van egy infrastruktúra-szerepkör egy tárfiókot, és egy általános tárfiók a tipikus operációs rendszer összes eseményt.

Az állapotfigyelő erőforrás-szolgáltató hívása beolvasni a blob-tároló URL-CÍMÉT a REST-protokollal. Külső biztonsági megoldásokat a API és a tárolási fiók használatával feldolgozandó események.

### <a name="use-azure-storage-explorer-to-view-events"></a>Azure Storage kezelővel eseményeinek megtekintése

Egy Azure Tártallózó nevű eszközt használó Azure verem által összegyűjtött események kérheti le. Letöltheti az Azure Tártallózó [http://storageexplorer.com](http://storageexplorer.com).

Az alábbi eljárás egy példa segítségével Azure Tártallózó konfigurálása Azure verem:

1. Jelentkezzen be a verem Azure felügyeleti portál operátor.
2. Tallózás **tárfiókok** , és keressen **frphealthaccount**. A **frphealthaccount** fiókja az az általános tárolási fiók összes operációs rendszer események tárolására használható.

   ![Tárfiókok](media/azure-stack-integrate-security/storage-accounts.png)

3. Válassza ki **frphealthaccount**, majd kattintson a **hívóbetűk**.

   ![Elérési kulcs](media/azure-stack-integrate-security/access-keys.png)

4. A hozzáférési kulcs másolása a vágólapra.
5. Nyissa meg az Azure Storage Explorert.
6. Az a **szerkesztése** menüjében válassza **cél Azure verem**.
7. Válassza ki **fiók hozzáadása**, majd válassza ki **használja a tárfiók nevét és a kulcs**.

   ![Csatlakozás a tároló](media/azure-stack-integrate-security/connect-storage.png)

8. Kattintson a **Tovább** gombra.
9. Az a **külső tárterület csatolása** lap:

   a. Írja be azt a fióknevet **frphealthaccount**.

   b. Illessze be a tárfiók elérési kulcsának.

   c. A **tárolási végpontok tartomány**, jelölje be **más**, és adja meg a tárolási végpont **[régió]. [ Tartománynév]**.

   d. Válassza ki a **HTTP használata** jelölőnégyzetet.

   ![Külső tárterület csatolása](media/azure-stack-integrate-security/attach-storage.png)

10. Kattintson a **következő**, tekintse át az összefoglalást, és **Befejezés** a varázslót.
11. Ezután keresse meg az egyes blob tárolók és töltse le az eseményeket.

   ![Keresse meg a blobok](media/azure-stack-integrate-security/browse-blob.png)

### <a name="use-programming-languages-to-access-events"></a>A belépési események programnyelveket használata

Különböző programnyelveken segítségével a tárfiókhoz. Használja az alábbi dokumentáció egy példa, amely megfelel a nyelv kiválasztása:

[https://azure.microsoft.com/resources/samples/?term=storage+account](https://azure.microsoft.com/resources/samples/?term=storage+account)

## <a name="device-access-auditing"></a>Eszköz-hozzáférés naplózása

Minden fizikai eszköz Azure verem használható TACACS vagy RADIUS. Ez magában foglalja az alaplapi felügyeleti vezérlővel (BMC) és a hálózati kapcsolók elérésére.

Az Azure verem megoldások tartalmaz RADIUS vagy a beépített TACACS. A megoldás azonban érvényesítése a piacon elérhető meglévő RADIUS vagy TACACS megoldások támogatásához.

A RADIUS csak az MSCHAPv2 lett érvényesítve. RADIUS használata legbiztonságosabb végrehajtása jelképez.
Tekintse meg az OEM hardver gyártójánál TACAS vagy RADIUS engedélyezése a saját Azure verem megoldás részét képező eszközök.

## <a name="syslog"></a>Rendszernapló

Minden fizikai eszköz Azure verem Syslog-üzeneteket küldhet. Az Azure verem megoldások tartalmaz a Syslog-kiszolgálónak. A megoldás azonban érvényesítése a piacon elérhető meglévő Syslog megoldások üzenetek küldése támogatásához.

A Syslog-célcím összegyűjtött ehhez a központi telepítés nem kötelező paraméter, de azt is hozzáadhatók feladás egy vagy több központi telepítési.

## <a name="next-steps"></a>További lépések

[Házirend karbantartása](azure-stack-servicing-policy.md)
