---
title: Az Azure Stack adatközpont integrációja - biztonság
description: 'Útmutató: Azure Stack a biztonságot integrálhatja a datacenter security'
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 02/28/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: ''
ms.openlocfilehash: 9f356b814ac1ac6ca8b6d6efe7cb9f5d9ed66270
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39442475"
---
# <a name="azure-stack-datacenter-integration---security"></a>Az Azure Stack adatközpont integrációja - biztonság
Az Azure Stack eljárása alapján terveztük és a biztonság szem előtt. Az Azure Stack egy rendszer zárolt, így a szoftver biztonsági ügynök telepítése nem támogatott.

Ez a cikk segít az Azure Stack biztonsági szolgáltatásainak integrálását a biztonsági megoldások már üzembe helyezte az adatközpontban.

## <a name="security-logs"></a>A biztonsági naplók

Az Azure Stack gyűjti az operációs rendszer és az infrastruktúra-szerepkörök és a skálázási egység csomópontok biztonsági események két percenként. A naplók a storage-fiók blob tárolók vannak tárolva.

Nincs infrastruktúra szerepkörönként egy tárfiókot, és a egy általános célú tárfiók a tipikus operációs rendszer összes eseményt.

Az egészségügyi erőforrás-szolgáltató a blob-tároló URL-Címének lekéréséhez a REST protokoll használatával nem hívható meg. Külső biztonsági megoldásokat az API-t és a storage-fiókok segítségével a feldolgozási események beolvasása.

### <a name="use-azure-storage-explorer-to-view-events"></a>Események megtekintése az Azure Storage Explorer használata

Azure Stack egy Azure Storage Explorer néven eszközzel által gyűjtött eseményeket kérheti le. Letöltheti az Azure Storage Explorer [ http://storageexplorer.com ](http://storageexplorer.com).

Az alábbi eljárás egy példa az Azure Storage Explorer konfigurálása az Azure Stack használatával:

1. Jelentkezzen be az Azure Stack rendszergazdai portálon kezelőként.
1. Tallózás **tárfiókok** , és keressen **frphealthaccount**. A **frphealthaccount** az általános tárolási fiók tárolják az operációs rendszer összes eseményt.

   ![Tárfiókok](media/azure-stack-integrate-security/storage-accounts.png)

1. Válassza ki **frphealthaccount**, majd kattintson a **Tárelérési kulcsok**.

   ![Elérési kulcs](media/azure-stack-integrate-security/access-keys.png)

1. A hozzáférési kulcs másolása a vágólapra.
1. Nyissa meg az Azure Storage Explorerben.
1. Az a **szerkesztése** menüjében válassza **cél Azure Stack**.
1. Válassza ki **fiók hozzáadása**, majd válassza ki **tárfiók nevének és kulcsának**.

   ![Tároló csatlakoztatása](media/azure-stack-integrate-security/connect-storage.png)

1. Kattintson a **Tovább** gombra.
1. Az a **külső tárterület csatolása** oldalon:

   a. Írja be a fiók nevét **frphealthaccount**.

   b. Illessze be a tárfiók hozzáférési kulcsát.

   c. Alatt **tárolási végpontok tartománya**válassza **más**, és adja meg a storage-végpont **[régió]. [ Tartománynév]**.

   d. Válassza ki a **HTTP használata** jelölőnégyzetet.

   ![Külső tárterület csatolása](media/azure-stack-integrate-security/attach-storage.png)

1. Kattintson a **tovább**, tekintse át az összefoglalást, és **Befejezés** a varázslót.
1. Ezután keresse meg az egyes blobtárolók és az események letöltése.

   ![Blobok tallózása](media/azure-stack-integrate-security/browse-blob.png)

### <a name="use-programming-languages-to-access-events"></a>A hozzáférési események programozási nyelvet használja

Számos programozási nyelvet használhatja a tárfiók eléréséhez. Válasszon ki egy példa, amely megfelel a nyelvet használja az alábbi dokumentáció:

[https://azure.microsoft.com/resources/samples/?term=storage+account](https://azure.microsoft.com/resources/samples/?term=storage+account)

## <a name="device-access-auditing"></a>Eszköz-hozzáférés naplózása

Minden fizikai eszköz az Azure Stackben TACACS vagy RADIUS-használatát támogatja. Ez magában foglalja a hozzáférést az alaplapi felügyeleti vezérlőnek (BMC) és a hálózati kapcsolók.

Az Azure Stack-megoldások tartalmaz RADIUS vagy a beépített TACACS. A megoldások azonban ellenőrzése a piacon elérhető meglévő RADIUS vagy TACACS megoldások támogatása érdekében.

A RADIUS csak az MSCHAPv2 lett érvényesítve. RADIUS használata a legbiztonságosabb megvalósítás Ez jelöli.
Tekintse meg az OEM hardvergyártójához TACAS vagy RADIUS engedélyezése az Azure Stack megoldás részét képező eszközök.

## <a name="syslog"></a>Rendszernapló

Minden fizikai eszköz az Azure Stackben Syslog-üzeneteket küldhet. Az Azure Stack-megoldások Syslog-kiszolgálót tartalmaz. A megoldások azonban ellenőrzése a piacon elérhető meglévő Syslog megoldásokat üzenetek küldése támogatásához.

A Syslog-célcím nem gyűjtött a központi telepítés kötelező paraméter, de azt is hozzáadhatók üzembe helyezés után.

## <a name="next-steps"></a>További lépések

[Karbantartási szabályzat](azure-stack-servicing-policy.md)
