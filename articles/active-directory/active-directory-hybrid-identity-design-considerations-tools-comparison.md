---
title: "Hibrid identitás: a címtár-integrációs eszközök összehasonlítása | Microsoft Docs"
description: "Ez az oldal egy átfogó táblázatot biztosít, amely összehasonlítja a címtár-integrációhoz használható különböző címtár-integrációs eszközöket."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: 1e62a4bd-4d55-4609-895e-70131dedbf52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: f87b5837c7a786c8b309a6a52e69b53c0ad4c9d8
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>Hibrid identitás: a címtár-integrációs eszközök összehasonlítása
Az évek során a címtár-integrációs eszközök bővültek és továbbfejlődtek.  Ez a dokumentum ezen eszközök egyesített nézetét is elérhetővé teszi, valamint az egyes eszközökben elérhető funkciók összehasonlíthatóságát biztosítja.

<!-- The hardcoded link is a workaround for campaign ids not working in acom links-->

> [!NOTE]
> Az Azure AD Connect magában foglalja a korábban Dirsync és AAD Sync néven kiadott összetevőket és funkciókat. Ezek az eszközök már nem érhetők el különállóan, és minden jövőbeli fejlesztés az Azure AD Connect frissítéseiben fog szerepelni, hogy mindig tudja, honnan szerezheti be a legújabb funkciókat.
> 
> A DirSync és az Azure AD Sync elavultnak számít. További információt [itt](active-directory-aadconnect-dirsync-deprecated.md) találhat.
> 
> 

A következő magyarázatot használhatja mindegyik táblázathoz.

● = Már elérhető  
JK = Jövőbeli kiadás  
NyE = Nyilvános előzetes verzió  

## <a name="on-premises-to-cloud-synchronization"></a>Helyszínről felhőbe végzett szinkronizálás
| Szolgáltatás | Azure Active Directory Connect | Azure Active Directory Synchronization Services (AAD Sync) | Azure Active Directory Synchronization Tool (DirSync) | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Csatlakozás egyetlen helyszíni AD-erdőhöz |● |● |● |● |● |
| Csatlakozás több helyszíni AD-erdőhöz |● |● | |● |● |
| Csatlakozás több helyszíni Exchange-szervezethez |● | | | | |
| Csatlakozás egyetlen helyszíni LDAP-címtárhoz |JK | | |● |● |
| Csatlakozás több helyszíni LDAP-címtárakhoz |JK | | |● |● |
| Csatlakozás helyszíni AD-hez és helyszíni LDAP-címtárakhoz |JK | | |● |● |
| Csatlakozás egyedi rendszerekhez (SQL, Oracle, MySQL stb.) |JK | | |● |● |
| Ügyfél által meghatározott attribútumok szinkronizálása (címtárbővítmények) |● | | | | |
| Csatlakozás helyszíni HR-hez (SAP, Oracle eBusiness, PeopleSoft) |JK | | |● |● |
| Támogatja a FIM-szinkronizálási szabályokat és összekötőket a helyszíni rendszerekre történő üzembe helyezéshez. | | | |● |● |

## <a name="cloud-to-on-premises-synchronization"></a>Felhőről helyszínre végzett szinkronizálás
| Szolgáltatás | Azure Active Directory Connect | Azure Active Directory Synchronization Services | Azure Active Directory Synchronization Tool (DirSync) | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Eszközök visszaírása |● | |● | | |
| Attribútum visszaírása (hibrid Exchange-környezethez) |● |● |● |● |● |
| Csoportobjektumok visszaírása |● | | | | |
| Jelszavak visszaírása (önkiszolgáló jelszó-visszaállításból (SSPR) és jelszómódosításból) |● |● | | | |

## <a name="authentication-feature-support"></a>Hitelesítési funkciók támogatása
| Funkció | Azure Active Directory Connect | Azure Active Directory Synchronization Services | Azure Active Directory Synchronization Tool (DirSync) | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Jelszó-szinkronizálás egyetlen helyszíni AD-erdőhöz |● |● |● | | |
| Jelszó-szinkronizálás több helyszíni AD-erdőhöz |● |● | | | |
| Egyszeri bejelentkezés összevonással |● |● |● |● |● |
| Jelszavak visszaírása (SSPR-ből és jelszómódosításból) |● |● | | | |

## <a name="set-up-and-installation"></a>Beállítás és telepítés
| Szolgáltatás | Azure Active Directory Connect | Azure Active Directory Synchronization Services | Azure Active Directory Synchronization Tool (DirSync) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|
| Támogatja a tartományvezérlőre történő telepítést |● |● |● | |
| Támogatja az SQL Express használatát |● |● |● | |
| Egyszerű frissítés DirSync-ről |● | | | |
| Rendszergazdai felület honosítása Windows Server-nyelvekre |● |● |● | |
| Végfelhasználói felület honosítása Windows Server-nyelvekre | | | |● |
| A Windows Server 2008 és a Windows Server 2008 R2 támogatása |● Szinkronizáláshoz, nem összevonáshoz |● |● |● |
| A Windows Server 2012 és a Windows Server 2012 R2 támogatása |● |● |● |● |

## <a name="filtering-and-configuration"></a>Szűrés és konfigurálás
| Szolgáltatás | Azure Active Directory Connect | Azure Active Directory Synchronization Services | Azure Active Directory Synchronization Tool (DirSync) | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Szűrés a tartományokon és szervezeti egységeken |● |● |● |● |● |
| Szűrés az objektumok attribútumértékein |● |● |● |● |● |
| A minimális attribútumkészletek szinkronizálásának engedélyezése (MinSync) |● |● | | | |
| Különböző szolgáltatássablonok alkalmazásának engedélyezése az attribútumfolyamokhoz |● |● | | | |
| A folyamból az attribútumok eltávolításának engedélyezése az AD-ből az Azure AD-ba |● |● | | | |
| Az attribútumfolyamok speciális testreszabásának engedélyezése |● |● | |● |● |

## <a name="next-steps"></a>Következő lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).

