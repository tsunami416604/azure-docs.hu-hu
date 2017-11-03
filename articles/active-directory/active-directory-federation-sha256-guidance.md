---
title: "Office 365 függő entitás megbízhatóságához módosítás aláírás-kivonatoló algoritmus |} Microsoft Docs"
description: "Ezen a lapon útmutatást nyújt a SHA-képzési algoritmus összevonási megbízhatósági kapcsolat, és az Office 365 módosítása"
keywords: "SHA1, SHA256, O365, összevonási, aadconnect, az AD FS, az ad fs, a módosítás sha összevonási megbízhatósági kapcsolat, a megbízható függő entitás"
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: samueld
editor: 
ms.assetid: cf6880e2-af78-4cc9-91bc-b64de4428bbd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: anandy
ms.openlocfilehash: c581b1468630a9f28204592c936360b72f42f0d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="change-signature-hash-algorithm-for-office-365-relying-party-trust"></a>Aláírás-kivonatoló algoritmus az Office 365 megbízható függő entitás módosítása
## <a name="overview"></a>Áttekintés
Active Directory összevonási szolgáltatások (AD FS) a Microsoft Azure Active Directoryban annak érdekében, hogy azok ne lehessen illetéktelenül jogkivonatai jelentkezik. Az aláírás SHA1 vagy SHA-256-alapú lehet. Az Azure Active Directory mostantól támogatja az SHA-256 algoritmussal aláírt jogkivonatokat, és javasolt SHA256 a legmagasabb szintű biztonság a jogkivonat-aláíró algoritmus beállítást. Ez a cikk ismerteti a jogkivonat-aláíró algoritmus szint a biztonságosabb SHA-256 beállításához szükséges lépéseket.

>[!NOTE]
>A Microsoft azt javasolja, hogy SHA-256 használatát a algoritmusként biztonságosabb, mint az SHA1, de SHA1 még mindig egy támogatott beállítás a jogkivonatok aláírásához.

## <a name="change-the-token-signing-algorithm"></a>A jogkivonat-aláíró algoritmus módosítása
Miután beállította az aláírási algoritmus az alábbi két folyamatok egyike, az AD FS aláírja a jogkivonatok az Office 365 megbízható függő entitás SHA-256. Nem kell további konfigurációs módosításokat, és ez a változás nincs hatással van a elérését Office 365- vagy más Azure AD-alkalmazások.

### <a name="ad-fs-management-console"></a>AD FS felügyeleti konzol
1. Nyissa meg az elsődleges AD FS-kiszolgáló az AD FS felügyeleti konzolon.
2. Bontsa ki az AD FS csomópontot, és kattintson a **függő entitás Megbízhatóságai**.
3. Kattintson a jobb gombbal az Office 365 vagy az Azure függő entitás megbízhatóságához, és válassza ki **tulajdonságok**.
4. Válassza ki a **speciális** fülre, és válassza ki a biztonságos kivonatoló algoritmus SHA-256.
5. Kattintson az **OK** gombra.

![SHA-256 aláírási algoritmus--MMC](./media/active-directory-aadconnectfed-sha256guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>AD FS PowerShell-parancsmagok
1. Egyetlen AD FS-kiszolgálón nyissa meg a Powershellt a rendszergazdai jogosultságokkal.
2. Állítsa be a biztonságos kivonatoló algoritmus használatával a **Set-AdfsRelyingPartyTrust** parancsmag.
   
   <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'http://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>Is
* [Az Azure AD Connect Office 365-megbízhatóság javítása](connect/active-directory-aadconnect-federation-management.md#repairthetrust)

