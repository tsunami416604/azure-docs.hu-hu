---
title: 'Hibrid identitás: a címtár-integrációs eszközök összehasonlítása | Microsoft Docs'
description: Ez az oldal egy átfogó táblázatot biztosít, amely összehasonlítja a címtár-integrációhoz használható különböző címtár-integrációs eszközöket.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 1e62a4bd-4d55-4609-895e-70131dedbf52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/07/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 052d99a819aee415d5e7ad6dc00b8c786af0f636
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811104"
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>Hibrid identitás: a címtár-integrációs eszközök összehasonlítása
Az évek során a címtár-integrációs eszközök bővültek és továbbfejlődtek.  


- [Fim](https://docs.microsoft.com/previous-versions/windows/desktop/forefront-2010/ff182370%28v%3dvs.100%29) és [MIM](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016) továbbra is támogatottak, és elsősorban a helyszíni rendszerek közötti szinkronizálás engedélyezése.   A [FIM Windows Azure AD-összekötő](https://docs.microsoft.com/previous-versions/mim/dn511001(v=ws.10)?redirectedfrom=MSDN) támogatja a FIM és a MIM, de nem ajánlott az új telepítések – az ügyfelek a helyszíni források, mint a Notes vagy az SAP HCM kell használnia mim feltöltéséhez Active Directory tartományi szolgáltatások (AD DS), majd használja az Azure AD Connect sync vagy az Azure AD Connect felhőkiépítés szinkronizálni a DD DS az Azure AD.
- [Az Azure AD Connect szinkronizálása](how-to-connect-sync-whatis.md) magában foglalja a korábban a DirSync és az Azure AD Sync szolgáltatásban kiadott összetevőket és funkciókat az AD DS-erdők és az Azure AD közötti szinkronizáláshoz.  
- [Az Azure AD Connect felhőalapú kiépítés](../cloud-provisioning/what-is-cloud-provisioning.md) egy új Microsoft-ügynök az AD DS-ből az Azure AD-be való szinkronizáláshoz, amely olyan forgatókönyvek esetén hasznos, mint például az egyesülés és az akvizíció, ahol a beszerzett vállalat AD-erdői el vannak különítve az anyavállalat AD-erdőitől.

Ha többet szeretne megtudni az Azure AD Connect-szinkronizálás és az Azure AD Connect felhőalapú kiépítés közötti különbségekről, olvassa el a [Mi az Azure AD Connect felhőkiépítés című cikkében?](../cloud-provisioning/what-is-cloud-provisioning.md)

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).

