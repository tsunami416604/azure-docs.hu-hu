---
title: Azure Log Integration Azure Active Directory naplókkal | Microsoft Docs
description: Ismerje meg, hogyan telepítheti az Azure Log Integration szolgáltatást, és hogyan integrálhatja a naplókat az Azure-naplókból
services: security
documentationcenter: na
author: Barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 05/28/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: e7f3a9bc9aade5f8ade857e9c2f14f8e9898a7da
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244670"
---
# <a name="integrate-azure-active-directory-audit-logs"></a>Azure Active Directory naplóinak integrálása

Azure Active Directory (Azure AD) naplózási események segítségével azonosíthatja a Azure Active Directoryban bekövetkezett privilegizált műveleteket. A nyomon követhető események típusai a [Azure Active Directory naplózási jelentés eseményeinek](../../active-directory/reports-monitoring/concept-audit-logs.md)áttekintésével jeleníthetők meg.


>[!IMPORTANT]
> Az Azure log-integrációs szolgáltatást a 06/15/2019-as rendszer elavulttá teszi. A AzLog letöltése a 2018. június 27-én le lett tiltva. Útmutatás a további lépések áttekintéséhez: az [Azure monitor használata az Siem-eszközökkel való integráláshoz](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>Azure Active Directory naplók integrálásának lépései

> [!NOTE]
> A cikk lépéseinek elvégzése előtt tekintse át az [első lépések](azure-log-integration-get-started.md) című cikket, és végezze el a szükséges lépéseket.

1. Nyissa meg a parancssort, és futtassa a következő parancsot:

   ``cd c:\Program Files\Microsoft Azure Log Integration``

2. Futtassa ezt a parancsot: 
 
   ``azlog createazureid``

   Ez a parancs felszólítja az Azure-bejelentkezésre. A parancs Ezután létrehoz egy Azure Active Directory egyszerű szolgáltatást azon Azure AD-bérlők számára, amelyek olyan Azure-előfizetéseket futtatnak, amelyekben a bejelentkezett felhasználó rendszergazda, társ-rendszergazda vagy tulajdonos. A parancs sikertelen lesz, ha a bejelentkezett felhasználó csak egy vendég felhasználó az Azure AD-bérlőben. Az Azure-ba történő hitelesítés az Azure AD-n keresztül történik. Egy egyszerű szolgáltatásnév létrehozása a Azure Log Integration hoz létre egy Azure AD-identitást, amely hozzáférést kap az Azure-előfizetések olvasásához.

3. Futtassa a következő parancsot a bérlői azonosító megadásához. A parancs futtatásához a bérlői rendszergazda szerepkör tagjának kell lennie.

   ``Azlog.exe authorizedirectoryreader tenantId``

   Példa:

   ``AZLOG.exe authorizedirectoryreader ba2c0000-d24b-4f4e-92b1-48c4469999``

4. A következő mappákban ellenőrizheti, hogy a rendszer létrehozza-e a Azure Active Directory naplófájlok JSON-fájljait:

   * **C:\Users\azlog\AzureActiveDirectoryJson**
   * **C:\Users\azlog\AzureActiveDirectoryJsonLD**

A következő videó bemutatja a cikkben ismertetett lépéseket:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Azure-AD-Integration/player]


> [!NOTE]
> A JSON-fájlokban lévő információk a biztonsági és az eseménykezelő (SIEM) rendszerbe való bekapcsolásával kapcsolatos konkrét utasításokért forduljon a SIEM-szolgáltatóhoz.

A közösségi segítségnyújtás a [Azure log INTEGRATION MSDN fórumán](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration)keresztül érhető el. Ez a fórum lehetővé teszi, hogy a Azure Log Integration közösség tagjai kérdéseket, válaszokat, tippeket és trükköket támogassák egymásnak. Emellett a Azure Log Integration csapata figyeli ezt a fórumot, és segít, amikor csak lehet.

Létrehozhat egy [támogatási kérést](../../azure-supportability/how-to-create-azure-support-request.md)is. Válassza ki a **naplózási integráció** lehetőséget arra a szolgáltatásra, amelyhez támogatást kér.

## <a name="next-steps"></a>Következő lépések
A Azure Log Integrationról további információt a következő témakörben talál:

* [Microsoft Azure log Integration az Azure-naplókhoz](https://azure.microsoft.com/services/monitor/): Ez a letöltőközpont oldal részleteket, rendszerkövetelményeket és telepítési utasításokat tartalmaz a Azure log Integrationhoz.
* [Bevezetés a Azure log Integrationba](azure-log-integration-overview.md): ebben a cikkben bemutatjuk a Azure log Integration, a főbb képességeiket és azok működését.
* [Azure log INTEGRATION GYIK](azure-log-integration-faq.md): Ez a cikk a Azure log Integration kapcsolatos kérdésekre ad választ.
* [Új funkciók a Azure Diagnostics és az Azure-naplókhoz](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/): ebben a blogbejegyzésben bemutatjuk az Azure-naplókba és más funkciókba, amelyek segítségével betekintést nyerhet az Azure-erőforrások műveleteibe.
