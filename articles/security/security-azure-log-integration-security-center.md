---
title: "A security center integrálása az Azure napló |} Microsoft Docs"
description: "Azure Security center riasztásait napló integrációs használata beszerzéséről"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 08/29/2017
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 9acce21d544a43adcd0c0983771c02c6bb39caec
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="how-to-get-your-security-center-alerts-in-azure-log-integration"></a>A Security Center riasztásait az Azure naplóelemzés integrációs beolvasásával

A cikkben az Azure Security Center által generált biztonsági riasztások információkat le az Azure napló integrációs szolgáltatás engedélyezése szükséges lépéseket. Sikeresen végrehajtotta a lépéseket a [Ismerkedés](security-azure-log-integration-get-started.md) cikket ebben a cikkben ismertetett lépések végrehajtása előtt.

## <a name="detailed-steps"></a>Részletes lépések

Az alábbi lépéseket fogja létrehozni a szükséges Azure Active Directory szolgáltatás egyszerű, és rendelje hozzá a szolgáltatás elve olvasási engedéllyel az előfizetés:
1. Nyissa meg a parancssort, és keresse meg **c:\Program Files\Microsoft Azure napló integráció**
2. Futtassa a parancsot``azlog createazureid``

    Ez a parancs kéri az Azure bejelentkezési. A parancs létrehoz egy [Azure Active Directory szolgáltatás egyszerű](../active-directory/develop/active-directory-application-objects.md) az az Azure AD-Bérlőkkel, amely az Azure-előfizetéseket, amelyben a bejelentkezett felhasználó nem rendszergazda, a Társadminisztrátorának vagy a tulajdonos tárolni. A parancs sikertelen lesz, ha a felhasználó csak a Vendég felhasználó az Azure AD-bérlő. Az Azure hitelesítési keresztül Azure Active Directory (AD) történik. Az Azure AD identity, amely az Azure-előfizetések olvasási hozzáférést kap egy egyszerű Azlog integrációs szolgáltatás létrehozásakor létrejön.

3. Ezután futtatja az előfizetés olvasási hozzáférést rendel a most létrehozott egyszerű szolgáltatás parancsot. Ha egy előfizetés-azonosító nem adja meg, majd a parancs megkísérli rendelje hozzá a szolgáltatás egyszerű olvasó szerepkört, amelyhez előfizetéseket bármely hozzáféréssel rendelkezik. </br></br>
``azlog authorize <SubscriptionID>`` </br> például </br>
``azlog authorize 0ee55555-0bc4-4a32-a4e8-c29980000000``

    >[!NOTE]
    Ha közvetlenül az után futtassa az Engedélyezés parancsot figyelmeztetések jelenhetnek a ```createazureid``` parancsot. Néhány történő létrehozásakor a Azure AD-fiókot, és ha a fiók használható közötti késés van. Ha körülbelül 60 másodperc futtatása után várja a ```createazureid``` parancsot kell futtatni az Engedélyezés parancsot, és nem a figyelmeztetések kell megjelennie.

4. Ellenőrizze, győződjön meg arról, hogy a naplófájlok JSON vannak-e a következő mappák:
 * **c:\Users\azlog\AzureResourceManagerJson**
 * **c:\Users\azlog\AzureResourceManagerJsonLD** </br></br>
5. Ellenőrizze a következő mappák győződjön meg arról, hogy létezik-e a Security Center riasztásait a bennük foglalt:</br></br>
 * **c:\Users\azlog\AzureSecurityCenterJson**
 * **c:\Users\azlog\AzureSecurityCenterJsonLD** </br></br>

Ha esetleges problémákat tapasztal a telepítés és konfigurálás során, nyisson egy [támogatási kérelem](/azure-supportability/how-to-create-azure-support-request.md), jelölje be **napló integrációs** a szolgáltatást, amelynek támogatási kérelmet.

## <a name="next-steps"></a>További lépések
Azure napló integrációs kapcsolatos további tudnivalókért tekintse meg a következő dokumentumokat:

* [A Microsoft Azure napló integráció az Azure-naplók](https://www.microsoft.com/download/details.aspx?id=53324) – letöltőközpontból részletes rendszerkövetelményeket, és telepítése Azure naplóelemzés integrációs utasításokat.
* [Bevezetés az Azure naplóelemzés integrációs](security-azure-log-integration-overview.md) – Ez a dokumentum bemutatja a Azure naplóelemzés integrációs, annak főbb funkcióit és annak működéséről.
* [Partner konfigurációs lépések](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – ebben a blogbejegyzésben bemutatja, hogyan használható Splunk, HP ArcSight és az IBM QRadar partneri megoldások Azure naplóelemzés-integráció konfigurálása.
* [Gyakori kérdések (GYIK) integrációs Azure naplóelemzés](security-azure-log-integration-faq.md) -Ez gyakran ismételt kérdések Azure naplóelemzés integrációs kapcsolatos kérdésekre ad választ.
* [Az Azure diagnostics és az Azure-beli Auditnaplók újdonságai](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) – ebben a blogbejegyzésben bemutatja az Azure naplói, és egyéb szolgáltatásokat, amelyek segítenek betekintést nyerhet az Azure-erőforrások működésére.
