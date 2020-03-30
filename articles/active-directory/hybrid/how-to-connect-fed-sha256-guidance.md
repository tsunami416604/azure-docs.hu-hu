---
title: Aláírás-kivonatoló algoritmus módosítása az Office 365 függő entitásmegbízhatóságához – Azure
description: Ez a lap útmutatást nyújt az Összevonási megbízhatósági kapcsolat SHA algoritmusának módosításához az Office 365-tel
keywords: SHA1,SHA256,O365,federation,aadconnect,adfs,ad fs,change sha,federation trust,relying party trust
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: cf6880e2-af78-4cc9-91bc-b64de4428bbd
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2233b434fda628dcf812a62f06541fc4b0296aba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897352"
---
# <a name="change-signature-hash-algorithm-for-office-365-relying-party-trust"></a>Aláírás-kivonatoló algoritmus módosítása az Office 365 függő entitásmegbízhatóságához
## <a name="overview"></a>Áttekintés
Az Active Directory összevonási szolgáltatások (AD FS) aláírja a jogkivonatokat a Microsoft Azure Active Directoryba annak érdekében, hogy azok ne lehessen illetéktelenül illetéktelenül illetéktelenül illetéktelenül illetéktelenül illetéktelenül illetéktelenül illetéktelenül illetéktelenül közlekedni. Ez az aláírás alapulhat SHA1 vagy SHA256. Az Azure Active Directory mostantól támogatja az SHA256 algoritmussal aláírt jogkivonatokat, és azt javasoljuk, hogy a legmagasabb szintű biztonság érdekében a tokenaláíró algoritmust SHA256-ra állítja. Ez a cikk ismerteti a jogkivonat-aláíró algoritmus biztonságosabb SHA256 szintre beállításához szükséges lépéseket.

>[!NOTE]
>A Microsoft az SHA256 használatát javasolja a tokenek aláírásának algoritmusaként, mivel biztonságosabb, mint az SHA1, de az SHA1 továbbra is támogatott lehetőség marad.

## <a name="change-the-token-signing-algorithm"></a>A tokenaláíró algoritmus módosítása
Miután beállította az aláírási algoritmust az alábbi két folyamat egyikével, az AD FS aláírja az Office 365 függő entitás megbízhatóságának tokenjeit az SHA256-tal. Nem kell további konfigurációs módosításokat végrehajtania, és ez a módosítás nincs hatással az Office 365-höz vagy más Azure AD-alkalmazásokhoz való hozzáférésre.

### <a name="ad-fs-management-console"></a>AD FS felügyeleti konzol
1. Nyissa meg az AD FS felügyeleti konzolt az elsődleges AD FS-kiszolgálón.
2. Bontsa ki az AD FS csomópontot, és kattintson **a Függő entitás megbízhatósági kapcsolatok parancsára.**
3. Kattintson a jobb gombbal az Office 365/Azure függő entitás megbízhatóságára, és válassza a **Tulajdonságok parancsot.**
4. Válassza a **Speciális** lapot, és válassza ki az SHA256 biztonságos kivonatoló algoritmust.
5. Kattintson az **OK** gombra.

![SHA256 aláírási algoritmus - MMC](./media/how-to-connect-fed-sha256-guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>AD FS PowerShell-parancsmagok
1. Bármely AD FS-kiszolgálón nyissa meg a PowerShellt rendszergazdai jogosultságokkal.
2. Állítsa be a biztonságos kivonatoló algoritmust a **Set-AdfsRelyingPartyTrust** parancsmag használatával.
   
   <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'https://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>Is olvasni
* [Az Office 365 megbízhatóságának javítása az Azure AD Connect segítségével](how-to-connect-fed-management.md#repairthetrust)

