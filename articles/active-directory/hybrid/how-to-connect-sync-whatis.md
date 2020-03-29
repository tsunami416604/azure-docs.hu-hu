---
title: 'Azure AD Connect szinkronizálás: A szinkronizálás megértése és testreszabása | Microsoft dokumentumok'
description: Bemutatja, hogyan működik az Azure AD Connect szinkronizálása, és hogyan szabható testre.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ee4bf802-045b-4da0-986e-90aba2de58d6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3b87f40d75d4045155e7dd953dc76ffd9de2b34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60348739"
---
# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Azure AD Connect szinkronizálás: A szinkronizálás megértése és testreszabása
Az Azure Active Directory Connect szinkronizálási szolgáltatások (Azure AD Connect sync) az Azure AD Connect egyik fő összetevője. Gondoskodik a helyszíni környezet és az Azure AD közötti identitásadatok szinkronizálásához kapcsolódó összes műveletről. Az Azure AD Connect sync a DirSync, az Azure AD Sync és a Forefront Identity Manager utódja az Azure Active Directory-összekötő konfigurálásával.

Ez a témakör az **Azure AD Connect szinkronizálási** (más néven **szinkronizálási motor)** otthona, és felsorolja az összes hozzá kapcsolódó témakörre mutató hivatkozásokat. Az Azure AD Connect re mutató hivatkozásokról a [Helyszíni identitások integrálása az Azure Active Directoryval.](whatis-hybrid-identity.md)

A szinkronizálási szolgáltatás két összetevőből áll, a helyszíni **Azure AD Connect szinkronizálási** összetevőből és az Azure AD Azure AD nevű szolgáltatási oldalából, az **Azure AD Connect szinkronizálási szolgáltatásból.**

## <a name="azure-ad-connect-sync-topics"></a>Az Azure AD Connect szinkronizálási témakörei
| Témakör | Mit fed le, és mikor kell olvasni |
| --- | --- |
| **Az Azure AD Connect szinkronizálásának alapjai** | |
| [Az architektúra ismertetése](concept-azure-ad-connect-sync-architecture.md) |Azoknak, akik újak a szinkronizálási motor, és szeretné megismerni az architektúra és a használt kifejezéseket. |
| [Technikai kulcsfogalmak](how-to-connect-sync-technical-concepts.md) |Az architektúra témakörének rövid verziója, és röviden ismerteti a használt kifejezéseket. |
| [Azure AD Connect-topológiák](plan-connect-topologies.md) |A szinkronizálási motor által támogatott különböző topológiákat és forgatókönyveket ismerteti. |
| **Egyéni konfiguráció** | |
| [A telepítővarázsló ismételt futtatása](how-to-connect-installation-wizard.md) |Az Azure AD Connect telepítővarázsló ismételt futtatásakor ismerteti, hogy milyen lehetőségei vannak. |
| [A deklaratív kiépítés ismertetése](concept-azure-ad-connect-sync-declarative-provisioning.md) |A deklaratív kiépítésnek nevezett konfigurációs modell ismert. |
| [A deklaratív üzembe helyezési kifejezések ismertetése](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) |A deklaratív kiépítéshez használt kifejezésnyelv szintaxisát ismerteti. |
| [Az alapértelmezett konfiguráció ismertetése](concept-azure-ad-connect-sync-default-configuration.md) |A beépített szabályok és az alapértelmezett konfiguráció ismertetése. Azt is ismerteti, hogyan működnek együtt a szabályok a beépített forgatókönyvek működéséhez. |
| [A felhasználók és kapcsolattartók ismertetése](concept-azure-ad-connect-sync-user-and-contacts.md) |Folytatja az előző témakört, és leírja, hogyan működik együtt a felhasználók és a kapcsolattartók konfigurációja, különösen többerdős környezetben. |
| [Az alapértelmezett konfiguráció módosítása](how-to-connect-sync-change-the-configuration.md) |Végigvezeti, hogyan lehet egy közös konfigurációs módosítás attribútum folyamatok. |
| [Az alapértelmezett konfiguráció módosításának ajánlott eljárásai](how-to-connect-sync-best-practices-changing-default-configuration.md) |Támogatási korlátozások és a beépített konfiguráció módosítása. |
| [A szűrés konfigurálása](how-to-connect-sync-configure-filtering.md) |Ismerteti a különböző lehetőségeket, hogyan korlátozhatja, hogy mely objektumok szinkronizálása az Azure AD-vel, és lépésről-lépésre, hogyan konfigurálhatja ezeket a beállításokat. |
| **Jellemzők és forgatókönyvek** | |
| [Véletlen törlések megakadályozása](how-to-connect-sync-feature-prevent-accidental-deletes.md) |A *véletlen törlés megakadályozása* szolgáltatás és konfigurálása. |
| [Scheduler](how-to-connect-sync-feature-scheduler.md) |A beépített ütemező ismertetése, amely adatokat importál, szinkronizál és exportál. |
| [Jelszókivonat-szinkronizálás megvalósítása](how-to-connect-password-hash-synchronization.md) |A jelszó-szinkronizálás működésének, megvalósításának, valamint a működésnek és a hibaelhárításnak a ismertetése. |
| [Eszközvisszaíró](how-to-connect-device-writeback.md) |Az eszközvisszaírás működése az Azure AD Connectben. |
| [Címtárbővítmények](how-to-connect-sync-feature-directory-extensions.md) |Bemutatja, hogyan bővítheti ki az Azure AD sémát a saját egyéni attribútumokkal. |
| [Office 365 PreferredDataLocation](how-to-connect-sync-feature-preferreddatalocation.md) |Ez a témakör azt ismerteti, hogy miként helyezheti el a felhasználó Office 365-erőforrásait a felhasználóval azonos régióban. |
| **Szinkronizálási szolgáltatás** | |
| [Az Azure AD Connect szinkronizálási szolgáltatás jellemzői](how-to-connect-syncservice-features.md) |A szinkronizálási szolgáltatásoldal ismertetése, valamint az Azure AD szinkronizálási beállításainak módosítása. |
| [Duplikált attribútumrugalmasság](how-to-connect-syncservice-duplicate-attribute-resiliency.md) |A **felhasználóPrincipalName** és **proxyAddresses** duplikált attribútumértékek rugalmasságának engedélyezését és használatát ismerteti. |
| **Műveletek és felhasználói felület** | |
| [Szinkronizálási szolgáltatáskezelő](how-to-connect-sync-service-manager-ui.md) |A szinkronizálási szolgáltatáskezelő felhasználói felületének ismertetése, beleértve [az Operations](how-to-connect-sync-service-manager-ui-operations.md), [Connectors](how-to-connect-sync-service-manager-ui-connectors.md), [Metaverse Designer](how-to-connect-sync-service-manager-ui-mvdesigner.md)és [Metaverse Search](how-to-connect-sync-service-manager-ui-mvsearch.md) lapokat. |
| [Működési feladatok és szempontok](how-to-connect-sync-operations.md) |Működési problémák, például a vészhelyreállítás ismertetése. |
| **kézikönyv...** | |
| [Az Azure AD-fiók alaphelyzetbe állítása](how-to-connect-azureadaccount.md) |Az Azure AD Connect-szinkronizálásés az Azure AD-hez való csatlakozáshoz használt szolgáltatásfiók hitelesítő adatainak alaphelyzetbe állítása. |
| **További információk és hivatkozások** | |
| [Portok](reference-connect-ports.md) |Felsorolja, hogy mely portokat kell megnyitnia a szinkronizálási motor és a helyszíni könyvtárak és az Azure AD között. |
| [Az Azure Active Directoryval szinkronizált attribútumok](reference-connect-sync-attributes-synchronized.md) |A helyszíni AD és az Azure AD között szinkronizált összes attribútumot felsorolja. |
| [Függvényreferencia](reference-connect-sync-functions-reference.md) |A deklaratív kiépítésben elérhető összes függvény felsorolása. |

## <a name="additional-resources"></a>További források
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
