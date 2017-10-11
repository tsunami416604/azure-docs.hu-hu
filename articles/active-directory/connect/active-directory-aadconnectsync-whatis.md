---
title: "Azure AD Connect szinkronizálása: megértéséhez, valamint a szinkronizálás testreszabása |} Microsoft Docs"
description: "Azt ismerteti, hogyan az Azure AD Connect szinkronizálása akkor működik, és hogyan szabható testre."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: ee4bf802-045b-4da0-986e-90aba2de58d6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2016
ms.author: markvi
ms.openlocfilehash: 4edac05325ad12596d982d113df0db7461124b12
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Azure AD Connect szinkronizálása: megértéséhez, valamint a szinkronizálás testreszabása
Az Azure Active Directory Connect szinkronizálási szolgáltatások (az Azure AD Connect szinkronizálása) az Azure AD Connect fő összetevője. Ez gondoskodik az identitás a helyszíni környezetben és az Azure AD közti adatszinkronizálást kapcsolatos műveleteket. Azure AD Connect szinkronizálása a követő DirSync, az Azure AD Sync és a Forefront Identity Manager az az Azure Active Directory Connector konfigurálva.

Ez a témakör a érhető **az Azure AD Connect szinkronizálási szolgáltatás** (más néven **szinkronizálási motor**), és felsorolja a hozzá kapcsolódó összes további témakörökre mutató hivatkozásokat tartalmaz. Az Azure AD Connect hivatkozások: [a helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).

A helyszíni két összetevőből áll: a szinkronizálási szolgáltatás **az Azure AD Connect szinkronizálási szolgáltatás** összetevő és a szolgáltatási oldalon, az Azure AD nevű **az Azure AD Connect szinkronizálási szolgáltatás**. A szolgáltatás nem a DirSync, az Azure AD Sync és a Azure AD Connect közös.

## <a name="azure-ad-connect-sync-topics"></a>Az Azure AD Connect szinkronizálási kapcsolatos témakörök
| Témakör | Azt ismerteti, és mikor olvasása |
| --- | --- |
| **Az Azure AD Connect-szinkronizálás – alapok** | |
| [Az architektúra ismertetése](active-directory-aadconnectsync-understanding-architecture.md) |Azok is, akik új, a szinkronizálási motoron, és szeretné megtanulni, architektúrájának és kifejezésekkel kapcsolatos. |
| [Technikai kulcsfogalmak](active-directory-aadconnectsync-technical-concepts.md) |A rövid verzió architektúra témakör és röviden a használt kifejezések magyarázatát. |
| [Azure AD Connect-topológiák](active-directory-aadconnect-topologies.md) |Ismerteti a különböző topológiával és forgatókönyvek a szinkronizálási motor támogatja. |
| **Egyéni konfiguráció** | |
| [A telepítővarázsló futtatása újra](active-directory-aadconnectsync-installation-wizard.md) |Ismerteti a beállítások az Azure AD Connect telepítővarázsló újra futtatásakor elérhető rendelkezik. |
| [Deklaratív kiépítés ismertetése](active-directory-aadconnectsync-understanding-declarative-provisioning.md) |A deklaratív kiépítés nevű konfigurációs modell ismerteti. |
| [Deklaratív kiépítés kifejezéseinek ismertetése](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) |A deklaratív kiépítés használt kifejezés nyelv szintaxisának ismerteti. |
| [Az alapértelmezett konfiguráció ismertetése](active-directory-aadconnectsync-understanding-default-configuration.md) |A out-of-box szabályokat és az alapértelmezett konfiguráció ismertetése Is ismerteti, hogyan szabályok működik együtt az out-of-box helyzetekben működéséhez. |
| [Felhasználók és névjegyek](active-directory-aadconnectsync-understanding-users-and-contacts.md) |Az előző témakör továbbra is fennáll, és bemutatja a felhasználók és névjegyek működését együtt, különösen Többerdős környezetben. |
| [Hogyan lehet módosítani az alapértelmezett konfiguráció](active-directory-aadconnectsync-change-the-configuration.md) |Végigvezeti egy közös konfigurációs módosítást attribútum viszonylatában módjáról. |
| [Az alapértelmezett konfiguráció módosításának ajánlott eljárásai](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) |Támogatás korlátai és a out-of-box konfiguráció módosítása. |
| [A szűrés konfigurálása](active-directory-aadconnectsync-configure-filtering.md) |Bemutatja, hogyan korlátozása az objektumokat folyamatban van a szinkronizált az Azure AD- és részletes ezek a beállítások konfigurálása. |
| **Szolgáltatások és forgatókönyvek** | |
| [Véletlen törlések megakadályozása](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) |Ismerteti a *véletlen törlések megakadályozása* szolgáltatásról és konfigurálását. |
| [Scheduler](active-directory-aadconnectsync-feature-scheduler.md) |A beépített ütemezési, amely importálása, szinkronizálását, és az adatok exportálása ismerteti. |
| [Jelszó-szinkronizálás megvalósítása](active-directory-aadconnectsync-implement-password-synchronization.md) |Jelszó-szinkronizálás, végrehajtásához, és ismerteti és hibaelhárításához. |
| [Eszközök visszaírásához.](active-directory-aadconnect-feature-device-writeback.md) |Az Azure AD Connectben eszközvisszaíró működését ismerteti. |
| [Címtárbővítmények](active-directory-aadconnectsync-feature-directory-extensions.md) |Ismerteti, hogyan lehet a saját egyéni attribútumokkal rendelkező Azure AD-séma kiterjesztése. |
| **A szinkronizálási szolgáltatás** | |
| [Az Azure AD Connect szinkronizálási szolgáltatások](active-directory-aadconnectsyncservice-features.md) |Ismerteti a szinkronizálási szolgáltatás és a szinkronizálási beállítások módosítása az Azure ad-ben. |
| [Ismétlődő attribútum rugalmasság](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) |Ismerteti, hogyan lehet engedélyezése és használata **userPrincipalName** és **proxyAddresses** ismétlődő attribútum-érték rugalmasság. |
| **Műveletek és a felhasználói felület** | |
| [Szinkronizálási szolgáltatáskezelő](active-directory-aadconnectsync-service-manager-ui.md) |Ismerteti a Synchronization Service Manager felhasználói felületén, beleértve a [műveletek](active-directory-aadconnectsync-service-manager-ui-operations.md), [összekötők](active-directory-aadconnectsync-service-manager-ui-connectors.md), [Metaverzumtervező](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md), és [Metaverzum-keresés](active-directory-aadconnectsync-service-manager-ui-mvsearch.md) lapokon. |
| [Operatív feladatok és szempontok](active-directory-aadconnectsync-operations.md) |Fontos információk, például a vész-helyreállítási ismerteti. |
| **kézikönyv...** | |
| [Az Azure AD-fiókot alaphelyzetbe állítása](active-directory-aadconnectsync-howto-azureadaccount.md) |Hogyan adható meg a szolgáltatás fiók keresztül kapcsolódnak az Azure AD Connect szinkronizálása az Azure AD hitelesítő adatait. |
| **További információk és segédanyagok** | |
| [Portok](active-directory-aadconnect-ports.md) |Meg kell nyitnia a szinkronizálási motor és a helyszíni címtárak és az Azure AD között portokat sorolja fel. |
| [Az Azure Active Directoryval szinkronizált attribútumok](active-directory-aadconnectsync-attributes-synchronized.md) |Felsorolja az összes attribútumok szinkronizálása között a helyszíni AD és az Azure AD. |
| [Függvényreferencia](active-directory-aadconnectsync-functions-reference.md) |Felsorolja az összes elérhető deklaratív kiépítés funkciók. |

## <a name="additional-resources"></a>További források
* [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md)

