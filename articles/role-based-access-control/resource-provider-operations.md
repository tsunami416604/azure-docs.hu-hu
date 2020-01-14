---
title: Erőforrás-szolgáltatói műveletek Azure Resource Manager | Microsoft Docs
description: A Microsoft Azure Resource Manager erőforrás-szolgáltatók számára elérhető műveletek listája
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9d4b4134fa26fd2cb904a862ac16544873bf8bcb
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934464"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Erőforrás-szolgáltatói műveletek Azure Resource Manager

Ez a cikk felsorolja az egyes Azure Resource Manager erőforrás-szolgáltatók számára elérhető műveleteket. Ezek a műveletek [Egyéni szerepkörökben](custom-roles.md) használhatók, amelyek részletes [szerepköralapú hozzáférés-vezérlést (RBAC)](overview.md) biztosítanak az Azure-beli erőforrásokhoz. A műveleti karakterláncok formátuma a következő: `{Company}.{ProviderName}/{resourceType}/{action}`. Az erőforrás-szolgáltatói névterek Azure-szolgáltatásokhoz való leképezésének listáját lásd: az [erőforrás-szolgáltató egyeztetése a szolgáltatással](../azure-resource-manager/azure-services-resource-providers.md).

Az erőforrás-szolgáltatói műveletek folyamatosan fejlődnek. A legújabb műveletek beszerzéséhez használja a [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) vagy [az az Provider Operation List műveletet](/cli/azure/provider/operation#az-provider-operation-list).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Microsoft. HRE

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. HRE/domainServices/delete | Tartományi szolgáltatás törlése |
> | Műveletek | Microsoft. HRE/domainServices/oucontainer/delete | Ou-tároló törlése |
> | Műveletek | Microsoft. HRE/domainServices/oucontainer/READ | Ou-tárolók olvasása |
> | Műveletek | Microsoft. HRE/domainServices/oucontainer/Write | Ou-tároló írása |
> | Műveletek | Microsoft. HRE/domainServices/READ | Tartományi szolgáltatások olvasása |
> | Műveletek | Microsoft. HRE/domainServices/Write | Tartományi szolgáltatás írása |
> | Műveletek | Microsoft. HRE/Locations/operationresults/READ |  |
> | Műveletek | Microsoft. HRE/Operations/READ |  |
> | Műveletek | Microsoft. HRE/regisztráció/művelet | Tartományi szolgáltatás regisztrálása |
> | Műveletek | Microsoft. HRE/regisztráció/művelet | Tartományi szolgáltatás regisztrációjának törlése |

## <a name="microsoftaadiam"></a>Microsoft. aadiam

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. aadiam/diagnosticsettings/delete | Deleting a diagnostic setting |
> | Műveletek | Microsoft. aadiam/diagnosticsettings/READ | Reading a diagnostic setting |
> | Műveletek | Microsoft. aadiam/diagnosticsettings/Write | Writing a diagnostic setting |
> | Műveletek | Microsoft. aadiam/diagnosticsettingscategories/READ | Reading a diagnostic setting categories |
> | Műveletek | Microsoft. aadiam/metricDefinitions/READ | Bérlői szintű metrika definícióinak olvasása |
> | Műveletek | Microsoft. aadiam/mérőszámok/olvasás | Bérlői szintű metrikák olvasása |

## <a name="microsoftaddons"></a>Microsoft. addons

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. addons/műveletek/olvasás | A támogatott RP-műveletek beolvasása. |
> | Műveletek | Microsoft. addons/regisztráció/művelet | A megadott előfizetés regisztrálása a Microsoft. addons szolgáltatásban |
> | Műveletek | Microsoft. addons/supportProviders/listsupportplaninfo/művelet | A megadott előfizetéshez tartozó aktuális támogatási csomag információit sorolja fel. |
> | Műveletek | Microsoft. addons/supportProviders/supportPlanTypes/delete | Eltávolítja a megadott Canonical-támogatási csomagot. |
> | Műveletek | Microsoft. addons/supportProviders/supportPlanTypes/READ | A megadott Canonical támogatási csomag állapotának beolvasása. |
> | Műveletek | Microsoft. addons/supportProviders/supportPlanTypes/Write | Hozzáadja a megadott Canonical-támogatási csomag típusát. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. ADHybridHealthService/addsservices/művelet | Hozzon létre egy új erdőt a bérlő számára. |
> | Műveletek | Microsoft. ADHybridHealthService/addsservices/addomainservicemembers/READ | A megadott szolgáltatásnév összes kiszolgálójának beolvasása. |
> | Műveletek | Microsoft. ADHybridHealthService/addsservices/riasztások/olvasás | Riasztások részleteinek beolvasása az erdőhöz, például a alertid, a riasztás dátuma, a riasztás legutóbbi észlelése, a riasztás leírása, a legutóbbi frissítés, a riasztási szint, a riasztási állapot, a riasztások hibaelhárítási hivatkozásai |
> | Műveletek | Microsoft. ADHybridHealthService/addsservices/konfiguráció/olvasás | Beolvassa az erdő szolgáltatás-konfigurációját. Példa: erdő neve, működési szint, tartománynév-kiosztási főkiszolgáló FSMO-szerepkör, séma-főkiszolgáló FSMO-szerepköre stb. |
> | Műveletek | Microsoft. ADHybridHealthService/addsservices/delete | Töröl egy szolgáltatást, és az állapottal együtt a kiszolgálókat is. |
> | Műveletek | Microsoft. ADHybridHealthService/addsservices/dimensions/READ | Lekéri az erdő tartományait és helyeit. Példa: állapotadatok, aktív riasztások, megoldott riasztások, tulajdonságok, például a tartomány működési szintje, erdő, infrastruktúra-főkiszolgáló, elsődleges tartományvezérlő, RID-főkiszolgáló stb.  |
> | Műveletek | Microsoft. ADHybridHealthService/addsservices/features/UserPreference/READ | Beolvassa az erdő felhasználói beállításait.<br>Példa – MetricCounterName, például ldapsuccessfulbinds, ntlmauthentications, kerberosauthentications, addsinsightsagentprivatebytes, ldapsearches.<br>A felhasználói felületi diagramok beállításai stb. |
> | Műveletek | Microsoft. ADHybridHealthService/addsservices/forestsummary/READ | Beolvassa az erdő összefoglalását az adott erdőhöz, például az erdő nevét, az erdő alá tartozó tartományok számát, a helyek és a helyek részleteit stb. |
> | Műveletek | Microsoft. ADHybridHealthService/addsservices/metricmetadata/READ | Egy adott szolgáltatáshoz tartozó támogatott metrikák listájának beolvasása.<br>Ilyenek például az extranetes fiókzárolási szolgáltatások, a sikertelen kérelmek teljes száma, a függő jogkivonat-kérelmek (proxy), a jogkivonat-kérelmek előállított/másodperc stb.<br>NTLM-hitelesítés/mp, LDAP sikeres kötések/mp, LDAP-kötési idő, LDAP aktív szálak, Kerberos-hitelesítés/mp, ATQ szálak összesen etc a ADDomainService.<br>Futtassa a profil késését, a TCP-kapcsolatok létrehozását, az adatellenőrzési ügynök saját bájtjait, a statisztikát az Azure AD-be a ADSync szolgáltatáshoz. |
> | Műveletek | Microsoft. ADHybridHealthService/addsservices/metrikák/csoportok/olvasás | Ez az API a metrikák adatait kapja meg.<br>Ez az API például a következőhöz kapcsolódó információk beszerzésére használható: extranetes fiókzárolás, összes sikertelen kérelem, függő jogkivonat-kérés (proxy), jogkivonat-kérelmek előállított/másodperc stb. a ADFederation szolgáltatáshoz.<br>NTLM-hitelesítés/mp, LDAP sikeres kötések/mp, LDAP-kötési idő, LDAP aktív szálak, Kerberos-hitelesítés/mp, ATQ-szálak összesen etc a ADDomain szolgáltatáshoz.<br>Futtassa a profil késését, a TCP-kapcsolatok létrejöttét, az adatellenőrzési ügynök saját bájtjait, az Azure AD for Sync szolgáltatáshoz való exportálási statisztikát. |
> | Műveletek | Microsoft. ADHybridHealthService/addsservices/premiumcheck/READ | Ez az API beolvassa a prémium bérlő összes bekészített ADDomainServices listáját. |
> | Műveletek | Microsoft. ADHybridHealthService/addsservices/READ | A megadott szolgáltatásnév szolgáltatás részleteinek beolvasása. |
> | Műveletek | Microsoft. ADHybridHealthService/addsservices/replicationdetails/READ | Lekérdezi a megadott szolgáltatásnév összes kiszolgálójának replikálási részleteit. |
> | Műveletek | Microsoft. ADHybridHealthService/addsservices/replicationstatus/READ | Lekéri a tartományvezérlők számát és a replikálási hibákat, ha vannak ilyenek. |
> | Műveletek | Microsoft. ADHybridHealthService/addsservices/replicationsummary/READ | Beolvassa a tartományvezérlők teljes listáját, valamint az adott erdő replikációs részleteit. |
> | Műveletek | Microsoft. ADHybridHealthService/addsservices/Servicemembers/művelet | Adja hozzá a kiszolgálói példányt a szolgáltatáshoz. |
> | Műveletek | Microsoft. ADHybridHealthService/addsservices/Servicemembers/hitelesítő adatok/olvasás | A ADDomainService kiszolgáló regisztrációja során ezt az API-t hívja meg az új kiszolgálók bevezetéséhez szükséges hitelesítő adatok beszerzéséhez. |
> | Műveletek | Microsoft. ADHybridHealthService/addsservices/Servicemembers/delete | Egy adott szolgáltatás és bérlő kiszolgálójának törlése. |
> | Műveletek | Microsoft. ADHybridHealthService/addsservices/Write | Létrehozza vagy frissíti a bérlő ADDomainService-példányát. |
> | Műveletek | Microsoft. ADHybridHealthService/konfiguráció/művelet | A bérlő konfigurációjának frissítése. |
> | Műveletek | Microsoft. ADHybridHealthService/konfiguráció/olvasás | A bérlő konfigurációjának beolvasása. |
> | Műveletek | Microsoft. ADHybridHealthService/konfiguráció/írás | Létrehoz egy bérlői konfigurációt. |
> | Műveletek | Microsoft. ADHybridHealthService/naplók/tartalom/olvasás | A blobban tárolt ügynök-telepítési és regisztrációs naplók tartalmának beolvasása. |
> | Műveletek | Microsoft. ADHybridHealthService/naplók/olvasás | Beolvassa az ügynök telepítési és regisztrációs naplóit a bérlő számára. |
> | Műveletek | Microsoft. ADHybridHealthService/Operations/READ | Lekéri a rendszer által támogatott műveletek listáját. |
> | Műveletek | Microsoft. ADHybridHealthService/regisztráció/művelet | Regisztrálja a ADHybrid Állapotfigyelő szolgáltatás erőforrás-szolgáltatót, és lehetővé teszi a ADHybrid Állapotfigyelő szolgáltatás erőforrás létrehozását. |
> | Műveletek | Microsoft. ADHybridHealthService/jelentések/availabledeployments/READ | Lekéri az elérhető régiók listáját, amelyet a DevOps az ügyfél-incidensek támogatásához használ. |
> | Műveletek | Microsoft. ADHybridHealthService/jelentések/badpassword/READ | Lekérdezi a helytelen jelszó-kísérletek listáját Active Directory összevonási szolgáltatás összes felhasználója számára. |
> | Műveletek | Microsoft. ADHybridHealthService/jelentések/badpassworduseridipfrequency/READ | Beolvassa a blob SAS URI-t, amely tartalmazza az állapotot, és az újonnan várólistán lévő jelentési feladatának végleges eredményét az adott bérlő számára napi Felhasználónév/jelszó/IP-cím/felhasználóazonosító gyakorisága alapján. |
> | Műveletek | Microsoft. ADHybridHealthService/jelentések/consentedtodevopstenants/READ | Beolvassa a DevOps által beleegyezett bérlők listáját. Jellemzően ügyfélszolgálati támogatáshoz használatos. |
> | Műveletek | Microsoft. ADHybridHealthService/jelentések/isdevops/READ | Egy érték beolvasása, amely azt jelzi, hogy a bérlő DevOps-e, vagy sem. |
> | Műveletek | Microsoft. ADHybridHealthService/jelentések/selectdevopstenant/READ | A kiválasztott fejlesztői Ops-bérlőhöz tartozó userid (ObjectId) frissítése. |
> | Műveletek | Microsoft. ADHybridHealthService/jelentések/selecteddeployment/READ | Beolvassa az adott bérlő kiválasztott központi telepítését. |
> | Műveletek | Microsoft. ADHybridHealthService/jelentések/tenantassigneddeployment/READ | A bérlői azonosító beolvasása után a bérlői tároló helye lesz. |
> | Műveletek | Microsoft. ADHybridHealthService/jelentések/updateselecteddeployment/READ | Lekéri a földrajzi helyet, amelyről az adatok hozzáférnek. |
> | Műveletek | Microsoft. ADHybridHealthService/szolgáltatások/művelet | Egy szolgáltatási példány frissítése a bérlőben. |
> | Műveletek | Microsoft. ADHybridHealthService/szolgáltatások/riasztások/olvasás | A szolgáltatáshoz tartozó riasztások beolvasása. |
> | Műveletek | Microsoft. ADHybridHealthService/szolgáltatások/riasztások/olvasás | A szolgáltatáshoz tartozó riasztások beolvasása. |
> | Műveletek | Microsoft. ADHybridHealthService/Services/checkservicefeatureavailibility/READ | A szolgáltatás neve alapján ellenőrizhető, hogy egy szolgáltatásnak van-e minden szükséges funkciója a szolgáltatás használatához. |
> | Műveletek | Microsoft. ADHybridHealthService/Services/delete | Töröl egy szolgáltatási példányt a bérlőben. |
> | Műveletek | Microsoft. ADHybridHealthService/Services/exporterrors/READ | Lekéri egy adott szinkronizálási szolgáltatás exportálási hibáit. |
> | Műveletek | Microsoft. ADHybridHealthService/Services/exportstatus/READ | Egy adott szolgáltatás exportálási állapotának beolvasása. |
> | Műveletek | Microsoft. ADHybridHealthService/szolgáltatások/feedbacktype/visszajelzés/olvasás | Riasztási visszajelzés küldése egy adott szolgáltatásról és kiszolgálóról. |
> | Műveletek | Microsoft. ADHybridHealthService/Services/metricmetadata/READ | Egy adott szolgáltatáshoz tartozó támogatott metrikák listájának beolvasása.<br>Ilyenek például az extranetes fiókzárolási szolgáltatások, a sikertelen kérelmek teljes száma, a függő jogkivonat-kérelmek (proxy), a jogkivonat-kérelmek előállított/másodperc stb.<br>NTLM-hitelesítés/mp, LDAP sikeres kötések/mp, LDAP-kötési idő, LDAP aktív szálak, Kerberos-hitelesítés/mp, ATQ szálak összesen etc a ADDomainService.<br>Futtassa a profil késését, a TCP-kapcsolatok létrehozását, az adatellenőrzési ügynök saját bájtjait, a statisztikát az Azure AD-be a ADSync szolgáltatáshoz. |
> | Műveletek | Microsoft. ADHybridHealthService/szolgáltatások/mérőszámok/csoportok/átlag/olvasás | Adott szolgáltatás esetében ez az API egy adott szolgáltatás metrikáinak átlagát kapja meg.<br>Ez az API például a következőhöz kapcsolódó információk beszerzésére használható: extranetes fiókzárolás, összes sikertelen kérelem, függő jogkivonat-kérés (proxy), jogkivonat-kérelmek előállított/másodperc stb. a ADFederation szolgáltatáshoz.<br>NTLM-hitelesítés/mp, LDAP sikeres kötések/mp, LDAP-kötési idő, LDAP aktív szálak, Kerberos-hitelesítés/mp, ATQ-szálak összesen etc a ADDomain szolgáltatáshoz.<br>Futtassa a profil késését, a TCP-kapcsolatok létrejöttét, az adatellenőrzési ügynök saját bájtjait, az Azure AD for Sync szolgáltatáshoz való exportálási statisztikát. |
> | Műveletek | Microsoft. ADHybridHealthService/szolgáltatások/mérőszámok/csoportok/olvasás | Ez az API a metrikák adatait kapja meg.<br>Ez az API például a következőhöz kapcsolódó információk beszerzésére használható: extranetes fiókzárolás, összes sikertelen kérelem, függő jogkivonat-kérés (proxy), jogkivonat-kérelmek előállított/másodperc stb. a ADFederation szolgáltatáshoz.<br>NTLM-hitelesítés/mp, LDAP sikeres kötések/mp, LDAP-kötési idő, LDAP aktív szálak, Kerberos-hitelesítés/mp, ATQ-szálak összesen etc a ADDomain szolgáltatáshoz.<br>Futtassa a profil késését, a TCP-kapcsolatok létrejöttét, az adatellenőrzési ügynök saját bájtjait, az Azure AD for Sync szolgáltatáshoz való exportálási statisztikát. |
> | Műveletek | Microsoft. ADHybridHealthService/szolgáltatások/mérőszámok/csoportok/összeg/olvasás | Adott szolgáltatás esetén ez az API egy adott szolgáltatás metrikáinak összesített nézetét kapja meg.<br>Ez az API például a következőhöz kapcsolódó információk beszerzésére használható: extranetes fiókzárolás, összes sikertelen kérelem, függő jogkivonat-kérés (proxy), jogkivonat-kérelmek előállított/másodperc stb. a ADFederation szolgáltatáshoz.<br>NTLM-hitelesítés/mp, LDAP sikeres kötések/mp, LDAP-kötési idő, LDAP aktív szálak, Kerberos-hitelesítés/mp, ATQ-szálak összesen etc a ADDomain szolgáltatáshoz.<br>Futtassa a profil késését, a TCP-kapcsolatok létrejöttét, az adatellenőrzési ügynök saját bájtjait, az Azure AD for Sync szolgáltatáshoz való exportálási statisztikát. |
> | Műveletek | Microsoft. ADHybridHealthService/szolgáltatások/monitoringconfiguration/írás | Szolgáltatás figyelési konfigurációjának hozzáadása vagy frissítése. |
> | Műveletek | Microsoft. ADHybridHealthService/Services/monitoringconfigurations/READ | Lekéri egy adott szolgáltatás figyelési konfigurációit. |
> | Műveletek | Microsoft. ADHybridHealthService/szolgáltatások/monitoringconfigurations/írás | Szolgáltatás figyelési konfigurációinak hozzáadása vagy frissítése. |
> | Műveletek | Microsoft. ADHybridHealthService/Services/premiumcheck/READ | Ez az API beolvassa a prémium szintű bérlőhöz tartozó összes beépített szolgáltatás listáját. |
> | Műveletek | Microsoft. ADHybridHealthService/szolgáltatások/olvasás | A bérlőben lévő szolgáltatási példányok beolvasása. |
> | Műveletek | Microsoft. ADHybridHealthService/szolgáltatások/jelentések/blobUris/olvasás | Minden kockázatos IP-jelentés URI-azonosítójának beolvasása az elmúlt 7 napban. |
> | Műveletek | Microsoft. ADHybridHealthService/szolgáltatások/jelentések/részletek/olvasás | Az elmúlt 7 nap helytelen jelszavas hibával rendelkező leggyakoribb 50-felhasználók jelentésének beolvasása |
> | Műveletek | Microsoft. ADHybridHealthService/szolgáltatások/jelentések/generateBlobUri/művelet | Kockázatos IP-jelentést generál, és visszaadja a rá mutató URI-t. |
> | Műveletek | Microsoft. ADHybridHealthService/szolgáltatások/Servicemembers/művelet | Létrehoz egy kiszolgálói példányt a szolgáltatásban. |
> | Műveletek | Microsoft. ADHybridHealthService/szolgáltatások/Servicemembers/riasztások/olvasás | Egy kiszolgáló értesítéseinek beolvasása. |
> | Műveletek | Microsoft. ADHybridHealthService/szolgáltatások/Servicemembers/hitelesítő adatok/olvasás | A kiszolgáló regisztrálása során a rendszer ezt az API-t hívja meg az új kiszolgálók bevezetéséhez szükséges hitelesítő adatok beszerzéséhez. |
> | Műveletek | Microsoft. ADHybridHealthService/Services/Servicemembers/datafreshness/READ | Az adott kiszolgáló esetében ez az API lekéri a kiszolgálók által feltöltött adattípusok listáját, valamint az egyes feltöltések legkésőbbi idejét. |
> | Műveletek | Microsoft. ADHybridHealthService/Services/Servicemembers/delete | Töröl egy kiszolgálói példányt a szolgáltatásban. |
> | Műveletek | Microsoft. ADHybridHealthService/Services/Servicemembers/exportstatus/READ | Beolvassa a szinkronizálási exportálási hiba részleteit egy adott szinkronizálási szolgáltatáshoz. |
> | Műveletek | Microsoft. ADHybridHealthService/szolgáltatások/Servicemembers/metrikák/csoportok/olvasás | Ez az API a metrikák adatait kapja meg.<br>Ez az API például a következőhöz kapcsolódó információk beszerzésére használható: extranetes fiókzárolás, összes sikertelen kérelem, függő jogkivonat-kérés (proxy), jogkivonat-kérelmek előállított/másodperc stb. a ADFederation szolgáltatáshoz.<br>NTLM-hitelesítés/mp, LDAP sikeres kötések/mp, LDAP-kötési idő, LDAP aktív szálak, Kerberos-hitelesítés/mp, ATQ-szálak összesen etc a ADDomain szolgáltatáshoz.<br>Futtassa a profil késését, a TCP-kapcsolatok létrejöttét, az adatellenőrzési ügynök saját bájtjait, az Azure AD for Sync szolgáltatáshoz való exportálási statisztikát. |
> | Műveletek | Microsoft. ADHybridHealthService/szolgáltatások/Servicemembers/mérőszámok/olvasás | Lekérdezi az összekötők listáját, és futtatja a profil nevét az adott szolgáltatás és szolgáltatás tagja számára. |
> | Műveletek | Microsoft. ADHybridHealthService/Services/Servicemembers/READ | A szolgáltatásban található kiszolgálópéldány beolvasása. |
> | Műveletek | Microsoft. ADHybridHealthService/Services/Servicemembers/serviceconfiguration/READ | Egy adott bérlő szolgáltatás-konfigurációjának beolvasása. |
> | Műveletek | Microsoft. ADHybridHealthService/Services/tenantwhitelisting/READ | Egy adott bérlő szolgáltatás-engedélyezési állapotának beolvasása. |
> | Műveletek | Microsoft. ADHybridHealthService/szolgáltatások/írás | Létrehoz egy szolgáltatási példányt a bérlőben. |
> | Műveletek | Microsoft. ADHybridHealthService/regisztráció/művelet | A ADHybrid Állapotfigyelő szolgáltatás erőforrás-szolgáltatóhoz tartozó előfizetés regisztrációjának törlése. |

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Advisor/konfigurációk/olvasás | Konfigurációk beolvasása |
> | Műveletek | Microsoft. Advisor/konfigurációk/írás | Konfiguráció létrehozása/frissítése |
> | Műveletek | Microsoft. Advisor/generateRecommendations/művelet | Javaslatok állapotának előállítása |
> | Műveletek | Microsoft. Advisor/generateRecommendations/READ | Javaslatok állapotának előállítása |
> | Műveletek | Microsoft. Advisor/metaadatok/olvasás | Metaadatok beolvasása |
> | Műveletek | Microsoft. Advisor/Operations/READ | Lekéri a Microsoft Advisor műveleteit |
> | Műveletek | Microsoft. Advisor/javaslatok/elérhető/művelet | Új javaslat érhető el a Microsoft Advisor szolgáltatásban |
> | Műveletek | Microsoft. Advisor/javaslatok/olvasás | Olvasási javaslatok |
> | Műveletek | Microsoft. Advisor/javaslatok/tiltások/törlés | Törlés mellőzése |
> | Műveletek | Microsoft. Advisor/javaslatok/mellőzések/olvasás | Letiltások beolvasása |
> | Műveletek | Microsoft. Advisor/javaslatok/mellőzések/írás | Eltávolítások létrehozása/frissítése |
> | Műveletek | Microsoft. Advisor/regisztráció/művelet | Regisztrálja a Microsoft Advisor előfizetését |
> | Műveletek | Microsoft. Advisor/tiltások/törlés | Törlés mellőzése |
> | Műveletek | Microsoft. Advisor/mellőzések/olvasás | Letiltások beolvasása |
> | Műveletek | Microsoft. Advisor/Mellőzés/írás | Eltávolítások létrehozása/frissítése |
> | Műveletek | Microsoft. Advisor/regisztráció/művelet | A Microsoft Advisor-előfizetés regisztrációjának törlése |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. AlertsManagement/actionRules/delete | Művelet szabályának törlése egy adott előfizetésben. |
> | Műveletek | Microsoft. AlertsManagement/actionRules/READ | A bemeneti szűrőkhöz tartozó összes művelet szabályának beolvasása. |
> | Műveletek | Microsoft. AlertsManagement/actionRules/Write | Műveleti szabály létrehozása vagy frissítése egy adott előfizetésben |
> | Műveletek | Microsoft. AlertsManagement/riasztások/changestate/művelet | A riasztás állapotának módosítása |
> | Műveletek | Microsoft. AlertsManagement/riasztások/diagnosztika/olvasás | A riasztáshoz tartozó összes diagnosztika beolvasása |
> | Műveletek | Microsoft. AlertsManagement/riasztások/előzmények/olvasás | A riasztás előzményeinek beolvasása |
> | Műveletek | Microsoft. AlertsManagement/riasztások/olvasás | A bemeneti szűrők összes riasztásának beolvasása. |
> | Műveletek | Microsoft. AlertsManagement/alertsList/READ | A bemeneti szűrők összes riasztásának beolvasása az előfizetések között |
> | Műveletek | Microsoft. AlertsManagement/alertsMetaData/READ | Riasztások beolvasása a bemeneti paraméterhez. |
> | Műveletek | Microsoft. AlertsManagement/alertsSummary/READ | Riasztások összefoglalásának beolvasása |
> | Műveletek | Microsoft. AlertsManagement/alertsSummaryList/READ | Riasztások összegzésének lekérése az előfizetések között |
> | Műveletek | Microsoft. AlertsManagement/Operations/READ | A megadott műveletek beolvasása |
> | Műveletek | Microsoft. AlertsManagement/regisztráció/művelet | Regisztrálja az előfizetést a Microsoft riasztások kezeléséhez |
> | Műveletek | Microsoft. AlertsManagement/smartDetectorAlertRules/delete | Intelligens detektor riasztási szabályának törlése egy adott előfizetésben |
> | Műveletek | Microsoft. AlertsManagement/smartDetectorAlertRules/READ | Az intelligens detektor riasztási szabályainak beolvasása a bemeneti szűrőkhöz |
> | Műveletek | Microsoft. AlertsManagement/smartDetectorAlertRules/Write | Intelligens detektor riasztási szabályának létrehozása vagy frissítése egy adott előfizetésben |
> | Műveletek | Microsoft. AlertsManagement/smartGroups/changestate/művelet | Az intelligens csoport állapotának módosítása |
> | Műveletek | Microsoft. AlertsManagement/smartGroups/előzmények/olvasás | Az intelligens csoport előzményeinek beolvasása |
> | Műveletek | Microsoft. AlertsManagement/smartGroups/READ | A bemeneti szűrők összes intelligens csoportjának beolvasása |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. AnalysisServices/Locations/checkNameAvailability/Action | Ellenőrzi, hogy a megadott Analysis Server neve érvényes-e, és nincs-e használatban. |
> | Műveletek | Microsoft. AnalysisServices/Locations/operationresults/READ | Retrieves the information of the specified operation result. |
> | Műveletek | Microsoft. AnalysisServices/Locations/operationstatuses/READ | Retrieves the information of the specified operation status. |
> | Műveletek | Microsoft. AnalysisServices/Operations/READ | Retrieves the information of operations |
> | Műveletek | Microsoft. AnalysisServices/regisztráció/művelet | Regisztrálja Analysis Services erőforrás-szolgáltatót. |
> | Műveletek | Microsoft. AnalysisServices/kiszolgálók/törlés | Törli a Analysis Server. |
> | Műveletek | Microsoft. AnalysisServices/Servers/listGatewayStatus/Action | A kiszolgálóhoz társított átjáró állapotának listázása. |
> | Műveletek | Microsoft. AnalysisServices/kiszolgálók/olvasás | A megadott Analysis Server információinak beolvasása. |
> | Műveletek | Microsoft. AnalysisServices/kiszolgálók/folytatás/művelet | A Analysis Server folytatása. |
> | Műveletek | Microsoft. AnalysisServices/kiszolgálók/SKU/READ | A kiszolgáló rendelkezésre álló SKU-adatainak beolvasása |
> | Műveletek | Microsoft. AnalysisServices/kiszolgálók/felfüggesztés/művelet | Felfüggeszti a Analysis Server. |
> | Műveletek | Microsoft. AnalysisServices/kiszolgálók/írás | Létrehozza vagy frissíti a megadott Analysis Server. |
> | Műveletek | Microsoft. AnalysisServices/SKU/READ | Retrieves the information of Skus |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. ApiManagement/checkNameAvailability/READ | Ellenőrzi, hogy elérhető-e a megadott szolgáltatás neve |
> | Műveletek | Microsoft. ApiManagement/Operations/READ | A Microsoft. ApiManagement erőforráshoz elérhető összes API-művelet olvasása |
> | Műveletek | Microsoft. ApiManagement/regisztráció/művelet | Előfizetés regisztrálása a Microsoft. ApiManagement erőforrás-szolgáltatónál |
> | Műveletek | Microsoft. ApiManagement/jelentések/olvasás | Az időszakok, a földrajzi régió, a fejlesztők, a termékek, az API-k, a műveletek, az előfizetés és a byRequest által összesített jelentések beolvasása. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/API-k/törlés | Törli az API Management Service-példány megadott API-ját. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/API-k/diagnosztika/törlés | A megadott diagnosztika törlése egy API-ból. |
> | Műveletek | Microsoft. ApiManagement/Service/API-k/diagnosztika/olvasás | Egy API összes diagnosztikát listázza. vagy beolvassa az azonosító alapján megadott API-diagnosztika részleteit. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/API-k/diagnosztika/írás | Létrehoz egy új diagnosztikát egy API-hoz, vagy frissíti a meglévőt. vagy frissíti a diagnosztika részleteit az azonosító alapján megadott API-hoz. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/API-k/problémák/mellékletek/törlés | A megadott Megjegyzés törlése a hibából. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/API-k/problémák/mellékletek/olvasás | Felsorolja a megadott API-hoz tartozó probléma összes mellékletét. vagy beolvassa az azonosítóval megadott API-hoz tartozó melléklettel kapcsolatos adatokat. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/API-k/problémák/mellékletek/írás | Új mellékletet hoz létre a probléma egy API-ban vagy egy meglévő frissítése során. |
> | Műveletek | Microsoft. ApiManagement/Service/API-k/problémák/megjegyzések/törlés | A megadott Megjegyzés törlése a hibából. |
> | Műveletek | Microsoft. ApiManagement/Service/API-k/problémák/megjegyzések/olvasás | Felsorolja a megadott API-hoz kapcsolódó probléma összes megjegyzését. vagy beolvassa az azonosító alapján megadott API-hoz tartozó probléma megjegyzésének részleteit. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/API-k/problémák/megjegyzések/írás | Új Megjegyzés létrehozása a probléma számára egy API-ban vagy egy meglévő frissítése. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/API-k/problémák/törlés | Törli a megadott problémát egy API-ból. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/API-k/problémák/olvasás | Felsorolja a megadott API-hoz kapcsolódó összes problémát. vagy beolvassa a probléma részleteit az azonosító által meghatározott API-hoz. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/API-k/problémák/írás | Új probléma létrehozása egy API-hoz, vagy egy meglévő frissítése. vagy frissít egy meglévő problémát egy API-hoz. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/API-k/műveletek/törlés | Törli a megadott műveletet az API-ban. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/API-k/műveletek/házirendek/törlés | A házirend-konfiguráció törlése az API-művelettel. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/API-k/műveletek/házirendek/olvasás | A házirend-konfiguráció listájának beolvasása az API-művelet szintjén. vagy szerezze be a házirend-konfigurációt az API-művelet szintjén. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/API-k/műveletek/házirendek/írás | Létrehozza vagy frissíti az API-művelet szintjének házirend-konfigurációját. |
> | Műveletek | Microsoft. ApiManagement/Service/API-k/Operations/Policy/delete | A házirend-konfiguráció törlése a műveleti szinten |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/API-k/műveletek/házirend/olvasás | Házirend-konfiguráció beolvasása műveleti szinten |
> | Műveletek | Microsoft. ApiManagement/Service/API-k/Operations/Policy/Write | Házirend-konfiguráció létrehozása műveleti szinten |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/API-k/műveletek/olvasás | Felsorolja a megadott API-hoz tartozó műveletek gyűjteményét. vagy lekéri az azonosító alapján megadott API-művelet részleteit. |
> | Műveletek | Microsoft. ApiManagement/Service/API-k/Operations/Tags/delete | Válassza le a címkét a műveletből. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/API-k/műveletek/címkék/olvasás | Felsorolja a művelethez társított összes címkét. vagy a művelethez társított címke beolvasása. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/API-k/műveletek/címkék/írás | Címke társítása a művelethez. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/API-k/műveletek/írás | Létrehoz egy új műveletet az API-ban, vagy frissíti a meglévőt. vagy frissíti a művelet részleteit az azonosítójában megadott API-ban. |
> | Műveletek | Microsoft. ApiManagement/Service/API-k/operationsByTags/READ | A címkékkel társított műveletek gyűjteményét listázza. |
> | Műveletek | Microsoft. ApiManagement/Service/API-k/házirendek/törlés | Törli a házirend-konfigurációt az API-ban. |
> | Műveletek | Microsoft. ApiManagement/Service/API-k/házirendek/olvasás | A szabályzat konfigurációjának beolvasása az API szintjén. vagy szerezze be a szabályzat konfigurációját az API szintjén. |
> | Műveletek | Microsoft. ApiManagement/Service/API-k/szabályzatok/írás | Létrehozza vagy frissíti az API házirend-konfigurációját. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/API-k/házirend/törlés | A szabályzat konfigurációjának törlése API-szinten |
> | Műveletek | Microsoft. ApiManagement/Service/API-k/házirend/olvasás | A szabályzat konfigurációjának beolvasása API-szinten |
> | Műveletek | Microsoft. ApiManagement/Service/API-k/házirend/írás | Házirend-konfiguráció létrehozása API-szinten |
> | Műveletek | Microsoft. ApiManagement/Service/API-k/termékek/olvasás | Felsorolja az összes olyan terméket, amely az API részét képezi. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/API-k/olvasás | Felsorolja az API Management Service-példány összes API-jait. vagy lekéri az azonosító alapján megadott API részleteit. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/API-k/kiadások/törlés | Eltávolítja az API összes kiadását, vagy törli a megadott kiadást az API-ban. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/API-k/kiadások/olvasás | Az API összes kiadását listázza.<br>Egy API-kiadás jön létre, amikor egy API-változatot aktuálisan használ.<br>A kiadásokat a rendszer az előző változatokra történő visszaállításra is felhasználja.<br>Az eredmények lapozva lesznek, és a $top és a $skip paraméterek is korlátozva lesznek.<br>vagy egy API-kiadás részleteit adja vissza. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/API-k/kiadások/írás | Új kiadás létrehozása az API-hoz. vagy frissíti az azonosító alapján megadott API kiadásának részleteit. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/API-k/változatok/törlés | Egy API összes változatának eltávolítása |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/API-k/változatok/olvasás | Felsorolja az API összes változatát. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/API-k/sémák/törlés | Törli a séma konfigurációját az API-ban. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/API-k/sémák/olvasás | A séma konfigurációjának beolvasása az API szintjén. vagy a séma konfigurációjának beolvasása az API szintjén. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/API-k/sémák/írás | Létrehozza vagy frissíti az API sémájának konfigurációját. |
> | Műveletek | Microsoft. ApiManagement/Service/API-k/tagDescriptions/delete | Az API-címke leírásának törlése. |
> | Műveletek | Microsoft. ApiManagement/Service/API-k/tagDescriptions/READ | Az API hatókörében lévő összes címke leírása. A hencegés hasonló modell van definiálva az API-szinten, de a címke hozzárendelhető a műveletekhez, vagy lekérheti a címke leírását az API hatókörében lévő tagDescription. |
> | Műveletek | Microsoft. ApiManagement/Service/API-k/tagDescriptions/írás | Címke leírásának létrehozása/frissítése az API hatókörében. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/API-k/címkék/törlés | Válassza le a címkét az API-ból. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/API-k/címkék/olvasás | Az API-hoz társított összes címke listája. vagy az API-hoz társított címke beolvasása. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/API-k/címkék/írás | Címke társítása az API-hoz. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/API-k/írás | Új vagy frissíti a API Management Service-példány meglévő API-ját. vagy frissíti a API Management Service-példány megadott API-ját. |
> | Műveletek | Microsoft. ApiManagement/Service/apisByTags/READ | A címkékhez társított API-k gyűjteményét listázza. |
> | Műveletek | Microsoft. ApiManagement/Service/apiVersionSets/delete | Az adott API-verzió megadott készletének törlése. |
> | Műveletek | Microsoft. ApiManagement/Service/apiVersionSets/READ | Az API-verziók készleteit sorolja fel a megadott szolgáltatási példányban. vagy lekéri az azonosító alapján megadott API-verzió részleteit. |
> | Műveletek | Microsoft. ApiManagement/Service/apiVersionSets/Versions/READ | Verzió entitások listájának beolvasása |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/apiVersionSets/írás | Egy API-verzió készletének létrehozása vagy frissítése. vagy frissíti az azonosító alapján megadott API-VersionSet adatait. |
> | Műveletek | Microsoft. ApiManagement/Service/applynetworkconfigurationupdates/művelet | Frissíti a Virtual Network futó Microsoft. ApiManagement-erőforrásokat a frissített hálózati beállítások kiválasztásához. |
> | Műveletek | Microsoft. ApiManagement/Service/authorizationServers/delete | Az adott engedélyezési kiszolgáló példányának törlése. |
> | Műveletek | Microsoft. ApiManagement/Service/authorizationServers/listSecrets/művelet | Az engedélyezési kiszolgáló titkának beolvasása. |
> | Műveletek | Microsoft. ApiManagement/Service/authorizationServers/READ | A szolgáltatási példányon belül meghatározott engedélyezési kiszolgálók gyűjteményét listázza. vagy beolvassa az engedélyezési kiszolgáló titkos kódok nélküli részleteit. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/authorizationServers/írás | Új engedélyezési kiszolgáló létrehozása vagy egy meglévő engedélyezési kiszolgáló frissítése. vagy frissíti az azonosító alapján megadott engedélyezési kiszolgáló adatait. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/háttér/törlés | A megadott háttér törlése. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/háttér/olvasás | Felsorolja a megadott szolgáltatási példányban található háttérrendszer-gyűjteményeket. vagy lekéri az azonosító alapján megadott háttér részleteit. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/háttérrendszer/újracsatlakozási/művelet | Értesíti a APIM proxyt, hogy a megadott időkorlát után hozzon létre egy új kapcsolódást a háttérrendszer számára. Ha nincs időkorlát megadva, a rendszer 2 perces időkorlátot használ. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/háttér/írás | Egy háttérrendszer létrehozása vagy frissítése. vagy frissít egy meglévő hátteret. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/biztonsági mentés/művelet | API Management szolgáltatás biztonsági mentése a megadott tárolóba egy felhasználó által megadott Storage-fiókban |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/gyorsítótárak/törlés | Adott gyorsítótár törlése. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/gyorsítótárak/olvasás | Felsorolja a megadott szolgáltatási példány összes külső gyorsítótárának gyűjteményét. vagy lekéri az azonosító alapján megadott gyorsítótár részleteit. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/gyorsítótárak/írás | Létrehoz vagy frissít egy külső gyorsítótárat az API Management-példányban való használatra. vagy frissíti az azonosító alapján megadott gyorsítótár részleteit. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/tanúsítványok/törlés | Adott tanúsítvány törlése. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/tanúsítványok/olvasás | Felsorolja a megadott szolgáltatási példányban található összes tanúsítvány gyűjteményét. vagy lekéri az azonosító alapján megadott tanúsítvány részleteit. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/tanúsítványok/írás | Létrehozza vagy frissíti a háttérrel való hitelesítéshez használt tanúsítványt. |
> | Műveletek | Microsoft. ApiManagement/Service/contentTypes/contentItems/delete | Eltávolítja a megadott tartalmi elemeket. |
> | Műveletek | Microsoft. ApiManagement/Service/contentTypes/contentItems/READ | A tartalmi elemek listáját vagy a tartalom elem részleteit adja vissza. |
> | Műveletek | Microsoft. ApiManagement/Service/contentTypes/contentItems/írás | Új tartalmi elemek létrehozása vagy a megadott tartalmi elemek frissítése |
> | Műveletek | Microsoft. ApiManagement/Service/contentTypes/READ | A tartalomtípusok listáját adja vissza. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/törlés | API Management szolgáltatás példányának törlése |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/diagnosztika/törlés | Törli a megadott diagnosztikát. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/diagnosztika/olvasás | Felsorolja az API Management Service-példány összes diagnosztikát. vagy lekéri az azonosító alapján megadott diagnosztika részleteit. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/diagnosztika/írás | Létrehoz egy új diagnosztikát, vagy frissíti a meglévőt. vagy frissíti az azonosító alapján megadott diagnosztika részleteit. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/átjárók/művelet | Az átjáró konfigurációjának beolvasása. vagy frissíti az átjáró szívverését. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/átjárók/API-k/törlés | Törli a megadott API-t a megadott átjáróról. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/átjárók/API-k/olvasás | Az átjáróhoz társított API-k gyűjteményét listázza. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/átjárók/API-k/írás | Egy API-t ad hozzá a megadott átjáróhoz. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/átjárók/törlés | Adott átjáró törlése. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/átjárók/hostnameConfigurations/olvasás | Felsorolja a megadott átjáró állomásnév-konfigurációinak gyűjteményét. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/átjárók/kulcsok/művelet | Az átjáró kulcsainak beolvasása. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/átjárók/olvasás | A Service instance szolgáltatásban regisztrált átjárók gyűjteményét listázza. vagy beolvassa az azonosító által megadott átjáró részleteit. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/átjárók/regeneratePrimaryKey/művelet | Újragenerálta az elsődleges átjáró kulcsát a invalidationg létrehozott jogkivonatokkal. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/átjárók/regenerateSecondaryKey/művelet | Újragenerálja a másodlagos átjáró kulcsát a vele létrehozott jogkivonatok invalidationg. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/átjárók/jogkivonat/művelet | Az átjáróhoz tartozó megosztott hozzáférés engedélyezési jogkivonatának beolvasása. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/átjárók/írás | Létrehoz vagy frissít egy átjárót az API Management-példányban való használatra. vagy frissíti az azonosító alapján megadott átjáró részleteit. |
> | Műveletek | Microsoft. ApiManagement/Service/getssotoken/művelet | Beolvassa az SSO-tokent, amely bejelentkezhet API Management szolgáltatásbeli örökölt portálra rendszergazdaként |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/csoportok/törlés | Törli a API Management szolgáltatási példány adott csoportját. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/csoportok/olvasás | A szolgáltatási példányon belül definiált csoportok gyűjteményét listázza. vagy lekéri az azonosító alapján megadott csoport részleteit. |
> | Műveletek | Microsoft. ApiManagement/Service/groups/Users/delete | Meglévő felhasználó eltávolítása meglévő csoportból. |
> | Műveletek | Microsoft. ApiManagement/Service/groups/Users/READ | A csoporthoz társított felhasználói entitások gyűjteményét listázza. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/csoportok/felhasználók/írás | Meglévő felhasználó hozzáadása meglévő csoporthoz |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/csoportok/írás | Létrehoz vagy frissít egy csoportot. vagy frissíti az azonosító alapján megadott csoport részleteit. |
> | Műveletek | Microsoft. ApiManagement/Service/identityProviders/delete | Törli a megadott identitás-szolgáltatói konfigurációt. |
> | Műveletek | Microsoft. ApiManagement/Service/identityProviders/listSecrets/művelet | Identitás-szolgáltatói titkok beolvasása. |
> | Műveletek | Microsoft. ApiManagement/Service/identityProviders/READ | Felsorolja a megadott szolgáltatási példányban konfigurált identitás-szolgáltató gyűjteményét. vagy beolvassa az identitás-szolgáltató konfigurációs adatait titkos kódok nélkül. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/identityProviders/írás | Létrehozza vagy frissíti a IdentityProvider-konfigurációt. vagy frissít egy meglévő IdentityProvider-konfigurációt. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/problémák/olvasás | Felsorolja a megadott szolgáltatási példányban felmerülő problémák gyűjteményét. vagy API Management probléma részleteinek beolvasása |
> | Műveletek | Microsoft. ApiManagement/Service/Locations/networkstatus/READ | Lekéri azon erőforrások hálózati hozzáférési állapotát, amelyeken a szolgáltatás a helytől függ. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/Tuskózó/delete | A megadott naplózó törlése. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/naplózók/olvasás | A megadott szolgáltatási példányban lévő naplózók gyűjteményét listázza. vagy beolvassa az azonosító alapján megadott naplózó részleteit. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/naplózók/írás | Egy naplózó létrehozása vagy frissítése. vagy frissít egy meglévő naplózó. |
> | Műveletek | Microsoft. ApiManagement/Service/managedeployments/művelet | SKU/egységek módosítása, API Management szolgáltatás regionális központi telepítésének hozzáadása/eltávolítása |
> | Műveletek | Microsoft. ApiManagement/Service/namedValues/delete | Törli az adott megnevezett értéket az API Management Service-példányból. |
> | Műveletek | Microsoft. ApiManagement/Service/namedValues/listSecrets/művelet | Lekéri az azonosító alapján megadott megnevezett érték titkait. |
> | Műveletek | Microsoft. ApiManagement/Service/namedValues/READ | A szolgáltatási példányon belül definiált nevesített értékek gyűjteményét listázza. vagy lekéri az azonosító alapján megadott megnevezett érték részleteit. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/namedValues/írás | Megnevezett érték létrehozása vagy frissítése. vagy frissíti az adott megnevezett értéket. |
> | Műveletek | Microsoft. ApiManagement/Service/networkstatus/READ | Lekéri azon erőforrások hálózati hozzáférési állapotát, amelyeken a szolgáltatás függ. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/értesítések/művelet | Értesítés küldése egy megadott felhasználónak |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/értesítések/olvasás | A szolgáltatási példányon belül definiált tulajdonságok gyűjteményét listázza. vagy beolvassa az azonosító által megadott értesítés részleteit. |
> | Műveletek | Microsoft. ApiManagement/Service/Notifications/recipientEmails/delete | Eltávolítja az e-mailt az értesítési listából. |
> | Műveletek | Microsoft. ApiManagement/Service/Notifications/recipientEmails/READ | Beolvassa az értesítésre előfizetett értesítő címzett e-mailek listáját. |
> | Műveletek | Microsoft. ApiManagement/Service/Notifications/recipientEmails/Write | Hozzáadja az e-mail-címet az értesítés címzettjeinek listájához. |
> | Műveletek | Microsoft. ApiManagement/Service/Notifications/recipientUsers/delete | Eltávolítja a API Management felhasználót az értesítési listából. |
> | Műveletek | Microsoft. ApiManagement/Service/Notifications/recipientUsers/READ | Beolvassa az értesítésre előfizetett értesítési címzett felhasználó listáját. |
> | Műveletek | Microsoft. ApiManagement/Service/Notifications/recipientUsers/Write | Hozzáadja a API Management felhasználót az értesítés címzettjeinek listájához. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/értesítések/írás | API Management közzétevő értesítésének létrehozása vagy frissítése. |
> | Műveletek | Microsoft. ApiManagement/Service/openidConnectProviders/delete | Törli a API Management szolgáltatás példányának adott OpenID Connect-szolgáltatóját. |
> | Műveletek | Microsoft. ApiManagement/Service/openidConnectProviders/listSecrets/művelet | Adott OpenID Connect-szolgáltatói titkok beolvasása. |
> | Műveletek | Microsoft. ApiManagement/Service/openidConnectProviders/READ | Az összes OpenId Connect-szolgáltató listája. vagy egy adott OpenID Connect-szolgáltató beolvasása titkos kódok nélkül. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/openidConnectProviders/írás | Létrehozza vagy frissíti az OpenID Connect-szolgáltatót. vagy frissíti az adott OpenID Connect-szolgáltatót. |
> | Műveletek | Microsoft. ApiManagement/Service/operationresults/READ | A hosszú ideig futó művelet aktuális állapotának beolvasása |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/házirendek/törlés | Törli az API Management szolgáltatás globális házirend-konfigurációját. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/házirendek/olvasás | Az API Management szolgáltatás összes globális házirend-definícióját listázza. vagy szerezze be az API Management szolgáltatás globális szabályzatának definícióját. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/házirendek/írás | Létrehozza vagy frissíti az API Management szolgáltatás globális házirend-konfigurációját. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/házirend/törlés | A házirend-konfiguráció törlése a bérlői szinten |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/házirend/olvasás | Házirend-konfiguráció beolvasása a bérlői szinten |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/házirend/írás | Házirend-konfiguráció létrehozása a bérlői szinten |
> | Műveletek | Microsoft. ApiManagement/Service/policyDescriptions/READ | Felsorolja az összes házirend leírását. |
> | Műveletek | Microsoft. ApiManagement/Service/policySnippets/READ | Felsorolja az összes házirend-kódrészletet. |
> | Műveletek | Microsoft. ApiManagement/Service/portalsettings/READ | A portál bejelentkezési beállításainak beszerzése, illetve a portálra vonatkozó regisztrációs beállítások beszerzése, illetve a portál delegálási beállításainak beolvasása. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/portalsettings/írás | A bejelentkezési beállítások frissítése. vagy a bejelentkezési beállítások létrehozása vagy frissítése. vagy frissítse a regisztrációs beállításokat, vagy frissítse a regisztrációs beállításokat, vagy frissítse a delegálási beállításokat. vagy a delegálási beállítások létrehozása vagy frissítése. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/termékek/API-k/törlés | A megadott termékből törli a megadott API-t. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/termékek/API-k/olvasás | A termékhez társított API-k gyűjteményét listázza. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/termékek/API-k/írás | Egy API-t ad hozzá a megadott termékhez. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/termékek/törlés | Termék törlése. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/termékek/csoportok/törlés | Törli a megadott csoport és termék közötti társítást. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/termékek/csoportok/olvasás | Felsorolja a megadott termékhez társított fejlesztői csoportok gyűjteményét. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/termékek/csoportok/írás | Hozzáadja a megadott fejlesztői csoport társítását a megadott termékhez. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/termékek/házirendek/törlés | Törli a termék házirend-konfigurációját. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/termékek/házirendek/olvasás | A szabályzat konfigurációjának beolvasása a termék szintjén. vagy szerezze be a szabályzat konfigurációját a termék szintjén. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/termékek/házirendek/írás | Létrehozza vagy frissíti a termékhez tartozó házirend-konfigurációt. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/termékek/házirend/törlés | A házirend-konfiguráció törlése a termék szintjén |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/termékek/házirend/olvasás | A szabályzat konfigurációjának beolvasása a termék szintjén |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/termékek/házirend/írás | Házirend-konfiguráció létrehozása a termék szintjén |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/termékek/olvasás | Felsorolja a megadott szolgáltatási példányban található termékek gyűjteményét. vagy lekéri az azonosító alapján megadott termék részleteit. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/termékek/előfizetések/olvasás | Felsorolja a megadott termékre vonatkozó előfizetések gyűjteményét. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/termékek/címkék/törlés | Válassza le a címkét a termékből. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/termékek/címkék/olvasás | Felsorolja a termékhez társított összes címkét. vagy a termékhez társított címke beolvasása. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/termékek/címkék/írás | Címke kiosztása a termékhez. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/termékek/írás | Létrehoz vagy frissít egy terméket. vagy frissítse a meglévő termék részleteit. |
> | Műveletek | Microsoft. ApiManagement/Service/productsByTags/READ | A címkékhez társított termékek gyűjteményét listázza. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/tulajdonságok/törlés | A API Management szolgáltatás példányának adott tulajdonságát törli. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/tulajdonságok/listSecrets/művelet | Lekéri az azonosító alapján megadott tulajdonság titkait. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/tulajdonságok/olvasás | A szolgáltatási példányon belül definiált tulajdonságok gyűjteményét listázza. vagy lekéri az azonosító alapján megadott tulajdonság részleteit. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/tulajdonságok/írás | Létrehoz vagy frissít egy tulajdonságot. vagy frissíti az adott tulajdonságot. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/kvóták/időszakok/olvasás | Kvóta számláló értékének lekérése az időszakhoz |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/kvóták/időszakok/írás | Kvóta számlálójának jelenlegi értékének beállítása |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/kvóták/olvasás | Kvóta értékének beolvasása |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/kvóták/írás | Kvóta számlálójának jelenlegi értékének beállítása |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/olvasás | API Management szolgáltatási példány metaadatainak olvasása |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/régiók/olvasás | Felsorolja az összes olyan Azure-régiót, amelyben a szolgáltatás létezik. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/jelentések/olvasás | Jelentések beolvasása időszakok szerint összesítve, vagy a jelentés beolvasása földrajzi régió szerint összesítve vagy a fejlesztők által összesített jelentés beolvasása.<br>vagy a jelentések összesítése termékek alapján.<br>vagy beolvashatja az API-k által összesített jelentést, vagy beolvashatja az Operations által összesített jelentést, vagy az előfizetés alapján összesítheti a jelentést.<br>vagy kérések beolvasása az adatjelentésekről |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/visszaállítás/művelet | API Management szolgáltatás visszaállítása a megadott tárolóból egy felhasználó által megadott Storage-fiókban |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/előfizetések/törlés | Törli a megadott előfizetést. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/előfizetések/listSecrets/művelet | Lekéri a megadott előfizetési kulcsokat. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/előfizetések/olvasás | Az API Management Service-példány összes előfizetését listázza. vagy lekéri a megadott előfizetési entitást (kulcs nélkül). |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/előfizetések/regeneratePrimaryKey/művelet | Újragenerálta a API Management szolgáltatási példány meglévő előfizetésének elsődleges kulcsát. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/előfizetések/regenerateSecondaryKey/művelet | Újragenerálja a API Management szolgáltatási példány meglévő előfizetésének másodlagos kulcsát. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/előfizetések/írás | Létrehozza vagy frissíti a megadott felhasználó előfizetését a megadott termékre. vagy frissíti az azonosító alapján megadott előfizetés részleteit. |
> | Műveletek | Microsoft. ApiManagement/Service/tagResources/READ | A címkékhez társított erőforrások gyűjteményét listázza. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/címkék/törlés | Törli a API Management szolgáltatási példány adott címkéjét. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/címkék/olvasás | A szolgáltatási példányon belül definiált címkék gyűjteményét listázza. vagy lekéri az azonosító alapján megadott címke részleteit. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/címkék/írás | Létrehoz egy címkét. vagy frissíti az azonosító alapján megadott címke részleteit. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/sablonok/törlés | Alapértelmezett API Management e-mail sablon alaphelyzetbe állítása |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/sablonok/olvasás | Az összes e-mail-sablon beolvasása vagy API Management e-mail sablon adatainak beolvasása |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/sablonok/írás | API Management e-mail-sablon létrehozása vagy frissítése API Management e-mail-sablon frissítése |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/bérlő/törlés | A bérlő házirend-konfigurációjának eltávolítása |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/bérlő/üzembe helyezés/művelet | Egy központi telepítési feladat futtatásával alkalmazza a megadott git-ág módosításait az adatbázisban található konfigurációra. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/bérlő/listSecrets/művelet | Bérlői hozzáférési információk részleteinek beolvasása |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/bérlő/operationResults/olvasás | A művelet eredményeinek beolvasása vagy egy adott művelet eredményének beolvasása |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/bérlő/olvasás | Az API Management szolgáltatás globális szabályzat-definíciójának beolvasása. vagy a bérlői hozzáférési információk részleteinek beolvasása |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/bérlő/regeneratePrimaryKey/művelet | Elsődleges elérési kulcs előállítása |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/bérlő/regenerateSecondaryKey/művelet | Másodlagos elérési kulcs újragenerálása |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/bérlő/Mentés/művelet | Elkészíti a commit és a konfiguráció pillanatképét a tárházban lévő megadott ágra |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/bérlő/syncState/olvasás | Az utolsó git-szinkronizálás állapotának beolvasása |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/bérlő/érvényesítés/művelet | Ellenőrzi a megadott git-ág változásait. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/bérlő/írás | Házirend-konfiguráció beállítása a bérlőhöz vagy a bérlői hozzáférési információk frissítése – részletek |
> | Műveletek | Microsoft. ApiManagement/Service/updatecertificate/művelet | API Management szolgáltatás SSL-tanúsítványának feltöltése |
> | Műveletek | Microsoft. ApiManagement/Service/updatehostname/művelet | API Management szolgáltatás egyéni tartománynevének beállítása, frissítése vagy eltávolítása |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/felhasználók/művelet | Új felhasználó regisztrálása |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/felhasználók/megerősítések/küldés/művelet | Visszaigazolás küldése |
> | Műveletek | Microsoft. ApiManagement/Service/Users/delete | Adott felhasználó törlése. |
> | Műveletek | Microsoft. ApiManagement/Service/Users/generateSsoUrl/Action | Egy hitelesítési jogkivonatot tartalmazó átirányítási URL-címet kér le egy adott felhasználó a fejlesztői portálon való aláírásához. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/felhasználók/csoportok/olvasás | Felsorolja az összes felhasználói csoportot. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/felhasználók/identitások/olvasás | Az összes felhasználói identitás listája. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/felhasználók/kulcsok/olvasás | Felhasználóhoz tartozó kulcsok beolvasása |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/felhasználók/olvasás | Felsorolja a megadott szolgáltatási példányban regisztrált felhasználók gyűjteményét. vagy lekéri a felhasználó azonosítójában megadott adatokat. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/felhasználók/előfizetések/olvasás | Felsorolja a megadott felhasználó előfizetéseit. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/felhasználó/jogkivonat/művelet | A felhasználó megosztott hozzáférési engedélyezési jogkivonatának beolvasása. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/felhasználók/írás | Létrehoz vagy frissít egy felhasználót. vagy frissíti az azonosító alapján megadott felhasználó részleteit. |
> | Műveletek | Microsoft. ApiManagement/szolgáltatás/írás | API Management szolgáltatás új példányának létrehozása |
> | Műveletek | Microsoft. ApiManagement/regisztráció/művelet | A Microsoft. ApiManagement erőforrás-szolgáltatóhoz tartozó előfizetés regisztrációjának megszüntetése |

## <a name="microsoftauthorization"></a>Microsoft. Authorization

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Authorization/classicAdministrators/delete | Eltávolítja a rendszergazdát az előfizetésből. |
> | Műveletek | Microsoft. Authorization/classicAdministrators/operationstatuses/READ | Az előfizetés rendszergazdai műveleti állapotának beolvasása. |
> | Műveletek | Microsoft. Authorization/classicAdministrators/olvasás | Az előfizetés rendszergazdáinak beolvasása. |
> | Műveletek | Microsoft. Authorization/classicAdministrators/írás | Egy előfizetéshez tartozó rendszergazda hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. Authorization/denyAssignments/delete | Egy megtagadási hozzárendelés törlése a megadott hatókörben. |
> | Műveletek | Microsoft. Authorization/denyAssignments/olvasás | Megtagadási hozzárendelés adatainak beolvasása. |
> | Műveletek | Microsoft. Authorization/denyAssignments/írás | Megtagadási hozzárendelés létrehozása a megadott hatókörben. |
> | Műveletek | Microsoft. Authorization/elevateAccess/művelet | A hívónak felhasználói hozzáférésű rendszergazdai hozzáférést engedélyez a bérlői hatókörben |
> | Műveletek | Microsoft. Authorization/zárolások/törlés | Zárolások törlése a megadott hatókörben. |
> | Műveletek | Microsoft. Authorization/zárolás/olvasás | Lekéri a megadott hatókör zárolásait. |
> | Műveletek | Microsoft. Authorization/zárolás/írás | Zárolások hozzáadása a megadott hatókörben. |
> | Műveletek | Microsoft. Authorization/Operations/READ | A műveletek listájának beolvasása |
> | Műveletek | Microsoft. Authorization/engedélyek/olvasás | Felsorolja az összes olyan engedélyt, amelyet a hívó adott hatókörben adott meg. |
> | Műveletek | Microsoft. Authorization/szabályzatok/naplózás/művelet | A Azure Policy kiértékelésének eredményeképpen végrehajtott művelet "audit" hatással |
> | Műveletek | Microsoft. Authorization/házirendek/auditIfNotExists/művelet | A Azure Policy kiértékelésének eredményeképpen végrehajtott művelet "auditIfNotExists" hatással |
> | Műveletek | Microsoft. Authorization/házirendek/megtagadás/művelet | A Azure Policy kiértékelésének eredményeképpen végrehajtott művelet "megtagadás" hatással |
> | Műveletek | Microsoft. Authorization/házirendek/deployIfNotExists/művelet | Action taken as a result of evaluation of Azure Policy with 'deployIfNotExists' effect |
> | Műveletek | Microsoft.Authorization/policyAssignments/delete | Delete a policy assignment at the specified scope. |
> | Műveletek | Microsoft.Authorization/policyAssignments/read | Get information about a policy assignment. |
> | Műveletek | Microsoft.Authorization/policyAssignments/write | Create a policy assignment at the specified scope. |
> | Műveletek | Microsoft.Authorization/policyDefinitions/delete | Delete a policy definition. |
> | Műveletek | Microsoft.Authorization/policyDefinitions/read | Get information about a policy definition. |
> | Műveletek | Microsoft.Authorization/policyDefinitions/write | Create a custom policy definition. |
> | Műveletek | Microsoft.Authorization/policySetDefinitions/delete | Delete a policy set definition. |
> | Műveletek | Microsoft.Authorization/policySetDefinitions/read | Get information about a policy set definition. |
> | Műveletek | Microsoft.Authorization/policySetDefinitions/write | Create a custom policy set definition. |
> | Műveletek | Microsoft.Authorization/providerOperations/read | Get operations for all resource providers which can be used in role definitions. |
> | Műveletek | Microsoft.Authorization/roleAssignments/delete | Delete a role assignment at the specified scope. |
> | Műveletek | Microsoft.Authorization/roleAssignments/read | Get information about a role assignment. |
> | Műveletek | Microsoft.Authorization/roleAssignments/write | Create a role assignment at the specified scope. |
> | Műveletek | Microsoft.Authorization/roleDefinitions/delete | Delete the specified custom role definition. |
> | Műveletek | Microsoft.Authorization/roleDefinitions/read | Get information about a role definition. |
> | Műveletek | Microsoft.Authorization/roleDefinitions/write | Create or update a custom role definition with specified permissions and assignable scopes. |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | Read an Azure Automation DSC's registration information |
> | Műveletek | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | Writes a request to regenerate Azure Automation DSC keys |
> | Műveletek | Microsoft.Automation/automationAccounts/certificates/delete | Deletes an Azure Automation certificate asset |
> | Műveletek | Microsoft.Automation/automationAccounts/certificates/getCount/action | Reads the count of certificates |
> | Műveletek | Microsoft.Automation/automationAccounts/certificates/read | Gets an Azure Automation certificate asset |
> | Műveletek | Microsoft.Automation/automationAccounts/certificates/write | Creates or updates an Azure Automation certificate asset |
> | Műveletek | Microsoft.Automation/automationAccounts/compilationjobs/read | Reads an Azure Automation DSC's Compilation |
> | Műveletek | Microsoft.Automation/automationAccounts/compilationjobs/read | Reads an Azure Automation DSC's Compilation |
> | Műveletek | Microsoft.Automation/automationAccounts/compilationjobs/write | Writes an Azure Automation DSC's Compilation |
> | Műveletek | Microsoft.Automation/automationAccounts/compilationjobs/write | Writes an Azure Automation DSC's Compilation |
> | Műveletek | Microsoft.Automation/automationAccounts/configurations/content/read | Reads the configuration media content |
> | Műveletek | Microsoft.Automation/automationAccounts/configurations/delete | Deletes an Azure Automation DSC's content |
> | Műveletek | Microsoft.Automation/automationAccounts/configurations/getCount/action | Reads the count of an Azure Automation DSC's content |
> | Műveletek | Microsoft.Automation/automationAccounts/configurations/read | Gets an Azure Automation DSC's content |
> | Műveletek | Microsoft.Automation/automationAccounts/configurations/write | Writes an Azure Automation DSC's content |
> | Műveletek | Microsoft.Automation/automationAccounts/connections/delete | Deletes an Azure Automation connection asset |
> | Műveletek | Microsoft.Automation/automationAccounts/connections/getCount/action | Reads the count of connections |
> | Műveletek | Microsoft.Automation/automationAccounts/connections/read | Gets an Azure Automation connection asset |
> | Műveletek | Microsoft.Automation/automationAccounts/connections/write | Creates or updates an Azure Automation connection asset |
> | Műveletek | Microsoft.Automation/automationAccounts/connectionTypes/delete | Deletes an Azure Automation connection type asset |
> | Műveletek | Microsoft.Automation/automationAccounts/connectionTypes/read | Gets an Azure Automation connection type asset |
> | Műveletek | Microsoft.Automation/automationAccounts/connectionTypes/write | Creates an Azure Automation connection type asset |
> | Műveletek | Microsoft.Automation/automationAccounts/credentials/delete | Deletes an Azure Automation credential asset |
> | Műveletek | Microsoft.Automation/automationAccounts/credentials/getCount/action | Reads the count of credentials |
> | Műveletek | Microsoft.Automation/automationAccounts/credentials/read | Gets an Azure Automation credential asset |
> | Műveletek | Microsoft.Automation/automationAccounts/credentials/write | Creates or updates an Azure Automation credential asset |
> | Műveletek | Microsoft.Automation/automationAccounts/delete | Deletes an Azure Automation account |
> | Műveletek | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | Deletes Hybrid Runbook Worker Resources |
> | Műveletek | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Reads Hybrid Runbook Worker Resources |
> | Műveletek | Microsoft.Automation/automationAccounts/jobs/output/read | Gets the output of a job |
> | Műveletek | Microsoft.Automation/automationAccounts/jobs/read | Gets an Azure Automation job |
> | Műveletek | Microsoft.Automation/automationAccounts/jobs/resume/action | Resumes an Azure Automation job |
> | Műveletek | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | Lekéri a Azure Automation runbook tartalmát a feladatok végrehajtásának időpontjában. |
> | Műveletek | Microsoft. Automation/automationAccounts/feladatok/leállítás/művelet | Leállítja egy Azure Automation feladatot |
> | Műveletek | Microsoft. Automation/automationAccounts/feladatok/streamek/olvasás | Beolvas egy Azure Automation-feladatok streamjét |
> | Műveletek | Microsoft. Automation/automationAccounts/feladatok/streamek/olvasás | Beolvas egy Azure Automation-feladatok streamjét |
> | Műveletek | Microsoft. Automation/automationAccounts/Jobs/felfüggesztés/művelet | Azure Automation feladatok felfüggesztése |
> | Műveletek | Microsoft. Automation/automationAccounts/feladatok/írás | Létrehoz egy Azure Automation feladatot |
> | Műveletek | Microsoft. Automation/automationAccounts/jobSchedules/delete | Azure Automation feladatütemezés törlése |
> | Műveletek | Microsoft. Automation/automationAccounts/jobSchedules/READ | Azure Automation feladatütemezés beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/jobSchedules/Write | Létrehoz egy Azure Automation feladatok ütemtervét |
> | Műveletek | Microsoft. Automation/automationAccounts/linkedWorkspace/READ | Az Automation-fiókhoz kapcsolódó munkaterület beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/Listkeys műveletének beolvasása/művelet | Az Automation-fiók kulcsainak beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/modulok/tevékenységek/olvasás | Lekéri Azure Automation tevékenységeket |
> | Műveletek | Microsoft. Automation/automationAccounts/modulok/törlés | Azure Automation PowerShell-modul törlése |
> | Műveletek | Microsoft. Automation/automationAccounts/modulok/getCount/Action | Az Automation-fiókban lévő PowerShell-modulok számának beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/modulok/olvasás | Azure Automation PowerShell-modul beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/modulok/írás | Egy Azure Automation PowerShell-modul létrehozása vagy frissítése |
> | Műveletek | Microsoft. Automation/automationAccounts/nodeConfigurations/delete | Azure Automation DSC csomópont-konfigurációjának törlése |
> | Műveletek | Microsoft. Automation/automationAccounts/nodeConfigurations/rawContent/művelet | Beolvas egy Azure Automation DSC csomópont-konfigurációs tartalmát |
> | Műveletek | Microsoft. Automation/automationAccounts/nodeConfigurations/READ | Egy Azure Automation DSC csomópont-konfigurációjának beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/nodeConfigurations/Write | Egy Azure Automation DSC csomópont-konfigurációjának írása |
> | Műveletek | Microsoft. Automation/automationAccounts/nodecounts/READ | A megadott típushoz tartozó csomópontok számának összefoglalásának beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/csomópontok/törlés | Azure Automation DSC-csomópontok törlése |
> | Műveletek | Microsoft. Automation/automationAccounts/csomópontok/olvasás | Azure Automation DSC-csomópontok beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/csomópontok/jelentések/tartalom/olvasás | Azure Automation DSC-jelentés tartalmának beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/csomópontok/jelentések/olvasás | Azure Automation DSC-jelentések beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/csomópont/írás | Azure Automation DSC-csomópontok létrehozása vagy frissítése |
> | Műveletek | Microsoft. Automation/automationAccounts/objectDataTypes/Fields/READ | Azure Automation TypeFields beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/python2Packages/delete | Azure Automation Python 2 csomag törlése |
> | Műveletek | Microsoft. Automation/automationAccounts/python2Packages/READ | Azure Automation Python 2 csomag beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/python2Packages/Write | Egy Azure Automation Python 2 csomag létrehozása vagy frissítése |
> | Műveletek | Microsoft. Automation/automationAccounts/python3Packages/delete | Azure Automation Python 3 csomag törlése |
> | Műveletek | Microsoft. Automation/automationAccounts/python3Packages/READ | Azure Automation Python 3 csomag beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/python3Packages/Write | Egy Azure Automation Python 3 csomag létrehozása vagy frissítése |
> | Műveletek | Microsoft. Automation/automationAccounts/READ | Azure Automation fiók beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/runbookok/Content/READ | Egy Azure Automation runbook tartalmának beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/runbookok/delete | Azure Automation runbook törlése |
> | Műveletek | Microsoft. Automation/automationAccounts/runbookok/Piszkozat/tartalom/írás | Egy Azure Automation runbook-Piszkozat tartalmának létrehozása |
> | Műveletek | Microsoft. Automation/automationAccounts/runbookok/draft/operationResults/READ | Beolvassa Azure Automation runbook-Piszkozat műveleti eredményeit |
> | Műveletek | Microsoft. Automation/automationAccounts/runbookok/draft/olvasás | Azure Automation runbook-tervezet beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/runbookok/draft/testJob/READ | Beolvas egy Azure Automation runbook-Piszkozat tesztelési feladatot |
> | Műveletek | Microsoft. Automation/automationAccounts/runbookok/draft/testJob/folytatás/művelet | Egy Azure Automation runbook-Piszkozat tesztelési feladatot folytat |
> | Műveletek | Microsoft. Automation/automationAccounts/runbookok/draft/testJob/leállítás/művelet | Leállít egy Azure Automation runbook-Piszkozat tesztelési feladatot |
> | Műveletek | Microsoft. Automation/automationAccounts/runbookok/draft/testJob/felfüggesztés/művelet | Egy Azure Automation runbook-Piszkozat tesztelési feladata felfüggesztése |
> | Műveletek | Microsoft. Automation/automationAccounts/runbookok/draft/testJob/írás | Létrehoz egy Azure Automation runbook-Piszkozat tesztelési feladatot |
> | Műveletek | Microsoft. Automation/automationAccounts/runbookok/draft/undoEdit/művelet | Módosítások visszavonása egy Azure Automation runbook-piszkozatra |
> | Műveletek | Microsoft. Automation/automationAccounts/runbookok/getCount/művelet | Lekéri Azure Automation runbookok számát |
> | Műveletek | Microsoft. Automation/automationAccounts/runbookok/közzététel/művelet | Azure Automation runbook-Piszkozat közzététele |
> | Műveletek | Microsoft. Automation/automationAccounts/runbookok/READ | Azure Automation runbook beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/runbookok/Write | Létrehoz vagy frissít egy Azure Automation runbook |
> | Műveletek | Microsoft. Automation/automationAccounts/ütemtervek/törlés | Azure Automation ütemezett eszköz törlése |
> | Műveletek | Microsoft. Automation/automationAccounts/Schedules/getCount/Action | Beolvassa Azure Automation ütemtervek számát |
> | Műveletek | Microsoft. Automation/automationAccounts/ütemezett/beolvasás | Azure Automation ütemezett eszköz beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/ütemterv/írás | Egy Azure Automation Schedule-eszköz létrehozása vagy frissítése |
> | Műveletek | Microsoft. Automation/automationAccounts/softwareUpdateConfigurationMachineRuns/READ | Egy Azure Automation szoftverfrissítési konfigurációs gép futtatásának beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/softwareUpdateConfigurationRuns/READ | Azure Automation szoftverfrissítés-konfiguráció futtatásának beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/softwareUpdateConfigurations/delete | Azure Automation szoftverfrissítés konfigurációjának törlése |
> | Műveletek | Microsoft. Automation/automationAccounts/softwareUpdateConfigurations/delete | Azure Automation szoftverfrissítés konfigurációjának törlése |
> | Műveletek | Microsoft. Automation/automationAccounts/softwareUpdateConfigurations/READ | Azure Automation szoftverfrissítés konfigurációjának beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/softwareUpdateConfigurations/READ | Azure Automation szoftverfrissítés konfigurációjának beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/softwareUpdateConfigurations/Write | Azure Automation szoftverfrissítési konfiguráció létrehozása vagy frissítése |
> | Műveletek | Microsoft. Automation/automationAccounts/softwareUpdateConfigurations/Write | Azure Automation szoftverfrissítési konfiguráció létrehozása vagy frissítése |
> | Műveletek | Microsoft. Automation/automationAccounts/statisztika/olvasás | Azure Automation statisztikájának beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/updateDeploymentMachineRuns/READ | Azure Automation frissítési üzembe helyezési gép beszerzése |
> | Műveletek | Microsoft. Automation/automationAccounts/updateManagementPatchJob/READ | Beolvas egy Azure Automation Update Management-javítási feladatot |
> | Műveletek | Microsoft. Automation/automationAccounts/használat/olvasás | Lekéri Azure Automation használatát |
> | Műveletek | Microsoft. Automation/automationAccounts/változók/törlés | Azure Automation változó eszköz törlése |
> | Műveletek | Microsoft. Automation/automationAccounts/változók/olvasás | Egy Azure Automation változó eszköz beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/változók/írás | Egy Azure Automation változó eszköz létrehozása vagy frissítése |
> | Műveletek | Microsoft. Automation/automationAccounts/figyelők/törlés | Azure Automation Watcher-feladatok törlése |
> | Műveletek | Microsoft. Automation/automationAccounts/figyelők/olvasás | Beolvas egy Azure Automation figyelő feladatot |
> | Műveletek | Microsoft. Automation/automationAccounts/figyelők/indítás/művelet | Azure Automation Watcher-feladatok indítása |
> | Műveletek | Microsoft. Automation/automationAccounts/figyelők/leállítás/művelet | Azure Automation Watcher-feladatok leállítása |
> | Műveletek | Microsoft. Automation/automationAccounts/figyelők/streamek/olvasás | Beolvas egy Azure Automation megfigyelői feladatok streamjét |
> | Műveletek | Microsoft. Automation/automationAccounts/figyelők/watcherActions/delete | Azure Automation figyelőkkel kapcsolatos feladatok törlése |
> | Műveletek | Microsoft. Automation/automationAccounts/figyelők/watcherActions/READ | Azure Automation figyelőkkel kapcsolatos feladatok beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/figyelők/watcherActions/írás | Azure Automation figyelőkkel kapcsolatos feladatok létrehozása |
> | Műveletek | Microsoft. Automation/automationAccounts/figyelők/írás | Létrehoz egy Azure Automation figyelő feladatot |
> | Műveletek | Microsoft. Automation/automationAccounts/webhookok/művelet | URI generálása egy Azure Automation webhookhoz |
> | Műveletek | Microsoft. Automation/automationAccounts/webhookok/törlés | Azure Automation webhook törlése  |
> | Műveletek | Microsoft. Automation/automationAccounts/webhookok/olvasás | Egy Azure Automation webhook beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/webhookok/írás | Egy Azure Automation webhook létrehozása vagy frissítése |
> | Műveletek | Microsoft. Automation/automationAccounts/írás | Egy Azure Automation fiók létrehozása vagy frissítése |
> | Műveletek | Microsoft. Automation/Operations/READ | A Azure Automation erőforrásaihoz elérhető műveletek beolvasása |
> | Műveletek | Microsoft. Automation/regisztráció/művelet | Az előfizetés regisztrálása Azure Automation |

## <a name="microsoftazureactivedirectory"></a>Microsoft. AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. AzureActiveDirectory/b2cDirectories/delete | B2C-címtár erőforrásának törlése |
> | Műveletek | Microsoft. AzureActiveDirectory/b2cDirectories/READ | B2C-címtár erőforrásának megtekintése |
> | Műveletek | Microsoft. AzureActiveDirectory/b2cDirectories/Write | B2C-címtár erőforrásának létrehozása vagy frissítése |
> | Műveletek | Microsoft. AzureActiveDirectory/b2ctenants/READ | Felsorolja az összes B2C-bérlőt, ahol a felhasználó tagja |
> | Műveletek | Microsoft. AzureActiveDirectory/Operations/READ | A Microsoft. AzureActiveDirectory erőforrás-szolgáltatóhoz elérhető összes API-művelet olvasása |
> | Műveletek | Microsoft. AzureActiveDirectory/regisztráció/művelet | Előfizetés regisztrálása a Microsoft. AzureActiveDirectory erőforrás-szolgáltatónál |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. AzureStack/Operations/READ | Erőforrás-szolgáltatói művelet tulajdonságainak beolvasása |
> | Műveletek | Microsoft. AzureStack/regisztráció/művelet | Előfizetés regisztrálása a Microsoft. AzureStack erőforrás-szolgáltatónál |
> | Műveletek | Microsoft. AzureStack/regisztráció/customerSubscriptions/delete | Azure Stack ügyfél-előfizetés törlése |
> | Műveletek | Microsoft. AzureStack/regisztráció/customerSubscriptions/olvasás | Lekéri egy Azure Stack ügyfél-előfizetés tulajdonságait |
> | Műveletek | Microsoft. AzureStack/regisztráció/customerSubscriptions/írás | Létrehoz vagy frissít egy Azure Stack ügyfél-előfizetést |
> | Műveletek | Microsoft. AzureStack/regisztráció/törlés | Azure Stack Regisztráció törlése |
> | Műveletek | Microsoft. AzureStack/regisztráció/getActivationKey/művelet | A legújabb Azure Stack aktiválási kulcs beolvasása |
> | Műveletek | Microsoft. AzureStack/regisztráció/termékek/getProduct/művelet | Azure Stack Marketplace-termék beolvasása |
> | Műveletek | Microsoft. AzureStack/regisztráció/termékek/getProducts/művelet | Azure Stack Marketplace-termékek listájának beolvasása |
> | Műveletek | Microsoft. AzureStack/regisztráció/termékek/listDetails/művelet | Azure Stack Marketplace-termék részletes adatainak beolvasása |
> | Műveletek | Microsoft. AzureStack/regisztráció/termékek/olvasás | Egy Azure Stack Marketplace-termék tulajdonságainak beolvasása |
> | Műveletek | Microsoft. AzureStack/regisztráció/termékek/uploadProductLog/művelet | A Piactéri termék műveleti állapotának és időbélyegének rögzítése Azure Stack |
> | Műveletek | Microsoft. AzureStack/regisztráció/olvasás | Egy Azure Stack regisztráció tulajdonságainak beolvasása |
> | Műveletek | Microsoft. AzureStack/regisztráció/írás | Létrehoz vagy frissít egy Azure Stack regisztrációt |
> | Műveletek | Microsoft. AzureStack/verificationKeys/getCurrentKey/művelet | Beolvassa Azure Stack aláírás nyilvános kulcsának aktuális verzióját |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. batch/batchAccounts/alkalmazások/törlés | Egy alkalmazás törlése |
> | Műveletek | Microsoft. batch/batchAccounts/alkalmazások/olvasás | Alkalmazások felsorolása vagy egy alkalmazás tulajdonságainak beolvasása |
> | Műveletek | Microsoft. batch/batchAccounts/alkalmazások/verziók/aktiválás/művelet | Alkalmazás-csomag aktiválása |
> | Műveletek | Microsoft. batch/batchAccounts/alkalmazások/verziók/törlés | Alkalmazáscsomag törlése |
> | Műveletek | Microsoft. batch/batchAccounts/alkalmazások/verziók/olvasás | Egy alkalmazáscsomag tulajdonságainak beolvasása |
> | Műveletek | Microsoft. batch/batchAccounts/alkalmazások/verziók/írás | Új alkalmazáscsomag létrehozása vagy meglévő alkalmazáscsomag frissítése |
> | Műveletek | Microsoft. batch/batchAccounts/alkalmazások/írás | Új alkalmazás létrehozása vagy meglévő alkalmazás frissítése |
> | Műveletek | Microsoft. batch/batchAccounts/certificateOperationResults/olvasás | Egy hosszú ideig futó tanúsítvány-művelet eredményének beolvasása batch-fiókon |
> | Műveletek | Microsoft. batch/batchAccounts/Certificates/cancelDelete/Action | Egy batch-fiókban lévő tanúsítvány sikertelen törlésének megszakítása |
> | Műveletek | Microsoft. batch/batchAccounts/tanúsítványok/törlés | Tanúsítvány törlése batch-fiókból |
> | Műveletek | Microsoft. batch/batchAccounts/tanúsítványok/olvasás | A Batch-fiókban lévő tanúsítványok listája vagy a tanúsítvány tulajdonságainak beolvasása |
> | Műveletek | Microsoft. batch/batchAccounts/tanúsítványok/írás | Új tanúsítvány létrehozása batch-fiókon vagy meglévő tanúsítvány frissítése |
> | Műveletek | Microsoft. batch/batchAccounts/delete | Batch-fiók törlése |
> | DataAction | Microsoft. batch/batchAccounts/feladatok/törlés | Feladatot töröl egy batch-fiókból |
> | DataAction | Microsoft. batch/batchAccounts/feladatok/olvasás | A Batch-fiókban lévő feladatok felsorolása vagy a feladat tulajdonságainak beolvasása |
> | DataAction | Microsoft. batch/batchAccounts/feladatok/írás | Új feladatot hoz létre egy batch-fiókon, vagy frissít egy meglévő feladatot. |
> | DataAction | Microsoft. batch/batchAccounts/jobSchedules/delete | Ütemezett feladatütemezés törlése batch-fiókból |
> | DataAction | Microsoft. batch/batchAccounts/jobSchedules/olvasás | Egy batch-fiókban lévő feladatok ütemezett listáját vagy egy feladatütemezés tulajdonságainak beolvasása |
> | DataAction | Microsoft. batch/batchAccounts/jobSchedules/írás | Új feladatütemezés létrehozása batch-fiókon vagy meglévő feladatütemezés frissítése |
> | Műveletek | Microsoft. batch/batchAccounts/listkeys műveletének beolvasása/művelet | Egy batch-fiók hozzáférési kulcsainak listázása |
> | Műveletek | Microsoft. batch/batchAccounts/operationResults/olvasás | Egy hosszú ideig futó batch-fiók eredményének beolvasása |
> | Műveletek | Microsoft. batch/batchAccounts/poolOperationResults/olvasás | Egy hosszú ideig futó készlet műveletének eredményét jeleníti meg egy batch-fiókban |
> | Műveletek | Microsoft. batch/batchAccounts/készletek/törlés | Készlet törlése batch-fiókból |
> | Műveletek | Microsoft. batch/batchAccounts/készletek/disableAutoscale/művelet | Letiltja a Batch-fiókok automatikus méretezését |
> | Műveletek | Microsoft. batch/batchAccounts/készletek/olvasás | A Batch-fiókban lévő készletek felsorolása vagy a készlet tulajdonságainak beolvasása |
> | Műveletek | Microsoft. batch/batchAccounts/készletek/stopResize/művelet | Egy folyamatban lévő átméretezési művelet leállítása egy batch-fiók készletén |
> | Műveletek | Microsoft. batch/batchAccounts/készletek/írás | Létrehoz egy új készletet egy batch-fiókon, vagy frissít egy meglévő készletet. |
> | Műveletek | Microsoft. batch/batchAccounts/olvasás | Batch-fiókokat listáz, vagy egy batch-fiók tulajdonságait kéri le. |
> | Műveletek | Microsoft. batch/batchAccounts/regeneratekeys/művelet | Egy batch-fiók hozzáférési kulcsainak újragenerálása |
> | Műveletek | Microsoft. batch/batchAccounts/syncAutoStorageKeys/művelet | A Batch-fiókhoz konfigurált automatikus Storage-fiókhoz tartozó hozzáférési kulcsok szinkronizálása |
> | Műveletek | Microsoft. batch/batchAccounts/írás | Új batch-fiók létrehozása vagy meglévő batch-fiók frissítése |
> | Műveletek | Microsoft. batch/Locations/accountOperationResults/READ | Egy hosszú ideig futó batch-fiók eredményének beolvasása |
> | Műveletek | Microsoft. batch/Locations/checkNameAvailability/Action | Ellenőrzi, hogy a fiók neve érvényes-e, és nincs-e használatban. |
> | Műveletek | Microsoft. batch/helyszínek/kvóták/olvasás | A megadott előfizetéshez tartozó Batch-kvóták beolvasása a megadott Azure-régióban |
> | Műveletek | Microsoft. batch/Operations/READ | A Microsoft. batch erőforrás-szolgáltatón elérhető műveletek listája |
> | Műveletek | Microsoft. batch/regisztráció/művelet | Regisztrálja az előfizetést a Batch erőforrás-szolgáltató számára, és lehetővé teszi a Batch-fiókok létrehozását. |
> | Műveletek | Microsoft. batch/regisztráció/művelet | A Batch erőforrás-szolgáltatóhoz tartozó előfizetés regisztrációjának törlése a Batch-fiókok létrehozásának megakadályozása érdekében |

## <a name="microsoftbilling"></a>Microsoft. számlázás

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Billing/billingAccounts/szerződések/olvasás |  |
> | Műveletek | Microsoft. Billing/billingAccounts/billingPermissions/READ |  |
> | Műveletek | Microsoft. Billing/billingAccounts/billingProfiles/billingPermissions/READ |  |
> | Műveletek | Microsoft. Billing/billingAccounts/billingProfiles/ügyfelek/olvasás |  |
> | Műveletek | Microsoft. Billing/billingAccounts/billingProfiles/számlák/árlista/Letöltés/művelet |  |
> | Műveletek | Microsoft. Billing/billingAccounts/billingProfiles/invoiceSections/billingPermissions/READ |  |
> | Műveletek | Microsoft. Billing/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/áthelyezés/művelet |  |
> | Műveletek | Microsoft. Billing/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/átvitel/művelet |  |
> | Műveletek | Microsoft. Billing/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/validateMoveEligibility/művelet |  |
> | Műveletek | Microsoft. Billing/billingAccounts/billingProfiles/invoiceSections/Products/Move/Action |  |
> | Műveletek | Microsoft. Billing/billingAccounts/billingProfiles/invoiceSections/termékek/átvitel/művelet |  |
> | Műveletek | Microsoft. Billing/billingAccounts/billingProfiles/invoiceSections/Products/validateMoveEligibility/Action |  |
> | Műveletek | Microsoft. Billing/billingAccounts/billingProfiles/invoiceSections/READ |  |
> | Műveletek | Microsoft. Billing/billingAccounts/billingProfiles/invoiceSections/Write |  |
> | Műveletek | Microsoft. Billing/billingAccounts/billingProfiles/árlista/Letöltés/művelet |  |
> | Műveletek | Microsoft. Billing/billingAccounts/billingProfiles/READ |  |
> | Műveletek | Microsoft. Billing/billingAccounts/billingProfiles/írás |  |
> | Műveletek | Microsoft. Billing/billingAccounts/billingProfiles/írás |  |
> | Műveletek | Microsoft. Billing/billingAccounts/billingSubscriptions/READ |  |
> | Műveletek | Microsoft. Billing/billingAccounts/Customers/billingPermissions/READ |  |
> | Műveletek | Microsoft. Billing/billingAccounts/ügyfelek/olvasás |  |
> | Műveletek | Microsoft. Billing/billingAccounts/részlegek/olvasás |  |
> | Műveletek | Microsoft. Billing/billingAccounts/enrollmentAccounts/billingPermissions/READ |  |
> | Műveletek | Microsoft. Billing/billingAccounts/enrollmentAccounts/READ |  |
> | Műveletek | Microsoft. Billing/billingAccounts/enrollmentDepartments/billingPermissions/READ |  |
> | Műveletek | Microsoft. Billing/billingAccounts/listInvoiceSectionsWithCreateSubscriptionPermission/művelet |  |
> | Műveletek | Microsoft. Billing/billingAccounts/Products/READ |  |
> | Műveletek | Microsoft. számlázás/billingAccounts/olvasás |  |
> | Műveletek | Microsoft. számlázás/billingAccounts/írás |  |
> | Műveletek | Microsoft. Billing/részlegek/olvasás |  |
> | Műveletek | Microsoft. Billing/számlák/Letöltés/művelet | Számla letöltése a listáról letölthető hivatkozás használatával |
> | Műveletek | Microsoft. számlázás/számlák/olvasás |  |
> | Műveletek | Microsoft. számlázás/regisztráció/művelet |  |
> | Műveletek | Microsoft. Billing/validateAddress/művelet |  |

## <a name="microsoftbingmaps"></a>Microsoft. BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. BingMaps/mapApis/delete | Művelet törlése |
> | Műveletek | Microsoft. BingMaps/mapApis/listSecrets/művelet | A titkok listázása |
> | Műveletek | Microsoft. BingMaps/mapApis/listSingleSignOnToken/művelet | Az erőforráshoz tartozó egyszeri bejelentkezés engedélyezési jogkivonatának olvasása |
> | Műveletek | Microsoft. BingMaps/mapApis/READ | Olvasási művelet |
> | Műveletek | Microsoft. BingMaps/mapApis/regenerateKey/művelet | Újragenerálta a kulcsot |
> | Műveletek | Microsoft. BingMaps/mapApis/Write | Írási művelet |
> | Műveletek | Microsoft. BingMaps/Operations/READ | A művelet leírása. |

## <a name="microsoftblockchain"></a>Microsoft. Blockchain

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Blockchain/blockchainMembers/delete | Egy meglévő Blockchain-tag törlése. |
> | Műveletek | Microsoft. Blockchain/blockchainMembers/listApiKeys/művelet | Lekérdezi vagy felsorolja a meglévő Blockchain-tag API-kulcsait. |
> | Műveletek | Microsoft. Blockchain/blockchainMembers/READ | Lekérdezi vagy felsorolja a meglévő Blockchain-tag (oka) t. |
> | DataAction | Microsoft. Blockchain/blockchainMembers/transactionNodes/csatlakozási/művelet | Csatlakozás egy Blockchain-tag tranzakciós csomóponthoz. |
> | Műveletek | Microsoft. Blockchain/blockchainMembers/transactionNodes/delete | Töröl egy meglévő Blockchain-tag tranzakciós csomópontot. |
> | Műveletek | Microsoft. Blockchain/blockchainMembers/transactionNodes/listApiKeys/művelet | Lekérdezi vagy felsorolja a meglévő Blockchain-tagok tranzakciós csomópontjának API-kulcsait. |
> | Műveletek | Microsoft. Blockchain/blockchainMembers/transactionNodes/READ | Lekérdezi vagy felsorolja a meglévő Blockchain-tag tranzakciós csomópont (oka) t. |
> | Műveletek | Microsoft. Blockchain/blockchainMembers/transactionNodes/Write | Létrehoz vagy frissít egy Blockchain-tag tranzakciós csomópontot. |
> | Műveletek | Microsoft. Blockchain/blockchainMembers/Write | Egy Blockchain-tag létrehozása vagy frissítése. |
> | Műveletek | Microsoft. Blockchain/cordaMembers/delete | Töröl egy meglévő Blockchain Corda tagot. |
> | Műveletek | Microsoft. Blockchain/cordaMembers/READ | Lekérdezi vagy felsorolja a meglévő Blockchain Corda-tag (ka) t. |
> | Műveletek | Microsoft. Blockchain/cordaMembers/Write | Létrehoz vagy frissít egy Blockchain Corda tagot. |
> | Műveletek | Microsoft. Blockchain/Locations/blockchainMemberOperationResults/READ | Lekéri a Blockchain tagjainak működési eredményeit. |
> | Műveletek | Microsoft. Blockchain/Locations/checkNameAvailability/Action | Ellenőrzi, hogy az erőforrás neve érvényes-e, és nincs-e használatban. |
> | Műveletek | Microsoft. Blockchain/Operations/READ | A Microsoft Blockchain erőforrás-szolgáltató összes műveletének listázása. |
> | Műveletek | Microsoft. Blockchain/regisztráció/művelet | Regisztrálja az előfizetést a Blockchain erőforrás-szolgáltatóhoz. |

## <a name="microsoftblueprint"></a>Microsoft. Blueprint

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Blueprint/blueprintAssignments/assignmentOperations/READ | A tervrajzok összetevőinek olvasása |
> | Műveletek | Microsoft. Blueprint/blueprintAssignments/delete | Tetszőleges tervelemek törlése |
> | Műveletek | Microsoft. Blueprint/blueprintAssignments/READ | A tervrajzok összetevőinek olvasása |
> | Műveletek | Microsoft. Blueprint/blueprintAssignments/whoisblueprint/Action | Azure-tervrajzok egyszerű objektum-azonosítójának beolvasása. |
> | Műveletek | Microsoft. Blueprint/blueprintAssignments/Write | Tetszőleges tervelemek létrehozása vagy frissítése |
> | Műveletek | Microsoft. Blueprint/tervezetek/összetevők/törlés | Tetszőleges tervelemek törlése |
> | Műveletek | Microsoft. Blueprint/tervezetek/összetevők/olvasás | A tervrajzok összetevőinek olvasása |
> | Műveletek | Microsoft. Blueprint/tervezetek/összetevők/írás | Tetszőleges tervelemek létrehozása vagy frissítése |
> | Műveletek | Microsoft. Blueprint/tervezetek/törlés | Bármilyen tervrajz törlése |
> | Műveletek | Microsoft. Blueprint/tervezetek/olvasás | Bármilyen tervrajz beolvasása |
> | Műveletek | Microsoft. Blueprint/tervezetek/verziók/összetevők/olvasás | A tervrajzok összetevőinek olvasása |
> | Műveletek | Microsoft. Blueprint/tervezetek/verziók/törlés | Bármilyen tervrajz törlése |
> | Műveletek | Microsoft. Blueprint/tervezetek/verziók/olvasás | Bármilyen tervrajz beolvasása |
> | Műveletek | Microsoft. Blueprint/tervezetek/verziók/írás | Bármilyen tervrajz létrehozása vagy frissítése |
> | Műveletek | Microsoft. Blueprint/tervezetek/írás | Bármilyen tervrajz létrehozása vagy frissítése |
> | Műveletek | Microsoft. Blueprint/regisztráció/művelet | Regisztrálja az Azure-tervrajzok erőforrás-szolgáltatóját |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. BotService/botServices/csatornák/törlés | Bot-szolgáltatás törlése |
> | Műveletek | Microsoft. BotService/botServices/csatornák/olvasás | Robot szolgáltatás beolvasása |
> | Műveletek | Microsoft. BotService/botServices/csatornák/írás | Bot-szolgáltatás írása |
> | Műveletek | Microsoft. BotService/botServices/Connections/delete | Bot-szolgáltatás törlése |
> | Műveletek | Microsoft. BotService/botServices/Connections/READ | Robot szolgáltatás beolvasása |
> | Műveletek | Microsoft. BotService/botServices/Connections/Write | Bot-szolgáltatás írása |
> | Műveletek | Microsoft. BotService/botServices/delete | Bot-szolgáltatás törlése |
> | Műveletek | Microsoft. BotService/botServices/READ | Robot szolgáltatás beolvasása |
> | Műveletek | Microsoft. BotService/botServices/Write | Bot-szolgáltatás írása |
> | Műveletek | Microsoft. BotService/Locations/operationresults/READ | Aszinkron művelet állapotának beolvasása |
> | Műveletek | Microsoft. BotService/Operations/READ | Az összes erőforrástípus műveleteinek beolvasása |

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. cache/checknameavailability/művelet | Ellenőrzi, hogy a név elérhető-e új Redis Cache |
> | Műveletek | Microsoft. cache/Locations/operationresults/READ | Egy hosszú ideig futó művelet eredményének beolvasása, amelyre a "location" fejlécet korábban visszaadották az ügyfélnek |
> | Műveletek | Microsoft. cache/Operations/READ | A "Microsoft. cache" szolgáltató által támogatott műveletek felsorolása. |
> | Műveletek | Microsoft. cache/Redis/delete | A teljes Redis Cache törlése |
> | Műveletek | Microsoft. cache/Redis/export/művelet | Redis-adatforrások exportálása előre rögzített tárolási blobokra megadott formátumban |
> | Műveletek | Microsoft. cache/Redis/firewallRules/delete | Egy Redis Cache IP-tűzfalszabály-szabályainak törlése |
> | Műveletek | Microsoft. cache/Redis/firewallRules/READ | Egy Redis Cache IP-tűzfalszabályok beszerzése |
> | Műveletek | Microsoft. cache/Redis/firewallRules/Write | Egy Redis Cache IP-tűzfalszabály-szabályainak szerkesztése |
> | Műveletek | Microsoft. cache/Redis/forceReboot/Action | Kényszerítse újra a gyorsítótár-példány újraindítását, ami adatvesztéssel jár. |
> | Műveletek | Microsoft. cache/Redis/importálás/művelet | Megadott formátumú adatok importálása több blobból a Redis-be |
> | Műveletek | Microsoft. cache/Redis/linkedservers/delete | Csatolt kiszolgáló törlése Redis Cache |
> | Műveletek | Microsoft. cache/Redis/linkedservers/READ | Redis-gyorsítótárhoz társított csatolt kiszolgálók beolvasása. |
> | Műveletek | Microsoft. cache/Redis/linkedservers/Write | Csatolt kiszolgáló hozzáadása Redis Cachehoz |
> | Műveletek | Microsoft. cache/Redis/Listkeys műveletének beolvasása/Action | Redis Cache hozzáférési kulcsok értékének megtekintése a felügyeleti portálon |
> | Műveletek | Microsoft. cache/Redis/listUpgradeNotifications/READ | A gyorsítótár-bérlő legújabb frissítési értesítéseinek listázása. |
> | Műveletek | Microsoft. cache/Redis/metricDefinitions/READ | Egy Redis Cache elérhető metrikáinak beolvasása |
> | Műveletek | Microsoft. cache/Redis/patchSchedules/delete | Redis Cache javítási ütemtervének törlése |
> | Műveletek | Microsoft. cache/Redis/patchSchedules/READ | Egy Redis Cache javítási ütemtervének beolvasása |
> | Műveletek | Microsoft. cache/Redis/patchSchedules/Write | Redis Cache javítási ütemtervének módosítása |
> | Műveletek | Microsoft. cache/Redis/READ | A Redis Cache beállításainak és konfigurációjának megtekintése a felügyeleti portálon |
> | Műveletek | Microsoft. cache/Redis/regenerateKey/Action | Redis Cache hozzáférési kulcsok értékének módosítása a felügyeleti portálon |
> | Műveletek | Microsoft. cache/Redis/Start/művelet | Indítsa el a gyorsítótár-példányt. |
> | Műveletek | Microsoft. cache/Redis/leállítás/művelet | Állítsa le a gyorsítótár-példányt. |
> | Műveletek | Microsoft. cache/Redis/Write | A Redis Cache beállításainak és konfigurációjának módosítása a felügyeleti portálon |
> | Műveletek | Microsoft. cache/regisztráció/művelet | Regisztrálja a Microsoft. cache erőforrás-szolgáltatót egy előfizetéssel |
> | Műveletek | Microsoft. cache/regisztráció/művelet | A Microsoft. cache erőforrás-szolgáltató regisztrációjának törlése előfizetéssel |

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Capacity/appliedreservations/olvasás | Összes foglalás olvasása |
> | Műveletek | Microsoft. Capacity/calculateexchange/művelet | Kiszámítja az új vásárlások árfolyamát és árát, és visszaadja a házirend-hibákat. |
> | Műveletek | Microsoft. Capacity/calculateprice/művelet | A foglalás árának kiszámítása |
> | Műveletek | Microsoft. Capacity/katalógusok/olvasás | Foglalási katalógus olvasása |
> | Műveletek | Microsoft. Capacity/checkoffers/művelet | Előfizetési ajánlatok keresése |
> | Műveletek | Microsoft. Capacity/checkscopes/művelet | Előfizetés keresése |
> | Műveletek | Microsoft. Capacity/commercialreservationorders/olvasás | Bármely Bérlőben létrehozott foglalási megrendelések beolvasása |
> | Műveletek | Microsoft. Capacity/Exchange/művelet | Bármilyen foglalás cseréje |
> | Műveletek | Microsoft. Capacity/Operations/READ | Bármilyen művelet beolvasása |
> | Műveletek | Microsoft. Capacity/regisztráció/művelet | Regisztrálja a kapacitás erőforrás-szolgáltatót, és lehetővé teszi a kapacitás-erőforrások létrehozását. |
> | Műveletek | Microsoft. Capacity/reservationorders/művelet | Bármely foglalás frissítése |
> | Műveletek | Microsoft. Capacity/reservationorders/availablescopes/művelet | Bármely elérhető hatókör megkeresése |
> | Műveletek | Microsoft. Capacity/reservationorders/calculaterefund/művelet | Kiszámítja a visszatérítés összegét és az új vásárlás árát, és visszaadja a házirend-hibákat. |
> | Műveletek | Microsoft. Capacity/reservationorders/delete | Foglalás törlése |
> | Műveletek | Microsoft. Capacity/reservationorders/egyesítés/művelet | Bármilyen foglalás egyesítése |
> | Műveletek | Microsoft. Capacity/reservationorders/olvasás | Összes foglalás olvasása |
> | Műveletek | Microsoft. Capacity/reservationorders/foglalások/művelet | Bármely foglalás frissítése |
> | Műveletek | Microsoft. Capacity/reservationorders/Reservations/availablescopes/Action | Bármely elérhető hatókör megkeresése |
> | Műveletek | Microsoft. Capacity/reservationorders/foglalások/törlés | Foglalás törlése |
> | Műveletek | Microsoft. Capacity/reservationorders/foglalások/olvasás | Összes foglalás olvasása |
> | Műveletek | Microsoft. Capacity/reservationorders/foglalások/változatok/olvasás | Összes foglalás olvasása |
> | Műveletek | Microsoft. Capacity/reservationorders/foglalások/írás | Foglalás létrehozása |
> | Műveletek | Microsoft. Capacity/reservationorders/Return/action | Bármilyen foglalás visszaküldése |
> | Műveletek | Microsoft. Capacity/reservationorders/felosztás/művelet | Foglalás felosztása |
> | Műveletek | Microsoft. Capacity/reservationorders/swap/művelet | Bármilyen foglalás cseréje |
> | Műveletek | Microsoft. Capacity/reservationorders/írás | Foglalás létrehozása |
> | Műveletek | Microsoft. Capacity/bérlők/regisztráció/művelet | Bármely bérlő regisztrálása |
> | Műveletek | Microsoft. Capacity/regisztráció/művelet | Bérlő regisztrációjának törlése |
> | Műveletek | Microsoft. Capacity/validatereservationorder/művelet | Bármely foglalás ellenőrzése |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. CDN/cdnwebapplicationfirewallmanagedrulesets/delete |  |
> | Műveletek | Microsoft. CDN/cdnwebapplicationfirewallmanagedrulesets/READ |  |
> | Műveletek | Microsoft. CDN/cdnwebapplicationfirewallmanagedrulesets/írás |  |
> | Műveletek | Microsoft. CDN/cdnwebapplicationfirewallpolicies/delete |  |
> | Műveletek | Microsoft. CDN/cdnwebapplicationfirewallpolicies/READ |  |
> | Műveletek | Microsoft. CDN/cdnwebapplicationfirewallpolicies/írás |  |
> | Műveletek | Microsoft. CDN/CheckNameAvailability/művelet |  |
> | Műveletek | Microsoft. CDN/CheckResourceUsage/művelet |  |
> | Műveletek | Microsoft. CDN/edgenodes/delete |  |
> | Műveletek | Microsoft. CDN/edgenodes/READ |  |
> | Műveletek | Microsoft. CDN/edgenodes/írás |  |
> | Műveletek | Microsoft. CDN/operationresults/cdnwebapplicationfirewallpolicyresults/delete |  |
> | Műveletek | Microsoft. CDN/operationresults/cdnwebapplicationfirewallpolicyresults/READ |  |
> | Műveletek | Microsoft. CDN/operationresults/cdnwebapplicationfirewallpolicyresults/írás |  |
> | Műveletek | Microsoft. CDN/operationresults/delete |  |
> | Műveletek | Microsoft. CDN/operationresults/profileresults/CheckResourceUsage/művelet |  |
> | Műveletek | Microsoft. CDN/operationresults/profileresults/delete |  |
> | Műveletek | Microsoft. CDN/operationresults/profileresults/endpointresults/CheckResourceUsage/művelet |  |
> | Műveletek | Microsoft. CDN/operationresults/profileresults/endpointresults/customdomainresults/delete |  |
> | Műveletek | Microsoft. CDN/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/Action |  |
> | Műveletek | Microsoft. CDN/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/Action |  |
> | Műveletek | Microsoft. CDN/operationresults/profileresults/endpointresults/customdomainresults/READ |  |
> | Műveletek | Microsoft. CDN/operationresults/profileresults/endpointresults/customdomainresults/írás |  |
> | Műveletek | Microsoft. CDN/operationresults/profileresults/endpointresults/delete |  |
> | Műveletek | Microsoft. CDN/operationresults/profileresults/endpointresults/Load/művelet |  |
> | Műveletek | Microsoft. CDN/operationresults/profileresults/endpointresults/originresults/delete |  |
> | Műveletek | Microsoft. CDN/operationresults/profileresults/endpointresults/originresults/READ |  |
> | Műveletek | Microsoft. CDN/operationresults/profileresults/endpointresults/originresults/írás |  |
> | Műveletek | Microsoft. CDN/operationresults/profileresults/endpointresults/Purge/művelet |  |
> | Műveletek | Microsoft. CDN/operationresults/profileresults/endpointresults/READ |  |
> | Műveletek | Microsoft. CDN/operationresults/profileresults/endpointresults/Start/művelet |  |
> | Műveletek | Microsoft. CDN/operationresults/profileresults/endpointresults/leállítás/művelet |  |
> | Műveletek | Microsoft. CDN/operationresults/profileresults/endpointresults/ValidateCustomDomain/művelet |  |
> | Műveletek | Microsoft. CDN/operationresults/profileresults/endpointresults/Write |  |
> | Műveletek | Microsoft. CDN/operationresults/profileresults/GenerateSsoUri/művelet |  |
> | Műveletek | Microsoft. CDN/operationresults/profileresults/GetSupportedOptimizationTypes/művelet |  |
> | Műveletek | Microsoft. CDN/operationresults/profileresults/READ |  |
> | Műveletek | Microsoft. CDN/operationresults/profileresults/írás |  |
> | Műveletek | Microsoft. CDN/operationresults/READ |  |
> | Műveletek | Microsoft. CDN/operationresults/írás |  |
> | Műveletek | Microsoft. CDN/Operations/READ |  |
> | Műveletek | Microsoft. CDN/profilok/CheckResourceUsage/művelet |  |
> | Műveletek | Microsoft. CDN/profilok/törlés |  |
> | Műveletek | Microsoft. CDN/profilok/végpontok/CheckResourceUsage/művelet |  |
> | Műveletek | Microsoft. CDN/profilok/végpontok/customdomains/delete |  |
> | Műveletek | Microsoft. CDN/profilok/végpontok/customdomains/DisableCustomHttps/művelet |  |
> | Műveletek | Microsoft. CDN/profilok/végpontok/customdomains/EnableCustomHttps/művelet |  |
> | Műveletek | Microsoft. CDN/profilok/végpontok/customdomains/olvasás |  |
> | Műveletek | Microsoft. CDN/profilok/végpontok/customdomains/írás |  |
> | Műveletek | Microsoft. CDN/profilok/végpontok/törlés |  |
> | Műveletek | Microsoft. CDN/profilok/végpontok/betöltés/művelet |  |
> | Műveletek | Microsoft. CDN/profilok/végpontok/eredet/törlés |  |
> | Műveletek | Microsoft. CDN/profilok/végpontok/eredet/olvasás |  |
> | Műveletek | Microsoft. CDN/profilok/végpontok/eredet/írás |  |
> | Műveletek | Microsoft. CDN/profilok/végpontok/kiürítés/művelet |  |
> | Műveletek | Microsoft. CDN/profilok/végpontok/olvasás |  |
> | Műveletek | Microsoft. CDN/profilok/végpontok/indítás/művelet |  |
> | Műveletek | Microsoft. CDN/profilok/végpontok/leállítás/művelet |  |
> | Műveletek | Microsoft. CDN/profilok/végpontok/ValidateCustomDomain/művelet |  |
> | Műveletek | Microsoft. CDN/profilok/végpontok/írás |  |
> | Műveletek | Microsoft. CDN/profilok/GenerateSsoUri/művelet |  |
> | Műveletek | Microsoft. CDN/profilok/GetSupportedOptimizationTypes/művelet |  |
> | Műveletek | Microsoft. CDN/profilok/olvasás |  |
> | Műveletek | Microsoft. CDN/profilok/írás |  |
> | Műveletek | Microsoft. CDN/regisztráció/művelet | Regisztrálja az előfizetést a CDN erőforrás-szolgáltató számára, és lehetővé teszi a CDN-profilok létrehozását. |
> | Műveletek | Microsoft. CDN/ValidateProbe/művelet |  |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. CertificateRegistration/Tanúsítványrendelések/tanúsítványok/törlés | Meglévő tanúsítvány törlése |
> | Műveletek | Microsoft. CertificateRegistration/Tanúsítványrendelések/tanúsítványok/olvasás | A tanúsítványok listájának beolvasása |
> | Műveletek | Microsoft. CertificateRegistration/Tanúsítványrendelések/tanúsítványok/írás | Új tanúsítvány hozzáadása vagy meglévő frissítése |
> | Műveletek | Microsoft. CertificateRegistration/Tanúsítványrendelések/delete | Meglévő AppServiceCertificate törlése |
> | Műveletek | Microsoft. CertificateRegistration/Tanúsítványrendelések/READ | A Tanúsítványrendelések listájának beolvasása |
> | Műveletek | Microsoft. CertificateRegistration/Tanúsítványrendelések/újrakibocsátás/művelet | Meglévő tanúsítványrendelési újrakibocsátása |
> | Műveletek | Microsoft. CertificateRegistration/Tanúsítványrendelések/megújítás/művelet | Meglévő tanúsítványrendelési megújítása |
> | Műveletek | Microsoft. CertificateRegistration/Tanúsítványrendelések/resendEmail/művelet | Tanúsítvány e-mail újraküldése |
> | Műveletek | Microsoft. CertificateRegistration/Tanúsítványrendelések/resendRequestEmails/művelet | Kérelem e-mail-címének újraküldése egy másik e-mail-címre |
> | Műveletek | Microsoft. CertificateRegistration/Tanúsítványrendelések/resendRequestEmails/művelet | Hely zárolásának beolvasása egy kiállított App Service-tanúsítvány |
> | Műveletek | Microsoft. CertificateRegistration/Tanúsítványrendelések/retrieveCertificateActions/művelet | A tanúsítványkérelmek listájának beolvasása |
> | Műveletek | Microsoft. CertificateRegistration/Tanúsítványrendelések/retrieveEmailHistory/művelet | Tanúsítvány e-mail-előzményeinek beolvasása |
> | Műveletek | Microsoft. CertificateRegistration/Tanúsítványrendelések/verifyDomainOwnership/művelet | Tartomány tulajdonjogának ellenőrzése |
> | Műveletek | Microsoft. CertificateRegistration/Tanúsítványrendelések/Write | Új Tanúsítványrendelési hozzáadása vagy meglévő frissítése |
> | Műveletek | Microsoft. CertificateRegistration/Operations/READ | Az App Service-tanúsítvány regisztrációjának összes műveletének listázása |
> | Műveletek | Microsoft. CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/művelet | Egyszerű szolgáltatásnév kiépítése a Service app számára |
> | Műveletek | Microsoft. CertificateRegistration/regisztráció/művelet | Regisztrálja az előfizetéshez tartozó Microsoft-tanúsítványok erőforrás-szolgáltatóját |
> | Műveletek | Microsoft. CertificateRegistration/validateCertificateRegistrationInformation/művelet | Tanúsítvány-megvásárlási objektum ellenőrzése az elküldése nélkül |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. ClassicCompute/képességek/olvasás | Megjeleníti a képességeket |
> | Műveletek | Microsoft. ClassicCompute/checkDomainNameAvailability/művelet | Ellenőrzi egy adott tartománynév rendelkezésre állását. |
> | Műveletek | Microsoft. ClassicCompute/checkDomainNameAvailability/READ | Egy adott tartománynév elérhetőségének beolvasása. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/aktív/írás | Beállítja az aktív tartománynevet. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/availabilitySets/olvasás | Az erőforrás rendelkezésre állási csoportjának megjelenítése. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/képességek/olvasás | A tartománynév funkcióinak megjelenítése |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/törlés | Távolítsa el az erőforrások tartománynevét. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/deploymentslots/olvasás | Megjeleníti a telepítési tárolóhelyeket. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/deploymentslots/szerepkörök/olvasás | Szerepkör beszerzése a tartománynév üzembe helyezési pontján |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/deploymentslots/roles/roleinstances/READ | Szerepkör-példány beszerzése a szerepkörhöz a tartománynév üzembe helyezési tárolóhelyén |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/deploymentslots/állapot/olvasás | Az üzembe helyezési pont állapotának beolvasása. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/deploymentslots/állam/írás | Adja hozzá az üzembe helyezési pont állapotát. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/deploymentslots/upgradedomain/READ | Frissítési tartomány beolvasása az üzembe helyezési ponthoz a tartománynév alapján |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/deploymentslots/upgradedomain/írás | Frissítési tartomány frissítése a tartománynév üzembe helyezési nyílásába |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/deploymentslots/írás | Létrehozza vagy frissíti az üzemelő példányt. |
> | Műveletek | Microsoft. ClassicCompute/tartománynevek/bővítmények/törlés | Távolítsa el a tartománynevek kiterjesztéseit. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/kiterjesztés/operationStatuses/olvasás | A tartománynév-kiterjesztések műveleti állapotának beolvasása. |
> | Műveletek | Microsoft. ClassicCompute/tartománynevek/bővítmények/olvasás | A tartománynév-kiterjesztéseket adja vissza. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/bővítmény/írás | Adja hozzá a tartománynevek kiterjesztéseit. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/internalLoadBalancers/delete | Új belső terheléselosztási egyenleg eltávolítása. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/internalLoadBalancers/operationStatuses/READ | Beolvassa a tartománynevek belső terheléselosztó műveleti állapotát. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/internalLoadBalancers/olvasás | A belső terheléselosztó beolvasása. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/internalLoadBalancers/írás | Létrehoz egy új belső terheléselosztást. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/loadBalancedEndpointSets/operationStatuses/READ | Beolvassa a tartománynevek elosztott terhelésű végpontjának műveleti állapotát. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/loadBalancedEndpointSets/olvasás | Az elosztott terhelésű végpontok beolvasása. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/loadBalancedEndpointSets/írás | Adja hozzá a elosztott terhelésű végpont készletet. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/operationstatuses/olvasás | A tartománynév műveleti állapotának beolvasása. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/operationStatuses/olvasás | A tartománynév-kiterjesztések műveleti állapotának beolvasása. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/olvasás | Erőforrások tartománynevének visszaadása. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/serviceCertificates/delete | Törölje a használt szolgáltatási tanúsítványokat. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/serviceCertificates/operationStatuses/READ | A tartománynevek szolgáltatási tanúsítványainak műveleti állapotának beolvasása. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/serviceCertificates/olvasás | A használt szolgáltatási tanúsítványokat adja vissza. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/serviceCertificates/írás | Adja hozzá vagy módosítsa a használt szolgáltatási tanúsítványokat. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/bővítőhely/abortMigration/művelet | Egy üzembe helyezési pont áttelepítését szakítja meg. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/bővítőhely/commitMigration/művelet | Egy üzembe helyezési pont áttelepítését véglegesíti. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/bővítőhely/törlés | Egy adott üzembe helyezési pont törlése. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/bővítőhely/operationStatuses/olvasás | A tartománynevek tárolóhelyek műveleti állapotának beolvasása. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/bővítőhely/prepareMigration/művelet | Előkészíti az üzembe helyezési pont áttelepítését. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/bővítőhely/olvasás | Megjeleníti a telepítési tárolóhelyeket. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök/extensionReferences/delete | Távolítsa el az üzembe helyezési pont szerepkörének kiterjesztési hivatkozását. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök/extensionReferences/operationStatuses/READ | Beolvassa a tartománynevek tárolóhelyek szerepkör-bővítmény hivatkozásainak műveleti állapotát. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök/extensionReferences/olvasás | Az üzembe helyezési pont szerepkörének kiterjesztési hivatkozását adja vissza. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök/extensionReferences/írás | Adja hozzá vagy módosítsa az üzembe helyezési pont szerepkörének kiterjesztési hivatkozását. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök/metricdefinitions/olvasás | A tartománynévhez tartozó szerepkör-metrika definíciójának beolvasása. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök/mérőszámok/olvasás | A tartománynév szerepkör-metrikájának beolvasása. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök/operationstatuses/olvasás | A tartománynevek tárolóhelye szerepkör műveleti állapotának beolvasása. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök/szolgáltatók/Microsoft. bepillantást/diagnosticSettings/READ | A diagnosztikai beállítások beolvasása. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök/szolgáltatók/Microsoft. bepillantások/diagnosticSettings/írás | Diagnosztikai beállítások hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök/szolgáltatók/Microsoft. bepillantást/metricDefinitions/READ | A metrikák definícióinak beolvasása. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök/olvasás | Az üzembe helyezési pont szerepkörének beolvasása. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök/roleInstances/downloadremotedesktopconnectionfile/művelet | Letölti a távoli asztali kapcsolati fájlt a szerepkör-példányhoz a tartománynév tárolóhelye szerepkörben. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök/roleInstances/operationStatuses/READ | Beolvassa a szerepkör-példány műveleti állapotát a tartománynevek tárolóhelye szerepkörben. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök/roleInstances/olvasás | A szerepkör-példány beolvasása. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök/roleInstances/Újraépítés/művelet | Újraépíti a szerepkör-példányt. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök/roleInstances/rendszerkép/művelet | A szerepkör-példány rendszerképének alaphelyzetbe állítása. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök/roleInstances/újraindítás/művelet | Újraindítja a szerepkör-példányokat. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök/SKU/READ | Szerepkör-SKU beszerzése az üzembe helyezési ponthoz. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök/írás | Szerepkör hozzáadása az üzembe helyezési ponthoz. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/bővítőhely/indítás/művelet | Elindítja a telepítési tárolóhelyet. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/bővítőhely/állapot/indítás/írás | Az üzembe helyezési pont állapota leállítva értékre változik. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/bővítőhely/állapot/leállítás/írás | Az üzembe helyezési pont állapotának elindítását módosítja. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/bővítőhely/leállítás/művelet | Felfüggeszti az üzembe helyezési pontot. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/bővítőhely/upgradeDomain/írás | Menjen a tartomány frissítéséhez. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/bővítőhely/validateMigration/művelet | Egy üzembe helyezési pont áttelepítésének ellenőrzése. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/bővítőhely/írás | Létrehozza vagy frissíti az üzemelő példányt. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/swap/művelet | Kicseréli az átmeneti tárolóhelyet az üzemi tárolóhelyre. |
> | Műveletek | Microsoft. ClassicCompute/tartománynév/írás | Erőforrások tartománynevének hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. ClassicCompute/moveSubscriptionResources/művelet | Helyezze át az összes klasszikus erőforrást egy másik előfizetésbe. |
> | Műveletek | Microsoft. ClassicCompute/operatingSystemFamilies/READ | Felsorolja a Microsoft Azureban elérhető vendég operációsrendszer-családokat, valamint felsorolja az egyes családokhoz elérhető operációsrendszer-verziókat is. |
> | Műveletek | Microsoft. ClassicCompute/operatingSystems/READ | A Microsoft Azure jelenleg elérhető vendég operációs rendszer verzióit sorolja fel. |
> | Műveletek | Microsoft. ClassicCompute/Operations/READ | Gets the list of operations. |
> | Műveletek | Microsoft. ClassicCompute/operationStatuses/READ | Reads the operation status for the resource. |
> | Műveletek | Microsoft. ClassicCompute/kvóták/olvasás | Az előfizetés kvótájának beolvasása. |
> | Műveletek | Microsoft. ClassicCompute/regisztráció/művelet | Regisztrálás a klasszikus számítási feladatokba |
> | Műveletek | Microsoft. ClassicCompute/resourceTypes/SKU/READ | Beolvassa a támogatott erőforrástípusok SKU-listáját. |
> | Műveletek | Microsoft. ClassicCompute/validateSubscriptionMoveAvailability/művelet | Érvényesítse az előfizetés rendelkezésre állását a klasszikus áthelyezési művelethez. |
> | Műveletek | Microsoft. ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | A virtuális géphez társított hálózati biztonsági csoport törlése. |
> | Műveletek | Microsoft. ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/READ | A virtuális gépek társított hálózati biztonsági csoportjai műveleti állapotának beolvasása. |
> | Műveletek | Microsoft. ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/READ | A virtuális géphez társított hálózati biztonsági csoport beolvasása. |
> | Műveletek | Microsoft. ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/Write | Hozzáadja a virtuális géphez társított hálózati biztonsági csoportot. |
> | Műveletek | Microsoft. ClassicCompute/virtualMachines/asyncOperations/READ | A lehetséges aszinkron műveletek beolvasása |
> | Műveletek | Microsoft. ClassicCompute/virtualMachines/attachDisk/művelet | Adatlemez csatolása egy virtuális géphez. |
> | Műveletek | Microsoft. ClassicCompute/virtualMachines/Capture/Action | Virtuális gép rögzítése. |
> | Műveletek | Microsoft. ClassicCompute/virtualMachines/delete | Eltávolítja a virtuális gépeket. |
> | Műveletek | Microsoft. ClassicCompute/virtualMachines/detachDisk/művelet | Adatlemez leválasztása a virtuális gépről. |
> | Műveletek | Microsoft. ClassicCompute/virtualMachines/diagnosticsettings/READ | Virtuális gép diagnosztikai beállításainak beolvasása. |
> | Műveletek | Microsoft. ClassicCompute/virtualMachines/lemezek/olvasás | Adatlemezek listájának lekérése |
> | Műveletek | Microsoft. ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/művelet | Letölti a virtuális gép RDP-fájlját. |
> | Műveletek | Microsoft. ClassicCompute/virtualMachines/Extensions/operationStatuses/READ | A virtuálisgép-bővítmények műveleti állapotának beolvasása. |
> | Műveletek | Microsoft. ClassicCompute/virtualMachines/Extensions/READ | Lekéri a virtuálisgép-bővítményt. |
> | Műveletek | Microsoft. ClassicCompute/virtualMachines/Extensions/Write | A virtuálisgép-bővítményt helyezi át. |
> | Műveletek | Microsoft. ClassicCompute/virtualMachines/metricdefinitions/READ | A virtuális gép metrikai definíciójának beolvasása. |
> | Műveletek | Microsoft. ClassicCompute/virtualMachines/metrika/olvasás | A metrikák beolvasása. |
> | Műveletek | Microsoft. ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | A hálózati adapterhez társított hálózati biztonsági csoport törlése. |
> | Műveletek | Microsoft. ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/READ | A virtuális gépek társított hálózati biztonsági csoportjai műveleti állapotának beolvasása. |
> | Műveletek | Microsoft. ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/READ | A hálózati adapterhez társított hálózati biztonsági csoport beolvasása. |
> | Műveletek | Microsoft. ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/Write | Hozzáadja a hálózati adapterhez társított hálózati biztonsági csoportot. |
> | Műveletek | Microsoft. ClassicCompute/virtualMachines/operationStatuses/READ | A virtuális gépek műveleti állapotának beolvasása. |
> | Műveletek | Microsoft. ClassicCompute/virtualMachines/performMaintenance/művelet | Karbantartást végez a virtuális gépen. |
> | Műveletek | Microsoft. ClassicCompute/virtualMachines/Providers/Microsoft. bepillantást/diagnosticSettings/READ | A diagnosztikai beállítások beolvasása. |
> | Műveletek | Microsoft. ClassicCompute/virtualMachines/Providers/Microsoft. bepillantások/diagnosticSettings/írás | Diagnosztikai beállítások hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. ClassicCompute/virtualMachines/Providers/Microsoft. bepillantást/metricDefinitions/READ | A metrikák definícióinak beolvasása. |
> | Műveletek | Microsoft. ClassicCompute/virtualMachines/READ | A virtuális gépek listájának beolvasása. |
> | Műveletek | Microsoft. ClassicCompute/virtualMachines/újratelepítés/művelet | A virtuális gép újbóli üzembe helyezése. |
> | Műveletek | Microsoft. ClassicCompute/virtualMachines/újraindítás/művelet | Újraindítja a virtuális gépeket. |
> | Műveletek | Microsoft. ClassicCompute/virtualMachines/leállítás/művelet | Állítsa le a virtuális gépet. |
> | Műveletek | Microsoft. ClassicCompute/virtualMachines/Start/művelet | Indítsa el a virtuális gépet. |
> | Műveletek | Microsoft. ClassicCompute/virtualMachines/leállítás/művelet | Leállítja a virtuális gépet. |
> | Műveletek | Microsoft. ClassicCompute/virtualMachines/Write | Add or modify virtual machines. |

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. ClassicNetwork/expressroutecrossconnections/operationstatuses/READ | Expressz útvonalon keresztüli kapcsolatok állapotának beolvasása. |
> | Műveletek | Microsoft. ClassicNetwork/expressroutecrossconnections/társak/törlés | Express Route-kapcsolatok közötti társítás törlése |
> | Műveletek | Microsoft. ClassicNetwork/expressroutecrossconnections/társak/operationstatuses/olvasás | Expressz útvonal keresztben való összekapcsolási műveleti állapotának beolvasása. |
> | Műveletek | Microsoft. ClassicNetwork/expressroutecrossconnections/társak/olvasás | Expressz útvonalon keresztüli kapcsolatok összekapcsolásának beolvasása. |
> | Műveletek | Microsoft. ClassicNetwork/expressroutecrossconnections/társak/írás | Express Route-kapcsolatok közötti társítás hozzáadása. |
> | Műveletek | Microsoft. ClassicNetwork/expressroutecrossconnections/READ | Expressz útvonal-kereszt kapcsolatok beolvasása. |
> | Műveletek | Microsoft. ClassicNetwork/expressroutecrossconnections/Write | Express Route-kereszt kapcsolatainak hozzáadása |
> | Műveletek | Microsoft. ClassicNetwork/gatewaySupportedDevices/READ | A támogatott eszközök listájának beolvasása. |
> | Műveletek | Microsoft. ClassicNetwork/networkSecurityGroups/delete | A hálózati biztonsági csoport törlése. |
> | Műveletek | Microsoft. ClassicNetwork/networkSecurityGroups/operationStatuses/READ | A hálózati biztonsági csoport műveleti állapotának beolvasása. |
> | Műveletek | Microsoft. ClassicNetwork/networksecuritygroups/Providers/Microsoft. bepillantást/diagnosticSettings/READ | A hálózati biztonsági csoportok diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft. ClassicNetwork/networksecuritygroups/Providers/Microsoft. bepillantások/diagnosticSettings/írás | A hálózati biztonsági csoportok diagnosztikai beállításainak létrehozása vagy frissítése, ezt a műveletet az információk erőforrás-szolgáltatója egészíti ki. |
> | Műveletek | Microsoft. ClassicNetwork/networksecuritygroups/Providers/Microsoft. bepillantást/logDefinitions/READ | A hálózati biztonsági csoport eseményeinek beolvasása |
> | Műveletek | Microsoft. ClassicNetwork/networkSecurityGroups/READ | A hálózati biztonsági csoport beolvasása. |
> | Műveletek | Microsoft. ClassicNetwork/networkSecurityGroups/securityRules/delete | A biztonsági szabály törlése. |
> | Műveletek | Microsoft. ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/READ | A hálózati biztonsági csoport biztonsági szabályai műveleti állapotának beolvasása. |
> | Műveletek | Microsoft. ClassicNetwork/networkSecurityGroups/securityRules/READ | A biztonsági szabály beolvasása. |
> | Műveletek | Microsoft. ClassicNetwork/networkSecurityGroups/securityRules/Write | Egy biztonsági szabály hozzáadására vagy frissítésére szolgál. |
> | Műveletek | Microsoft. ClassicNetwork/networkSecurityGroups/Write | Új hálózati biztonsági csoport hozzáadására szolgál. |
> | Műveletek | Microsoft. ClassicNetwork/Operations/READ | Klasszikus hálózati műveletek beolvasása. |
> | Műveletek | Microsoft. ClassicNetwork/kvóták/olvasás | Az előfizetés kvótájának beolvasása. |
> | Műveletek | Microsoft. ClassicNetwork/regisztráció/művelet | Regisztrálás a klasszikus hálózatra |
> | Műveletek | Microsoft. ClassicNetwork/reservedIps/delete | Fenntartott IP-cím törlése. |
> | Műveletek | Microsoft. ClassicNetwork/reservedIps/JOIN/Action | Csatlakozás fenntartott IP-címhez |
> | Műveletek | Microsoft. ClassicNetwork/reservedIps/link/művelet | Fenntartott IP-cím csatolása |
> | Műveletek | Microsoft. ClassicNetwork/reservedIps/operationStatuses/READ | A fenntartott IP-címek műveleti állapotának beolvasása. |
> | Műveletek | Microsoft. ClassicNetwork/reservedIps/READ | A fenntartott IP-címek beolvasása |
> | Műveletek | Microsoft. ClassicNetwork/reservedIps/Write | Új fenntartott IP-cím hozzáadása |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/abortMigration/művelet | Megszakítja egy Virtual Network áttelepítését |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/képességek/olvasás | Megjeleníti a képességeket |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/checkIPAddressAvailability/művelet | Egy adott IP-cím rendelkezésre állását ellenőrzi egy virtuális hálózaton. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/commitMigration/művelet | Egy Virtual Network áttelepítését véglegesíti |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/delete | Törli a virtuális hálózatot. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/Gateway/clientRevokedCertificates/delete | Ügyféltanúsítvány visszavonásának megszüntetése. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/Gateway/clientRevokedCertificates/READ | A visszavont Ügyféltanúsítványok beolvasása. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/Gateway/clientRevokedCertificates/Write | Ügyféltanúsítvány visszavonása. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/Gateway/clientRootCertificates/delete | Törli a virtuális hálózati átjáró ügyféltanúsítványt. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/Gateways/clientRootCertificates/Letöltés/művelet | Tanúsítvány letöltése ujjlenyomat alapján. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/Gateways/clientRootCertificates/listPackage/művelet | Felsorolja a virtuális hálózati átjáró tanúsítványainak csomagját. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/Gateway/clientRootCertificates/READ | Keresse meg az ügyfél főtanúsítványait. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/Gateway/clientRootCertificates/Write | Feltölt egy új ügyfél főtanúsítványát. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/átjárók/kapcsolatok/csatlakozás/művelet | Egy hely és hely közötti átjáró kapcsolatának csatlakoztatása. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/átjárók/kapcsolatok/kapcsolat bontása/művelet | A hely és a hely közötti átjáró kapcsolatának leválasztása. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/átjárók/kapcsolatok/olvasás | A kapcsolatok listájának beolvasása. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/átjárók/kapcsolatok/teszt/művelet | Helyek közötti átjáró kapcsolatának tesztelése. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/átjárók/törlés | Törli a virtuális hálózati átjárót. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/átjárók/downloadDeviceConfigurationScript/művelet | Letölti az eszköz konfigurációs parancsfájlját. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/átjárók/downloadDiagnostics/művelet | Az átjáró-diagnosztika letöltése. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/átjárók/listCircuitServiceKey/művelet | Az áramköri szolgáltatás kulcsának beolvasása. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/átjárók/listPackage/művelet | Felsorolja a virtuális hálózati átjáró csomagját. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/Gateway/operationStatuses/READ | A virtuális hálózati átjárók műveleti állapotának beolvasása. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/átjárók/csomagok/olvasás | A virtuális hálózati átjáró csomagjának beolvasása. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/átjárók/olvasás | Lekéri a virtuális hálózati átjárókat. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/átjárók/startDiagnostics/művelet | Elindítja a virtuális hálózati átjáró diagnosztikai szolgáltatását. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/átjárók/stopDiagnostics/művelet | Leállítja a virtuális hálózati átjáró diagnosztikai szolgáltatását. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/átjárók/írás | Egy virtuális hálózati átjáró hozzáadására szolgál. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/JOIN/Action | Csatlakozik a virtuális hálózathoz. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/operationStatuses/READ | A virtuális hálózatok műveleti állapotának beolvasása. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/társ/művelet | A virtuális hálózatot egy másik virtuális hálózattal társítja. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/prepareMigration/művelet | Egy Virtual Network áttelepítésének előkészítése |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/READ | Szerezze be a virtuális hálózatot. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | Törli a távoli virtuális hálózat társ-felügyeleti proxyját. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/READ | Lekéri a távoli virtuális hálózati társ-felügyeleti proxyt. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/Write | Hozzáadja vagy frissíti a távoli virtuális hálózat társ-felügyeleti proxyját. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/alhálózat/associatedNetworkSecurityGroups/delete | Az alhálózathoz társított hálózati biztonsági csoport törlése. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/Subnets/associatedNetworkSecurityGroups/operationStatuses/READ | A virtuális hálózati alhálózat társított hálózati biztonsági csoport műveleti állapotának beolvasása. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/alhálózatok/associatedNetworkSecurityGroups/olvasás | Az alhálózathoz társított hálózati biztonsági csoport beolvasása. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/alhálózatok/associatedNetworkSecurityGroups/írás | Hozzáadja az alhálózathoz társított hálózati biztonsági csoportot. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/validateMigration/művelet | Egy Virtual Network áttelepítésének ellenőrzése |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/virtualNetworkPeerings/READ | A virtuális hálózat társításának beolvasása. |
> | Műveletek | Microsoft. ClassicNetwork/virtualNetworks/Write | Új virtuális hálózat hozzáadása. |

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. ClassicStorage/képességek/olvasás | Megjeleníti a képességeket |
> | Műveletek | Microsoft. ClassicStorage/checkStorageAccountAvailability/művelet | A Storage-fiók rendelkezésre állását ellenőrzi. |
> | Műveletek | Microsoft. ClassicStorage/checkStorageAccountAvailability/READ | A Storage-fiók rendelkezésre állásának beolvasása. |
> | Műveletek | Microsoft. ClassicStorage/lemezek/olvasás | A Storage-fiók lemezét adja vissza. |
> | Műveletek | Microsoft. ClassicStorage/images/operationstatuses/READ | Beolvassa a rendszerkép műveleti állapotát. |
> | Műveletek | Microsoft. ClassicStorage/images/READ | A képet adja vissza. |
> | Műveletek | Microsoft. ClassicStorage/Operations/READ | Klasszikus tárolási műveletek beolvasása |
> | Műveletek | Microsoft. ClassicStorage/osImages/READ | Az operációs rendszer rendszerképét adja vissza. |
> | Műveletek | Microsoft. ClassicStorage/osPlatformImages/READ | Lekéri az operációs rendszer platformjának rendszerképét. |
> | Műveletek | Microsoft. ClassicStorage/publicImages/READ | A nyilvános virtuális gép rendszerképének beolvasása. |
> | Műveletek | Microsoft. ClassicStorage/kvóták/olvasás | Az előfizetés kvótájának beolvasása. |
> | Műveletek | Microsoft. ClassicStorage/regisztráció/művelet | Regisztrálás a klasszikus tárolóba |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/abortMigration/művelet | Megszakítja egy Storage-fiók áttelepítését. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/blobServices/Providers/Microsoft. bepillantást/diagnosticSettings/READ | A diagnosztikai beállítások beolvasása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/blobServices/Providers/Microsoft. bepillantást/diagnosticSettings/Write | Diagnosztikai beállítások hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/blobServices/Providers/Microsoft. bepillantást/metricDefinitions/READ | A metrikák definícióinak beolvasása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/commitMigration/művelet | Egy Storage-fiók áttelepítését véglegesíti. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/delete | Törölje a Storage-fiókot. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/lemezek/törlés | Egy adott Storage-fiók lemezének törlése. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/Disks/operationStatuses/READ | Reads the operation status for the resource. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/lemezek/olvasás | A Storage-fiók lemezét adja vissza. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/lemezek/írás | Egy Storage-fiók lemezét adja meg. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/fileServices/Providers/Microsoft. bepillantást/diagnosticSettings/READ | A diagnosztikai beállítások beolvasása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/fileServices/Providers/Microsoft. bepillantást/diagnosticSettings/Write | Diagnosztikai beállítások hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/fileServices/Providers/Microsoft. bepillantást/metricDefinitions/READ | A metrikák definícióinak beolvasása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/images/delete | Egy adott Storage-fiók rendszerképének törlése. Elavult. A "Microsoft. ClassicStorage/storageAccounts/lemezképet" használata |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/images/operationstatuses/READ | A Storage-fiók rendszerképének műveleti állapotát adja vissza. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/images/READ | A Storage-fiók rendszerképét adja vissza. Elavult. A "Microsoft. ClassicStorage/storageAccounts/lemezképet" használata |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/Listkeys műveletének beolvasása/művelet | Felsorolja a Storage-fiókok hozzáférési kulcsait. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/operationStatuses/READ | Reads the operation status for the resource. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/osImages/delete | Egy adott tárolási fiók operációs rendszerének rendszerképét törli. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/osImages/READ | A Storage-fiók operációs rendszerének rendszerképét adja vissza. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/osImages/Write | Egy adott Storage-fiók operációs rendszerének rendszerképét adja meg. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/prepareMigration/művelet | A Storage-fiók áttelepítésének előkészítése. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. bepillantást/diagnosticSettings/READ | A diagnosztikai beállítások beolvasása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. bepillantások/diagnosticSettings/írás | Diagnosztikai beállítások hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. bepillantást/metricDefinitions/READ | A metrikák definícióinak beolvasása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/queueServices/Providers/Microsoft. bepillantást/diagnosticSettings/READ | A diagnosztikai beállítások beolvasása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/queueServices/Providers/Microsoft. bepillantást/diagnosticSettings/Write | Diagnosztikai beállítások hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/queueServices/Providers/Microsoft. bepillantást/metricDefinitions/READ | A metrikák definícióinak beolvasása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/READ | Adja vissza a Storage-fiókot az adott fiókkal. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/regenerateKey/művelet | Újragenerálja a Storage-fiók meglévő hozzáférési kulcsait. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/Services/diagnosticSettings/READ | A diagnosztikai beállítások beolvasása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/Services/diagnosticSettings/Write | Diagnosztikai beállítások hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/Services/metricDefinitions/READ | A metrikák definícióinak beolvasása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/Services/mérőszámok/olvasás | A metrikák beolvasása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/Services/READ | Szerezze be az elérhető szolgáltatásokat. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/tableServices/Providers/Microsoft. bepillantást/diagnosticSettings/READ | A diagnosztikai beállítások beolvasása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/tableServices/Providers/Microsoft. bepillantást/diagnosticSettings/Write | Diagnosztikai beállítások hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/tableServices/Providers/Microsoft. bepillantást/metricDefinitions/READ | A metrikák definícióinak beolvasása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/validateMigration/művelet | A Storage-fiók áttelepítésének ellenőrzése. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/lemezképet/delete | Egy adott virtuálisgép-rendszerkép törlése. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/lemezképet/operationstatuses/READ | Egy adott virtuálisgép-rendszerkép műveleti állapotának beolvasása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/lemezképet/READ | A virtuális gép rendszerképét adja vissza. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/lemezképet/Write | Egy adott virtuálisgép-rendszerkép hozzáadására szolgál. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/Write | Felvesz egy új Storage-fiókot. |
> | Műveletek | Microsoft. ClassicStorage/lemezképet/READ | A virtuálisgép-lemezképek felsorolása. |

## <a name="microsoftcognitiveservices"></a>Microsoft. CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | DataAction | Microsoft. CognitiveServices/accounts/AnomalyDetector/idősor/teljes/észlelés/művelet | A művelet egy teljes adatsorozatot használó modellt hoz létre, és az egyes pontokat ugyanazzal a modellel fogja észlelni.<br>Ezzel a módszerrel a rendszer egy bizonyos pont előtt és után pontokat használ annak megállapítására, hogy az anomália-e.<br>A teljes észlelés lehetővé teheti a felhasználónak az idősorozat teljes állapotát. |
> | DataAction | Microsoft. CognitiveServices/fiókok/AnomalyDetector/idősor/utolsó/észlelés/művelet | A művelet egy modellt hoz létre, amely a legújabb előtt pontokat használ. Ezzel a módszerrel csak a múltbeli pontok határozzák meg, hogy a célként megadott pont anomáli-e. A legutóbbi pont észlelése megfelel az üzleti metrikák valós idejű monitorozásának forgatókönyvének. |
> | DataAction | Microsoft. CognitiveServices/fiókok/automatikus javaslat/keresés/művelet | Ez a művelet javaslatokat nyújt egy adott lekérdezéshez vagy részleges lekérdezéshez. |
> | DataAction | Microsoft. CognitiveServices/fiókok/ComputerVision/elemzés/művelet | A művelet a képi tartalom alapján kibontja a vizualizációs funkciók gazdag készletét.  |
> | DataAction | Microsoft. CognitiveServices/accounts/ComputerVision/areaofinterest/Action | A művelet a rendszerkép legfontosabb területe körüli határoló mezőt adja vissza. |
> | DataAction | Microsoft. CognitiveServices/fiókok/ComputerVision/Leírás/művelet | Ezzel a művelettel egy, a teljes mondattal ellátott, emberi olvasási nyelvezettel rendelkező képet generál.<br> A leírás a művelet által is visszaadott tartalmi címkék gyűjteményén alapul.<br>Minden képhez több Leírás is létrehozható.<br> A leírásokat a megbízhatósági Pontszámuk alapján rendezi a rendszer.<br>Az összes Leírás angol nyelven van. |
> | DataAction | Microsoft. CognitiveServices/fiókok/ComputerVision/észlelés/művelet | A művelet végrehajtja az objektumok észlelését a megadott képen.  |
> | DataAction | Microsoft. CognitiveServices/accounts/ComputerVision/generatethumbnail/Action | A művelet a felhasználó által megadott szélességet és magasságot tartalmazó miniatűr képet hoz létre.<br> Alapértelmezés szerint a szolgáltatás elemzi a képet, azonosítja a releváns tartományt (ROI), és intelligens körbevágási koordinátákat hoz létre a tartomány alapján.<br> Az intelligens vágás segít, ha olyan oldalarányt ad meg, amely eltér a bemeneti képtől |
> | DataAction | Microsoft. CognitiveServices/fiókok/ComputerVision/modellek/elemzés/művelet | A művelet egy adott rendszerképen belül felismeri a tartalmat egy tartományszintű modell alkalmazásával.<br> A Computer Vision API által támogatott tartomány-specifikus modellek listája a/models GET kérelem használatával kérhető le.<br> Az API jelenleg a következő tartományszintű modelleket biztosítja: hírességek, tereptárgyak. |
> | DataAction | Microsoft. CognitiveServices/fiókok/ComputerVision/modellek/olvasás | Ez a művelet visszaadja a Computer Vision API által támogatott tartomány-specifikus modellek listáját.  Az API jelenleg a következő tartományszintű modelleket támogatja: Celebrity felismerő, Landmark felismerő. |
> | DataAction | Microsoft. CognitiveServices/accounts/ComputerVision/OCR/Action | Az optikai karakterfelismerés (OCR) észleli a képek szövegét, és Kinyeri a felismert karaktereket egy gépi használatú adatfolyamba.    |
> | DataAction | Microsoft. CognitiveServices/fiókok/ComputerVision/olvasás/elemzés/művelet | Ezzel a felülettel olvasási műveletet hajthat végre, amely az optikai karakterfelismerési (OCR) algoritmust használja, amely nagy mennyiségű dokumentumra optimalizált.<br>Képes kézzel írt, nyomtatott vagy kevert dokumentumokat kezelni.<br>Ha az olvasási felületet használja, a válasz egy "Operation-Location" nevű fejlécet tartalmaz.<br>A "művelet – hely" fejléc tartalmazza azt az URL-címet, amelyet a Get olvasási eredmény műveletéhez kell használni az OCR-eredmények eléréséhez. |
> | DataAction | Microsoft. CognitiveServices/fiókok/ComputerVision/olvasás/analyzeresults/olvasás | Ezzel a csatolóval lekérheti az olvasási művelet állapotát és OCR-eredményét.  A "operationId" értéket tartalmazó URL-címet a rendszer az olvasási művelet "Operation-Location" válasz fejlécében adja vissza. |
> | DataAction | Microsoft. CognitiveServices/accounts/ComputerVision/Read/Core/asyncbatchanalyze/Action | Ezzel a csatolóval beolvashatja egy batch olvasási fájl műveletének eredményét, amely a legkorszerűbb optikai karaktert alkalmazza |
> | DataAction | Microsoft. CognitiveServices/accounts/ComputerVision/Read/Operations/READ | Ez az interfész az olvasási művelet OCR-eredményeinek beolvasására szolgál. A csatoló URL-címét a Batch olvasási fájl felületéről visszaadott <b>"Operation-Location"</b> mezőből kell beolvasni. |
> | DataAction | Microsoft. CognitiveServices/accounts/ComputerVision/recognizetext/Action | Ezt a felületet használhatja egy szövegfelismerés művelet eredményének lekéréséhez. Ha a szövegfelismerés felületet használja, a válasz egy "Operation-Location" nevű mezőt tartalmaz. A "művelet helye" mező tartalmazza azt az URL-címet, amelyet a Get szövegfelismerés művelet eredménye művelethez kell használnia. |
> | DataAction | Microsoft. CognitiveServices/fiókok/ComputerVision/címke/művelet | A művelet a megadott rendszerkép tartalmához kapcsolódó szavak vagy címkék listáját hozza létre.<br>A Computer Vision API a képeken található objektumok, élőlények, díszletek vagy műveletek alapján lehet címkéket visszaadni.<br>A kategóriáktól eltérően a címkék nem a hierarchikus besorolási rendszer szerint vannak rendszerezve, hanem a képtartalomnak.<br>A címkék tartalmazhatnak olyan mutatókat, amelyek a kétértelműség elkerülését vagy kontextust biztosítanak, például a "cselló" címkét a "hangszer" mutató kísérheti.<br>Az összes címke angol nyelven van. |
> | DataAction | Microsoft. CognitiveServices/accounts/ComputerVision/textoperations/READ | Ez a csatoló a szöveg felismerésére szolgáló művelet eredményének beolvasására szolgál. A csatoló URL-címét a szövegfelismerés felületről visszaadott <b>"Operation-Location"</b> mezőből kell beolvasni. |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/imagelists/Action | Rendszerkép-lista létrehozása |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/imagelists/delete | Képlista – törlés |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/imagelists/images/delete | Rendszerkép törlése a rendszerkép-listából. A Képlista használatával a képfájlok és a mérkőzések API-k használatakor a rendszer zavaros egyeztetést végezhet más képeken. Törölje az összes képet a listából. A Képlista használatával a képfájlok és a mérkőzések API-k használatakor a rendszer zavaros egyeztetést végezhet más képekkel. * |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/imagelists/images/READ | Rendszerkép – az összes rendszerkép-azonosító lekérése |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/imagelists/images/Write | Adjon hozzá egy képet a képek listájához. A Képlista használatával a képfájlok és a mérkőzések API-k használatakor a rendszer zavaros egyeztetést végezhet más képeken. |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/imagelists/READ | Képlista – részletek beolvasása – képlista – összes beolvasása |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/imagelists/refreshindex/Action | Képlista – keresési index frissítése |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/imagelists/Write | Képlista – részletek frissítése |
> | DataAction | Microsoft. CognitiveServices/fiókok/ContentModerator/processimage/értékelés/művelet | A zamatos vagy felnőtt tartalmat tartalmazó rendszerkép valószínűségét adja vissza. |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/processimage/findfaces/Action | Arcok keresése a képekben. |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/processimage/Match/Action | A Fuzzily az egyik egyéni rendszerkép-listával egyező képet biztosít.<br>Az egyéni rendszerkép-listát az API használatával hozhatja létre és kezelheti.<br> |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/processimage/OCR/Action | A megadott nyelven a képen található bármely szöveget adja vissza. Ha a bemenetben nincs megadva nyelv, az észlelés alapértelmezett értéke az angol. |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/processtext/detectlanguage/Action | Ez a művelet a megadott bemeneti tartalom nyelvét fogja észlelni.<br>Az ISO 639-3 kódot adja vissza az elküldött szöveget tartalmazó domináns nyelvhez.<br>Több mint 110 nyelv támogatott. |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/processtext/Screen/Action | A művelet több mint 100 nyelven észleli a trágár kifejezéseket, és ezeket egyéni és megosztott tiltólistákkal egyezteti. |
> | DataAction | Microsoft. CognitiveServices/fiókok/ContentModerator/csapatok/feladatok/művelet | A rendszer visszaadja a feladattípust a végponton közzétett rendszerkép-tartalomhoz.  |
> | DataAction | Microsoft. CognitiveServices/fiókok/ContentModerator/csapatok/feladatok/olvasás | A feladatok azonosítójának beolvasása. |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/Teams/Reviews/AccessKey/READ | Szerezze be a csapathoz tartozó tartalom-hozzáférési kulcs áttekintését. |
> | DataAction | Microsoft. CognitiveServices/fiókok/ContentModerator/csapatok/értékelések/művelet | A létrehozott felülvizsgálatok megjelennek a véleményezők számára a csapata számára. Ahogy a felülvizsgálók teljes áttekintést végeznek, a felülvizsgálat eredményeit a rendszer közzéteszi (azaz HTTP-POST) a megadott CallBackEndpoint. |
> | DataAction | Microsoft. CognitiveServices/fiókok/ContentModerator/csapatok/értékelések/keretek/olvasás | *NotDefined* |
> | DataAction | Microsoft. CognitiveServices/fiókok/ContentModerator/csapatok/értékelések/keretek/írás | Ezzel a módszerrel adhat hozzá képkockákat a videók áttekintéséhez. |
> | DataAction | Microsoft. CognitiveServices/fiókok/ContentModerator/csapatok/értékelések/közzététel/művelet | A videós felülvizsgálatok kezdetben egy közzé nem tett állapotban jönnek létre – ez azt jelenti, hogy nem érhető el véleményezésre a csapatnál, hogy még át lehessen tekinteni őket. |
> | DataAction | Microsoft. CognitiveServices/fiókok/ContentModerator/csapatok/értékelések/olvasás | Az átadott felülvizsgálati azonosító részleteit adja vissza. |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/Teams/Reviews/átirat/művelet | Ez az API egy videóátirata-fájlt (a videóban elhangzó szavak szöveges verzióját) egy videó-áttekintésbe helyezi. A fájlnak érvényes WebVTT formátumúnak kell lennie. |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/Teams/Reviews/transcriptmoderationresult/Action | Ez az API egy átírási képernyő szöveges eredmény-fájlját adja a videó felülvizsgálatához. Az átirat képernyő szöveges eredménye a képernyő szövegének API-ját eredményezi. Az átiratok szöveges eredményének létrehozásához egy átiratot tartalmazó fájlt kell megadnia a trágár szöveg-API használatával. |
> | DataAction | Microsoft. CognitiveServices/fiókok/ContentModerator/csapatok/beállítások/sablonok/törlés | Sablon törlése a csapatban |
> | DataAction | Microsoft. CognitiveServices/fiókok/ContentModerator/csapatok/beállítások/sablonok/olvasás | A csapaton kiépített felülvizsgálati sablonok tömbjét adja vissza. |
> | DataAction | Microsoft. CognitiveServices/fiókok/ContentModerator/csapatok/beállítások/sablonok/írás | A megadott sablon létrehozása vagy frissítése |
> | DataAction | Microsoft. CognitiveServices/fiókok/ContentModerator/csapatok/munkafolyamatok/olvasás | Egy adott munkafolyamat részleteinek beolvasása a csapatban. A csapat számára elérhető összes munkafolyamat beolvasása * |
> | DataAction | Microsoft. CognitiveServices/fiókok/ContentModerator/csapatok/munkafolyamatok/írás | Hozzon létre egy új munkafolyamatot, vagy frissítsen egy meglévőt. |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/termlists/Action | Feltételek listájának létrehozása |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/termlists/bulkupdate/Action | Kifejezések listája – tömeges frissítés |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/termlists/delete | Kifejezések listája – törlés |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/termlists/READ | Lejárati listák – lekérdezheti az összes lejárati listát – részletes információk |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/termlists/refreshindex/Action | Feltételek listája – keresési index frissítése |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/termlists/terms/delete | Kifejezés – törlési időszak – az összes feltétel törlése |
> | DataAction | Microsoft. CognitiveServices/fiókok/ContentModerator/termlists/feltételek/olvasás | Kifejezés – az összes kifejezés beszerzése |
> | DataAction | Microsoft. CognitiveServices/fiókok/ContentModerator/termlists/feltételek/írás | Kifejezés – feltétel hozzáadása |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/termlists/Write | Lejárati listák – részletek frissítése |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/osztályozás/iterációk/rendszerkép/művelet | A rendszerkép besorolása és az eredmény mentése. |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/osztályozás/iterációk/rendszerkép/tár/művelet | Rendszerkép besorolása az eredmény mentése nélkül. |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/osztályozás/iterációk/URL/művelet | A kép URL-címének besorolása és az eredmény mentése. |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/osztályozás/iterációk/URL/tár/művelet | Képurl-cím besorolása az eredmény mentése nélkül. |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/észlelés/iterációk/képek/művelet | Egy rendszerkép objektumainak észlelése és az eredmény mentése. |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/észlelés/iterációk/rendszerkép/tár/művelet | A rendszerképben lévő objektumok észlelése az eredmény mentése nélkül. |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/észlelés/iteráció/URL/művelet | A kép URL-címében lévő objektumok észlelése és az eredmény mentése. |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/észlelés/iterációk/URL/tár/művelet | A rendszerkép URL-címében lévő objektumok észlelése az eredmény mentése nélkül. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. előrejelzés/tartományok/olvasás | Egy adott tartomány információinak beolvasása. Az elérhető tartományok listájának beolvasása. * |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Jóslás/labelproposals/Setting/Action | Adja meg a címke-javaslat készletének méretét. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Jóslás/labelproposals/beállítás/olvasás | A projekthez tartozó címke-javaslat készletének beolvasása. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Jóslás/Project/Migrálás/művelet | *NotDefined* |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. előrejelzés/projektek/művelet | Hozzon létre egy projektet. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Jóslás/projects/delete | Egy adott projekt törlése. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Jóslás/projects/images/Action | Ez az API a szövegtörzset a többrészes, az űrlap-és az Application/oktett-stream típusúként fogadja el. Többrészes használat esetén |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Jóslás/projects/images/delete | Képek törlése a betanítási lemezképek készletből. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Jóslás/projects/images/Files/Action | Ez az API a lemezképek létrehozásához fogadja a fájlok kötegét, és opcionálisan címkéket is. A korlát 64 kép és 20 címke. |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/projektek/lemezképek/azonosító/olvasás | Ez az API rendszerképeket ad vissza a megadott címkékhez és opcionálisan iterációhoz. Ha nem ad meg iterációt, |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/projektek/lemezképek/előrejelzések/művelet | Ez az API rendszerképekből álló köteget hoz létre a megadott előre jelzett képekből. A korlát 64 kép és 20 címke. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Jóslás/projects/images/regionproposals/Action | Ezzel az API-val a régiókra vonatkozó javaslatok is beszerezhetők a rendszerképekkel együtt. Üres tömböt ad vissza, ha nem található javaslat. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Jóslás/projects/images/Regions/Action | Ez az API elfogadja a képterületek kötegét, és opcionálisan felcímkézi a meglévő lemezképeket a régióval kapcsolatos információkkal. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Jóslás/projects/images/Regions/delete | Rendszerkép-régiók készletének törlése. |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/projektek/lemezképek/javasolt/művelet | Ez az API a javasolt címkék azonosítói alapján szűrt címkézett képek beolvasását végzi. Üres tömböt ad vissza, ha nem találhatók lemezképek. |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/projektek/lemezképek/javasolt/darabszám/művelet | Ez az API a tagIds egy adott küszöbértékhez javasolt címkével rendelkező címkézett képek számának beolvasását veszi igénybe. |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/projektek/lemezképek/címkézett/darabszám/olvasás | A szűrés egy és/vagy kapcsolaton keresztül történik. Ha például a megadott címke azonosítói a "Dog" és a |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Jóslás/projects/images/Tagged/READ | Ez az API támogatja a Batch és a Range kijelölését. Alapértelmezés szerint a rendszer csak az első 50-lemezképet fogja visszaadni. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Jóslás/projects/images/Tags/Action | Képek készletének hozzárendelése címkékkel. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Jóslás/projects/images/Tags/delete | Címkék készletének eltávolítása képekből. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. előrejelzés/projektek/lemezképek/címkézetlen/darabszám/olvasás | Ez az API azokat a lemezképeket adja vissza, amelyek nem rendelkeznek címkével egy adott projekthez, és opcionálisan egy iterációt is. Ha nem ad meg iterációt, |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/projektek/lemezképek/címkézetlen/olvasás | Ez az API támogatja a Batch és a Range kijelölését. Alapértelmezés szerint a rendszer csak az első 50-lemezképet fogja visszaadni. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Jóslás/projects/images/URL/művelet | Ez az API egy URL-köteget és opcionális címkéket fogad el a lemezképek létrehozásához. A korlát 64 kép és 20 címke. |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/projektek/ismétlések/törlés | Egy projekt adott iterációjának törlése. |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/projektek/ismétlések/exportálás/művelet | Exportáljon egy betanított iterációt. |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/projektek/ismétlések/exportálás/olvasás | Egy adott iteráció exportálási listájának beolvasása. |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/projektek/ismétlések/teljesítmény/lemezkép/darabszám/olvasás | A szűrés egy és/vagy kapcsolaton keresztül történik. Ha például a megadott címke azonosítói a "Dog" és a |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/projektek/ismétlések/teljesítmény/lemezkép/olvasás | Ez az API támogatja a Batch és a Range kijelölését. Alapértelmezés szerint a rendszer csak az első 50-lemezképet fogja visszaadni. |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/projektek/ismétlések/teljesítmény/olvasás | Részletes teljesítmény-információk az iterációról. |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/projektek/ismétlések/közzététel/művelet | Egy adott iteráció közzététele. |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/projektek/ismétlések/közzététel/törlés | Egy adott iteráció közzétételének visszavonása. |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/projektek/ismétlések/olvasás | Egy adott iteráció beolvasása. Iterációk beolvasása a projekthez. * |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/projektek/ismétlések/írás | Egy adott iteráció frissítése. |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/projektek/előrejelzések/törlés | Az előre jelzett rendszerképek készletének és a hozzájuk tartozó előrejelzési eredményeknek a törlése. |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/projektek/előrejelzések/lekérdezés/művelet | Az előrejelzési végpontnak elküldett rendszerképek beolvasása. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. előrejelzés/projektek/quicktest/rendszerkép/művelet | Rendszerkép gyors tesztelése. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Jóslás/projects/quicktest/URL/Action | Rendszerkép URL-címének gyors tesztelése |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/projektek/olvasás | Egy adott projekt beszerzése. A projektek beszerzése. * |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Jóslás/projects/Tags/Action | Hozzon létre egy címkét a projekthez. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Jóslás/projects/Tags/delete | Címke törlése a projektből. |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/projektek/címkék/olvasás | Egy adott címkével kapcsolatos információk beolvasása. Egy adott projekthez és iterációhoz tartozó címkék beolvasása. * |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/projektek/címkék/írás | Címke frissítése |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. előrejelzés/projektek/tagsandregions/javaslatok/művelet | Ezzel az API-val a címkézetlen rendszerképek tömbje/kötege számára javasolt címkék és régiók jelennek meg, a címkék megbízhatóságával együtt. Üres tömböt ad vissza, ha nem találhatók címkék. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. előrejelzés/projektek/vonat/művelet | A betanításra szolgáló várólisták projekt. |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/projektek/írás | Egy adott projekt frissítése. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. előrejelzés/kvóta/művelet | *NotDefined* |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Jóslás/kvóta/delete | *NotDefined* |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/kvóta/frissítés/írás | *NotDefined* |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/használat/előrejelzés/felhasználó/olvasás | Az Oxford-felhasználó használati előrejelzési erőforrásának beolvasása |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/használat/képzés/erőforrás/rétegek/olvasás | Azure-felhasználó használatának betanítása erőforrás-használathoz |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/használat/képzés/felhasználó/olvasás | Az Oxford-felhasználó használatának betanítási erőforrásának beszerzése |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. előrejelzés/felhasználó/művelet | *NotDefined* |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Jóslás/felhasználó/delete | *NotDefined* |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/felhasználó/állapot/írás | Felhasználói állapot frissítése |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/felhasználó/csoport/írás | *NotDefined* |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/felhasználók/olvasás | *NotDefined* |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/engedélyezési/törlés | Megadott képességgel rendelkező engedélyezett felhasználó törlése |
> | DataAction | Microsoft. CognitiveServices/fiókok/CustomVision. előrejelzés/engedélyezési/olvasási | A megadott képességgel rendelkező, engedélyezett felhasználók listájának beolvasása |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Jóslás/engedélyezési/írás | Egy adott képességgel rendelkező felhasználó frissítése vagy létrehozása az engedélyezési listában |
> | Műveletek | Microsoft. CognitiveServices/fiókok/törlés | API-fiókok törlése |
> | DataAction | Microsoft. CognitiveServices/accounts/EntitySearch/Search/Action | Az entitások és a helyek eredményeinek beolvasása egy adott lekérdezés esetében. |
> | DataAction | Microsoft. CognitiveServices/fiókok/arc/észlelés/művelet | Azonosíthatja az emberi arcokat egy képen, az arc négyszögeit és opcionálisan faceIds, tereptárgyak és attribútumokkal. |
> | DataAction | Microsoft. CognitiveServices/fiókok/arc/facelists/delete | Megadott Arcfelismerés törlése. A rendszer a Face (arc) listán szereplő kapcsolódó Face-képeket is törli. |
> | DataAction | Microsoft. CognitiveServices/fiókok/arc/facelists/persistedfaces/delete | Arc-listából származó arc törlése a megadott faceListId és persisitedFaceId. A kapcsolódó arckép is törlődik. |
> | DataAction | Microsoft. CognitiveServices/fiókok/Face/facelists/persistedfaces/Write | Egy arc hozzáadása egy megadott arc listához, amely legfeljebb 1 000 arc lehet. |
> | DataAction | Microsoft. CognitiveServices/fiókok/arc/facelists/READ | Egy Arcfelismerés faceListId, neve, userData és arcok beolvasása a Face listában. Listázza a faceListId, a nevet és a userData. |
> | DataAction | Microsoft. CognitiveServices/fiókok/arc/facelists/írás | Hozzon létre egy üres Arcfelismerés a felhasználó által megadott faceListId, névvel és opcionális userData. Akár 64-es Arcfelismerés is engedélyezett az Arcfelismerés információinak frissítése, beleértve a nevet és a userData. |
> | DataAction | Microsoft. CognitiveServices/fiókok/arc/findsimilars/művelet | A lekérdezés arca faceId a hasonló megjelenésű arcok kereséséhez egy faceId tömbből, egy Arcfelismerés vagy egy nagy Arcfelismerés. faceId |
> | DataAction | Microsoft. CognitiveServices/fiókok/arc/csoport/művelet | A jelölt arcok a Face hasonlóság alapján csoportosíthatók csoportokba. |
> | DataAction | Microsoft. CognitiveServices/fiókok/arc/azonosítás/művelet | egy-a-többhöz azonosítással megkeresheti az adott lekérdezés személyének legközelebbi egyezését egy személy vagy egy nagy személy csoportból. |
> | DataAction | Microsoft. CognitiveServices/fiókok/arc/largefacelists/delete | Megadott nagyméretű Arcfelismerés törlése. A rendszer a nagy számú arc listán szereplő kapcsolódó arcokat is törli. |
> | DataAction | Microsoft. CognitiveServices/fiókok/arc/largefacelists/persistedfaces/delete | A megadott largeFaceListId és persisitedFaceId használatával törölheti a Faces elemet egy nagyméretű Arcfelismerés alapján. A kapcsolódó arckép is törlődik. |
> | DataAction | Microsoft. CognitiveServices/fiókok/Face/largefacelists/persistedfaces/READ | Megőrzött arc beolvasása nagy largeFaceListId és persistedFaceId Egy megadott nagyméretű persistedFaceId és userData arcok listázása. |
> | DataAction | Microsoft. CognitiveServices/fiókok/Face/largefacelists/persistedfaces/Write | Egy arc hozzáadása egy megadott nagy Arcfelismerés-listához, amely legfeljebb 1 000 000 arc lehet. Egy adott arc userData mezőjének frissítése egy nagy Arcfelismerés-listán a persistedFaceId. |
> | DataAction | Microsoft. CognitiveServices/fiókok/arc/largefacelists/READ | Nagy számú Arcfelismerés largeFaceListId, neve, userData beolvasása. Jelenítse meg a largeFaceListId, a név és a userData információit. |
> | DataAction | Microsoft. CognitiveServices/accounts/Face/largefacelists/Train/Action | Nagyméretű Arcfelismerés betanítási feladat elküldése. A képzés elengedhetetlen lépés, hogy csak a betanított nagyméretű arcok használhatók. |
> | DataAction | Microsoft. CognitiveServices/fiókok/arc/largefacelists/képzés/olvasás | A nagyméretű Arcfelismerés betanítási állapotának ellenőrzéséhez, vagy még folyamatban van. A LargeFaceList-képzés aszinkron művelet |
> | DataAction | Microsoft. CognitiveServices/fiókok/arc/largefacelists/írás | Hozzon létre egy üres nagyméretű arc listát a felhasználó által megadott largeFaceListId, névvel és opcionális userData. Nagy méretű Arcfelismerés információinak frissítése, beleértve a nevet és a userData. |
> | DataAction | Microsoft. CognitiveServices/fiókok/arc/largepersongroups/delete | Meglévő nagyvállalati csoport törlése a megadott personGroupId. A rendszer törli a nagy csoportba tartozó megőrzött adatmennyiséget. |
> | DataAction | Microsoft. CognitiveServices/fiókok/Face/largepersongroups/Persons/Action | Hozzon létre egy új személyt egy adott nagy csoportba. Ha hozzá szeretne adni egy arcot ehhez a személyhez, hívja meg a következőt |
> | DataAction | Microsoft. CognitiveServices/fiókok/Face/largepersongroups/Persons/delete | Meglévő személy törlése egy nagyméretű személy csoportból. A rendszer törli az összes tárolt adat-és Face-képet a személy bejegyzésében. |
> | DataAction | Microsoft. CognitiveServices/fiókok/Face/largepersongroups/Persons/persistedfaces/delete | Egy nagyméretű személy csoportból származó személyből származó arc törlése. A rendszer a Face bejegyzéshez kapcsolódó arcfelismerés és-képet is törli. |
> | DataAction | Microsoft. CognitiveServices/fiókok/Face/largepersongroups/Persons/persistedfaces/READ | Személy arca adatainak beolvasása. A megőrzött személy arcát a largePersonGroupId, a számú personid és a persistedFaceId határozza meg. |
> | DataAction | Microsoft. CognitiveServices/fiókok/Face/largepersongroups/Persons/persistedfaces/Write | Arcfelismerés vagy-ellenőrzés céljából adjon hozzá egy képet egy személyhez egy nagy méretű személy csoportba. Annak érdekében, hogy a felhasználó frissítse a megőrzött személy userData mezőjét. |
> | DataAction | Microsoft. CognitiveServices/fiókok/Face/largepersongroups/Persons/READ | Beolvashat egy személy nevét és userData, valamint a regisztrált személy arcképét jelképező megőrzött faceIds. Az összes személy információinak listázása a megadott nagyméretű személy csoportban, beleértve a számú personid, a nevet, a userData és a persistedFaceIds. |
> | DataAction | Microsoft. CognitiveServices/fiókok/Face/largepersongroups/Persons/Write | Egy személy nevének vagy userData frissítése. |
> | DataAction | Microsoft. CognitiveServices/fiókok/arc/largepersongroups/READ | Egy nagyméretű személy csoport információinak beolvasása, beleértve a nevét és a userData is. Ez az API a nagyméretű személy csoport adatainak listáját adja vissza az összes meglévő nagyvállalati csoport largePesonGroupId, nevével és userData. |
> | DataAction | Microsoft. CognitiveServices/accounts/Face/largepersongroups/Train/Action | Nagyméretű csoportos betanítási feladat elküldése. A képzés egy kulcsfontosságú lépés, amelyet csak a betanított nagyméretű személyek használhatnak. |
> | DataAction | Microsoft. CognitiveServices/fiókok/arc/largepersongroups/képzés/olvasás | A nagyméretű személy csoportos betanítási állapotának ellenőrzéséhez, vagy még folyamatban van. A LargePersonGroup-képzés aszinkron művelet |
> | DataAction | Microsoft. CognitiveServices/fiókok/arc/largepersongroups/írás | Hozzon létre egy új, a felhasználó által megadott largePersonGroupId, névvel és opcionális userData rendelkező nagyméretű személy csoportot. Meglévő nagyvállalati csoport nevének és userData frissítése. A tulajdonságok változatlanok maradnak, ha nincsenek a kérelem törzsében. |
> | DataAction | Microsoft. CognitiveServices/fiókok/arc/persongroups/delete | Meglévő személy csoport törlése a megadott personGroupId. A rendszer törli az adott személy csoportba tartozó megőrzött adatkészleteket. |
> | DataAction | Microsoft. CognitiveServices/fiókok/Face/persongroups/Persons/Action | Hozzon létre egy új személyt egy adott személy csoportban. Ha hozzá szeretne adni egy arcot ehhez a személyhez, hívja meg a következőt |
> | DataAction | Microsoft. CognitiveServices/fiókok/Face/persongroups/Persons/delete | Meglévő személy törlése egy személy csoportból. A rendszer törli az összes tárolt adat-és Face-képet a személy bejegyzésében. |
> | DataAction | Microsoft. CognitiveServices/fiókok/Face/persongroups/Persons/persistedfaces/delete | Arc törlése egy személy csoportból. A rendszer a Face bejegyzéshez kapcsolódó arcfelismerés és-képet is törli. |
> | DataAction | Microsoft. CognitiveServices/fiókok/Face/persongroups/Persons/persistedfaces/READ | Személy arca adatainak beolvasása. A megőrzött személy arcát a personGroupId, a számú personid és a persistedFaceId határozza meg. |
> | DataAction | Microsoft. CognitiveServices/fiókok/Face/persongroups/Persons/persistedfaces/Write | Arcfelismerés vagy-ellenőrzés céljából vegyen fel egy képet egy személy csoportba. Ha több frissítés képét szeretné kezelni egy személy megőrzött arca userData mezőjében. |
> | DataAction | Microsoft. CognitiveServices/fiókok/Face/persongroups/Persons/READ | Beolvashat egy személy nevét és userData, valamint a regisztrált személy arcképét jelképező megőrzött faceIds. A megadott személy csoport összes adatának listázása, beleértve a regisztrált számú personid, a nevet, a userData és a persistedFaceIds. |
> | DataAction | Microsoft. CognitiveServices/fiókok/Face/persongroups/Persons/Write | Egy személy nevének vagy userData frissítése. |
> | DataAction | Microsoft. CognitiveServices/fiókok/arc/persongroups/READ | A személy csoport nevének és userData beolvasása. A személyes adatok ezen personGroup való beolvasásához használja a pesonGroupId, a Name és a userData listát. |
> | DataAction | Microsoft. CognitiveServices/accounts/Face/persongroups/Train/Action | Személy csoportos betanítási feladat elküldése. A képzés olyan kulcsfontosságú lépés, amelyet csak a képzett személy csoport használhat. |
> | DataAction | Microsoft. CognitiveServices/fiókok/arc/persongroups/képzés/olvasás | Annak ellenőrzéséhez, hogy a személy csoportos betanítási állapota befejezett vagy még folyamatban van-e. A PersonGroup-képzés egy aszinkron művelet, amely aktiválva van |
> | DataAction | Microsoft. CognitiveServices/fiókok/arc/persongroups/írás | Hozzon létre egy új személy csoportot a megadott personGroupId, névvel és felhasználó által megadott userData. Egy meglévő személy csoport nevének és userData frissítése. A tulajdonságok változatlanok maradnak, ha nincsenek a kérelem törzsében. |
> | DataAction | Microsoft. CognitiveServices/fiókok/szembenézés/ellenőrzés/művelet | Győződjön meg arról, hogy két arc ugyanahhoz a személyhez tartozik-e, vagy az, hogy egy arc személyhez tartozik-e. |
> | DataAction | Microsoft. CognitiveServices/accounts/FormRecognizer/Custom/models/elemzés/művelet | A kulcs-érték párok kinyerése egy adott dokumentumból. A bemeneti dokumentumnak a támogatott tartalomtípusok egyikének kell lennie: "Application/PDF", "képfájl/JPEG" vagy "képfájl/png". A rendszer sikeres választ ad vissza a JSON-ban. |
> | DataAction | Microsoft. CognitiveServices/accounts/FormRecognizer/Custom/models/delete | Modell-összetevők törlése. |
> | DataAction | Microsoft. CognitiveServices/accounts/FormRecognizer/Custom/models/Keys/READ | A modell kulcsainak beolvasása. |
> | DataAction | Microsoft. CognitiveServices/accounts/FormRecognizer/Custom/models/READ | Modell adatainak beolvasása. Az összes betanított egyéni modell adatainak beolvasása * |
> | DataAction | Microsoft. CognitiveServices/accounts/FormRecognizer/Custom/Train/Action | Egyéni modell létrehozása és betanítása.<br>A betanítási kérelemnek tartalmaznia kell egy olyan forrás paramétert, amely vagy egy külsőleg elérhető Azure Storage blob-tároló URI-ja (lehetőleg egy közös hozzáférési aláírás URI-ja) vagy egy helyileg csatlakoztatott meghajtó adatmappájának érvényes elérési útja.<br>A helyi elérési utak megadásakor a Linux/Unix útvonal formátumát kell követnie, és a bemeneti csatlakoztatási konfigurációhoz tartozó abszolút elérési útnak kell lennie. |
> | DataAction | Microsoft. CognitiveServices/fiókok/FormRecognizer/előre összeépített/beérkezés/asyncbatchanalyze/művelet | Mező szövegének és szemantikai értékének kinyerése egy adott bevételezési dokumentumból. A bemeneti képdokumentumnak a támogatott tartalomtípusok egyikének kell lennie – JPEG, PNG, BMP, PDF vagy TIFF. A sikeres válasz egy olyan JSON, amely egy "Operation-Location" nevű mezőt tartalmaz, amely tartalmazza a beérkezési eredmény beolvasása művelet URL-címét az eredmények aszinkron lekéréséhez. |
> | DataAction | Microsoft. CognitiveServices/fiókok/FormRecognizer/előre összeépített/bevételezés/műveletek/művelet | Lekérdezi az állapotot, és beolvassa egy elemzés-visszaigazolási művelet eredményét. A csatoló URL-címe a visszaigazolási válaszban a "művelet – hely" fejlécből szerezhető be. |
> | DataAction | Microsoft. CognitiveServices/accounts/ImageSearch/details/Action | Egy képpel kapcsolatos elemzéseket ad vissza, például a képet tartalmazó weblapokat. |
> | DataAction | Microsoft. CognitiveServices/accounts/ImageSearch/Search/Action | Adott lekérdezéshez kapcsolódó rendszerképek beolvasása. |
> | DataAction | Microsoft. CognitiveServices/fiókok/ImageSearch/trendek/művelet | Aktuálisan növekvő rendszerképek beolvasása. |
> | DataAction | Microsoft. CognitiveServices/accounts/ImmersiveReader/getcontentmodelforreader/Action | Egy magával ragadó olvasó munkamenetet hoz létre |
> | DataAction | Microsoft. CognitiveServices/fiókok/InkRecognizer/felismerés/művelet | A stroke-adathalmazok egy készlete elemzi a tartalmat, és létrehoz egy felismert entitások listáját, beleértve a felismert szöveget is. |
> | Műveletek | Microsoft. CognitiveServices/accounts/Listkeys műveletének beolvasása/Action | Kulcsok listázása |
> | DataAction | Microsoft. CognitiveServices/fiókok/LUIS/előrejelzés/művelet | Lekéri a megadott lekérdezés közzétett végpontjának előrejelzését. |
> | DataAction | Microsoft. CognitiveServices/accounts/NewsSearch/categorysearch/Action | Egy megadott kategóriára vonatkozó híreket ad vissza. |
> | DataAction | Microsoft. CognitiveServices/accounts/NewsSearch/Search/Action | Az adott lekérdezésre vonatkozó Hírek beszerzése. |
> | DataAction | Microsoft. CognitiveServices/accounts/NewsSearch/trendingtopics/Action | A Bing által azonosított trendekkel kapcsolatos témakörök beolvasása. Ugyanezek a témakörök a Bing kezdőlapjának alján látható szalagcímben jelennek meg. |
> | Műveletek | Microsoft. CognitiveServices/fiókok/olvasás | API-fiókok olvasása. |
> | Műveletek | Microsoft. CognitiveServices/accounts/regenerateKey/Action | Kulcs újragenerálta |
> | Műveletek | Microsoft. CognitiveServices/fiókok/SKU/READ | Meglévő erőforráshoz tartozó elérhető SKU-ket olvas. |
> | DataAction | Microsoft. CognitiveServices/fiókok/helyesírás/helyesírás/művelet | Helyesírás-ellenőrzési lekérdezés eredményének beolvasása a GET vagy a POST használatával. |
> | DataAction | Microsoft. CognitiveServices/fiókok/TextAnalytics/entitások/művelet | Az API az ismert entitások és az általános névvel ellátott entitások (\"személy\", \"hely\", \"szervezet\" stb.) listáját adja vissza egy adott dokumentumban. |
> | DataAction | Microsoft. CognitiveServices/fiókok/TextAnalytics/alkifejezések/művelet | Az API a bemeneti szövegben található fő beszédtémákat jelző sztringeket jeleníti meg. |
> | DataAction | Microsoft. CognitiveServices/accounts/TextAnalytics/languages/Action | Az API a felderített nyelvet és az adott 0 és 1 közötti numerikus pontszámot jeleníti meg. Az 1-hez közeli pontszámok 100%-os bizonyossággal jelzik az azonosított nyelv valódiságát. Összesen 120 nyelv támogatott. |
> | DataAction | Microsoft. CognitiveServices/accounts/TextAnalytics/hangulat/művelet | Az API 0 és 1 közötti numerikus pontszámokat jelenít meg.<br>Az 1-hez közeli értékek pozitív hangulatot, a 0-hoz közeliek pedig negatív hangulatot jelölnek.<br>A 0,5-es pontszám jelzi az érzelmek hiányát (például<br>egy factoid-utasítás). |
> | Műveletek | Microsoft. CognitiveServices/fiókok/használati adatok/olvasás | Meglévő erőforráshoz tartozó kvóta használatának beolvasása. |
> | DataAction | Microsoft. CognitiveServices/accounts/VideoSearch/details/Action | Bepillantást nyerhet egy videóra, például a kapcsolódó videókra. |
> | DataAction | Microsoft. CognitiveServices/accounts/VideoSearch/Search/Action | Egy adott lekérdezéshez kapcsolódó videók beolvasása. |
> | DataAction | Microsoft. CognitiveServices/fiókok/VideoSearch/trendek/művelet | Aktuálisan növekvő videók beolvasása. |
> | DataAction | Microsoft. CognitiveServices/accounts/VisualSearch/Search/Action | A megadott képhez kapcsolódó címkék listájának beolvasása |
> | DataAction | Microsoft. CognitiveServices/fiókok/webkeresés/keresés/művelet | Egy adott lekérdezéshez webes, képképek, Hírek & videók eredményeinek beolvasása. |
> | Műveletek | Microsoft. CognitiveServices/fiókok/írás | API-fiókok írása. |
> | Műveletek | Microsoft. CognitiveServices/checkDomainAvailability/művelet | Beolvassa az elérhető SKU-t egy előfizetéshez. |
> | Műveletek | Microsoft. CognitiveServices/Locations/checkSkuAvailability/Action | Beolvassa az elérhető SKU-t egy előfizetéshez. |
> | Műveletek | Microsoft. CognitiveServices/Locations/checkSkuAvailability/Action | Beolvassa az elérhető SKU-t egy előfizetéshez. |
> | Műveletek | Microsoft. CognitiveServices/Locations/deleteVirtualNetworkOrSubnets/Action | Értesítés a Microsoft. Network VirtualNetworks vagy alhálózatok törléséről. |
> | Műveletek | Microsoft. CognitiveServices/Locations/operationresults/READ | Aszinkron művelet állapotának beolvasása. |
> | Műveletek | Microsoft. CognitiveServices/Operations/READ | Az összes elérhető művelet listázása |
> | Műveletek | Microsoft. CognitiveServices/regisztráció/művelet | Cognitive Services-előfizetés regisztrálása |
> | Műveletek | Microsoft. CognitiveServices/regisztráció/művelet | Cognitive Services-előfizetés regisztrálása |

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Commerce/RateCard/READ | Az adott előfizetéshez tartozó ajánlati adatokat, erőforrás/fogyasztásmérő metaadatait és díjszabását adja vissza. |
> | Műveletek | Microsoft. Commerce/regisztráció/művelet | Előfizetés regisztrálása a Microsoft Commerce UsageAggregate |
> | Műveletek | Microsoft. Commerce/regisztráció/művelet | A Microsoft Commerce UsageAggregate-előfizetés regisztrációjának törlése |
> | Műveletek | Microsoft. Commerce/UsageAggregates/READ | Lekéri Microsoft Azure előfizetéssel való felhasználását. Az eredmény az összesített használati adatokat, az előfizetést és az erőforrással kapcsolatos információkat tartalmaz egy adott időtartományon belül. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. számítás/availabilitySets/törlés | A rendelkezésre állási csoport törlése |
> | Műveletek | Microsoft. számítás/availabilitySets/olvasás | Rendelkezésre állási csoport tulajdonságainak beolvasása |
> | Műveletek | Microsoft. számítás/availabilitySets/méreteinek listáján/olvasás | A rendelkezésre állási csoportba tartozó virtuális gépek létrehozásához vagy frissítéséhez elérhető méretek listázása |
> | Műveletek | Microsoft. számítás/availabilitySets/írás | Új rendelkezésre állási csoport létrehozása vagy egy meglévő frissítése |
> | Műveletek | Microsoft. számítás/diskEncryptionSets/törlés | Lemezes titkosítási csoport törlése |
> | Műveletek | Microsoft. számítás/diskEncryptionSets/olvasás | Lemezes titkosítási készlet tulajdonságainak beolvasása |
> | Műveletek | Microsoft. számítás/diskEncryptionSets/írás | Hozzon létre egy új lemez-titkosítási készletet, vagy frissítsen egy meglévőt |
> | Műveletek | Microsoft. számítás/lemezek/beginGetAccess/művelet | A lemez SAS URI-azonosítójának beolvasása a blob-hozzáféréshez |
> | Műveletek | Microsoft. számítás/lemezek/törlés | A lemez törlése |
> | Műveletek | Microsoft. számítás/lemezek/endGetAccess/művelet | A lemez SAS URI-azonosítójának visszavonása |
> | Műveletek | Microsoft. számítás/lemezek/olvasás | Lemez tulajdonságainak beolvasása |
> | Műveletek | Microsoft. számítás/lemezek/írás | Új lemez létrehozása vagy egy meglévő frissítése |
> | Műveletek | Microsoft. számítás/galériák/alkalmazások/törlés | A Gallery-alkalmazás törlése |
> | Műveletek | Microsoft. számítás/galériák/alkalmazások/olvasás | A Gallery-alkalmazás tulajdonságainak beolvasása |
> | Műveletek | Microsoft. számítás/galériák/alkalmazások/verziók/törlés | A katalógus alkalmazás verziójának törlése |
> | Műveletek | Microsoft. számítás/galériák/alkalmazások/verziók/olvasás | A katalógus alkalmazás verziójának tulajdonságainak beolvasása |
> | Műveletek | Microsoft. számítás/galériák/alkalmazások/verziók/írás | Új katalógus-alkalmazás verziójának létrehozása vagy egy meglévő frissítése |
> | Műveletek | Microsoft. számítás/galériák/alkalmazások/írás | Új katalógus-alkalmazás létrehozása vagy egy meglévő frissítése |
> | Műveletek | Microsoft. számítás/galériák/törlés | A katalógus törlése |
> | Műveletek | Microsoft. számítás/galériák/lemezképek/törlés | A katalógus rendszerképének törlése |
> | Műveletek | Microsoft. számítás/galériák/lemezképek/olvasás | A katalógus rendszerképének tulajdonságainak beolvasása |
> | Műveletek | Microsoft. számítás/galériák/lemezképek/verziók/törlés | A katalógus rendszerkép-verziójának törlése |
> | Műveletek | Microsoft. számítás/galériák/lemezképek/verziók/olvasás | A katalógus rendszerkép-verziójának tulajdonságainak beolvasása |
> | Műveletek | Microsoft. számítás/galériák/lemezképek/verziók/írás | Létrehoz egy új katalógus-rendszerkép verzióját, vagy frissít egy meglévőt |
> | Műveletek | Microsoft. számítás/galériák/lemezképek/írás | Létrehoz egy új katalógus-rendszerképet, vagy frissít egy meglévőt |
> | Műveletek | Microsoft. számítás/galériák/olvasás | A gyűjtemény tulajdonságainak beolvasása |
> | Műveletek | Microsoft. számítás/galériák/írás | Új katalógus létrehozása vagy egy meglévő frissítése |
> | Műveletek | Microsoft. számítás/hostGroups/törlés | A gazda csoport törlése |
> | Műveletek | Microsoft. számítási/hostGroups/gazdagépek/törlés | A gazdagép törlése |
> | Műveletek | Microsoft. számítási/hostGroups/gazdagépek/olvasás | Gazdagép tulajdonságainak lekérése |
> | Műveletek | Microsoft. számítási/hostGroups/gazdagépek/írás | Új gazdagép létrehozása vagy meglévő gazdagép frissítése |
> | Műveletek | Microsoft. számítás/hostGroups/olvasás | Egy gazda csoport tulajdonságainak beolvasása |
> | Műveletek | Microsoft. számítás/hostGroups/írás | Létrehoz egy új számítógépcsoportot, vagy frissít egy meglévőt. |
> | Műveletek | Microsoft. számítás/lemezképek/törlés | A rendszerkép törlése |
> | Műveletek | Microsoft. számítás/lemezképek/olvasás | A rendszerkép tulajdonságainak beolvasása |
> | Műveletek | Microsoft. számítás/lemezkép/írás | Új rendszerkép létrehozása vagy meglévő frissítése |
> | Műveletek | Microsoft. számítás/helyszínek/capsOperations/olvasás | Aszinkron Caps-művelet állapotának beolvasása |
> | Műveletek | Microsoft. számítás/helyszínek/kiírások/olvasás | Aszinkron lemez művelet állapotának beolvasása |
> | Műveletek | Microsoft. számítás/Locations/logAnalytics/getRequestRateByInterval/Action | Hozzon létre naplókat az összes kérelem időbeli intervallum szerinti megjelenítéséhez a támogatás szabályozása érdekében. |
> | Műveletek | Microsoft. számítás/Locations/logAnalytics/getThrottledRequests/Action | Hozzon létre naplókat a szabályozott kérelmek összesítésének megjelenítéséhez ResourceName, OperationName vagy az alkalmazott szabályozási házirend szerint csoportosítva. |
> | Műveletek | Microsoft. számítás/helyszínek/műveletek/olvasás | Gets the status of an asynchronous operation |
> | Műveletek | Microsoft. számítás/helyszínek/közzétevők/artifacttypes/ajánlatok/olvasás | Platform-rendszerkép ajánlat tulajdonságainak beolvasása |
> | Műveletek | Microsoft. számítás/helyszínek/közzétevők/artifacttypes/ajánlatok/SKU/READ | Platform-Rendszerképbeli SKU tulajdonságainak beolvasása |
> | Műveletek | Microsoft. számítás/helyszínek/közzétevők/artifacttypes/ajánlatok/SKU/Versions/READ | Platform rendszerkép-verzió tulajdonságainak beolvasása |
> | Műveletek | Microsoft. számítás/helyszínek/közzétevők/artifacttypes/típusok/olvasás | VMExtension-típus tulajdonságainak beolvasása |
> | Műveletek | Microsoft. számítás/helyszínek/közzétevők/artifacttypes/típusok/verziók/olvasás | VMExtension-verzió tulajdonságainak beolvasása |
> | Műveletek | Microsoft. számítás/helyszínek/közzétevők/olvasás | Közzétevő tulajdonságainak beolvasása |
> | Műveletek | Microsoft. számítás/helyszínek/runCommands/olvasás | A helyen elérhető futtatási parancsok listája |
> | Műveletek | Microsoft. számítás/helyszínek/használat/olvasás | Lekérdezi a szolgáltatási korlátokat és az előfizetés számítási erőforrásainak aktuális használati mennyiségét egy helyen |
> | Műveletek | Microsoft. számítás/helyszínek/méreteinek listáján/olvasás | A helyen elérhető virtuálisgép-méretek felsorolása |
> | Műveletek | Microsoft. számítás/helyszínek/vsmOperations/olvasás | Egy aszinkron művelet állapotának beolvasása a virtuálisgép-méretezési csoport számára a virtuálisgép-futtatókörnyezeti szolgáltatás bővítménnyel |
> | Műveletek | Microsoft. számítás/műveletek/olvasás | A Microsoft. számítási erőforrás-szolgáltatón elérhető műveletek felsorolása |
> | Műveletek | Microsoft. számítás/proximityPlacementGroups/törlés | A közelségi elhelyezési csoport törlése |
> | Műveletek | Microsoft. számítás/proximityPlacementGroups/olvasás | Közelségi elhelyezési csoport tulajdonságainak beolvasása |
> | Műveletek | Microsoft. számítás/proximityPlacementGroups/írás | Új közelségi elhelyezési csoport létrehozása vagy egy meglévő frissítése |
> | Műveletek | Microsoft. számítás/regisztrálás/művelet | Előfizetés regisztrálása a Microsoft. számítási erőforrás-szolgáltatónál |
> | Műveletek | Microsoft. számítás/restorePointCollections/törlés | Törli a visszaállítási pont gyűjteményét és a benne foglalt visszaállítási pontokat |
> | Műveletek | Microsoft. számítás/restorePointCollections/olvasás | Egy visszaállítási pont gyűjtemény tulajdonságainak beolvasása |
> | Műveletek | Microsoft. számítás/restorePointCollections/restorePoints/delete | A visszaállítási pont törlése |
> | Műveletek | Microsoft. számítás/restorePointCollections/restorePoints/olvasás | Egy visszaállítási pont tulajdonságainak beolvasása |
> | Műveletek | Microsoft. számítás/restorePointCollections/restorePoints/retrieveSasUris/művelet | Egy visszaállítási pont tulajdonságainak beolvasása a blob SAS URI-k mellett |
> | Műveletek | Microsoft. számítás/restorePointCollections/restorePoints/írás | Új visszaállítási pont létrehozása |
> | Műveletek | Microsoft. számítás/restorePointCollections/írás | Új visszaállítási pont gyűjtemény létrehozása vagy egy meglévő frissítése |
> | Műveletek | Microsoft. számítás/sharedVMImages/törlés | A lemezkép törlése |
> | Műveletek | Microsoft. számítás/sharedVMImages/olvasás | Lemezkép tulajdonságainak beolvasása |
> | Műveletek | Microsoft. számítási/sharedVMImages/verziók/törlés | Célrégiókba törlése |
> | Műveletek | Microsoft. számítási/sharedVMImages/verziók/olvasás | Célrégiókba tulajdonságainak beolvasása |
> | Műveletek | Microsoft. számítási/sharedVMImages/verziók/replikálás/művelet | Célrégiókba replikálása célként megadott régiókba |
> | Műveletek | Microsoft. számítási/sharedVMImages/verziók/írás | Hozzon létre egy új Célrégiókba, vagy frissítsen egy meglévőt |
> | Műveletek | Microsoft. számítás/sharedVMImages/írás | Új lemezkép létrehozása vagy egy meglévő frissítése |
> | Műveletek | Microsoft. számítás/SKU/READ | Az előfizetéshez elérhető Microsoft. számítási SKU-EK listájának beolvasása |
> | Műveletek | Microsoft. számítás/Pillanatképek/beginGetAccess/művelet | A blob-hozzáféréshez tartozó pillanatkép SAS URI-azonosítójának beolvasása |
> | Műveletek | Microsoft. számítás/Pillanatképek/törlés | Pillanatkép törlése |
> | Műveletek | Microsoft. számítás/Pillanatképek/endGetAccess/művelet | A pillanatkép SAS URI-azonosítójának visszavonása |
> | Műveletek | Microsoft. számítás/Pillanatképek/olvasás | Pillanatkép tulajdonságainak beolvasása |
> | Műveletek | Microsoft. számítás/Pillanatképek/írás | Új pillanatkép létrehozása vagy meglévő frissítése |
> | Műveletek | Microsoft. számítás/regisztráció/művelet | Előfizetés regisztrációjának törlése a Microsoft. számítási erőforrás-szolgáltatónál |
> | Műveletek | Microsoft. számítás/virtualMachines/rögzítés/művelet | A virtuális merevlemezek másolásával rögzíti a virtuális gépet, és létrehoz egy sablont, amely hasonló virtuális gépek létrehozásához használható |
> | Műveletek | Microsoft. számítás/virtualMachines/convertToManagedDisks/művelet | Átalakítja a virtuális gép blob-alapú lemezeit a felügyelt lemezekre. |
> | Műveletek | Microsoft. számítás/virtualMachines/felszabadítás/művelet | Kikapcsolja a virtuális gépet, és felszabadítja a számítási erőforrásokat |
> | Műveletek | Microsoft.Compute/virtualMachines/delete | A virtuális gép törlése |
> | Műveletek | Microsoft. számítás/virtualMachines/Extensions/delete | A virtuálisgép-bővítmény törlése |
> | Műveletek | Microsoft. számítás/virtualMachines/bővítmények/olvasás | Virtuálisgép-bővítmény tulajdonságainak beolvasása |
> | Műveletek | Microsoft. számítás/virtualMachines/bővítmények/írás | Új virtuálisgép-bővítményt hoz létre, vagy frissít egy meglévőt |
> | Műveletek | Microsoft. számítás/virtualMachines/általánosítás/művelet | A virtuális gép állapotának beállítása általánosított értékre, és a virtuális gép előkészítése a rögzítéshez |
> | Műveletek | Microsoft. számítás/virtualMachines/instanceView/olvasás | Lekéri a virtuális gép és az erőforrásainak részletes futásidejű állapotát |
> | DataAction | Microsoft. számítás/virtualMachines/bejelentkezés/művelet | Bejelentkezés egy virtuális gépre normál felhasználóként |
> | DataAction | Microsoft. számítás/virtualMachines/loginAsAdmin/művelet | Jelentkezzen be egy virtuális gépre a Windows rendszergazdai vagy a Linux root felhasználói jogosultságokkal |
> | Műveletek | Microsoft. számítás/virtualMachines/performMaintenance/művelet | Karbantartási műveletet hajt végre a virtuális gépen. |
> | Műveletek | Microsoft. számítási/virtualMachines/erő/művelet | Kikapcsolja a virtuális gépet. Vegye figyelembe, hogy a virtuális gép számlázása továbbra is megtörténik. |
> | Műveletek | Microsoft. számítás/virtualMachines/olvasás | Virtuális gép tulajdonságainak beolvasása |
> | Műveletek | Microsoft. számítás/virtualMachines/újratelepítés/művelet | Virtuális gép újbóli üzembe helyezése |
> | Műveletek | Microsoft. számítás/virtualMachines/rendszerkép/művelet | A különbséglemezek lemezt használó virtuális gép rendszerképének alaphelyzetbe állítása. |
> | Műveletek | Microsoft. számítás/virtualMachines/újraindítás/művelet | Újraindítja a virtuális gépet |
> | Műveletek | Microsoft. számítás/virtualMachines/ParancsFuttatása/művelet | Egy előre definiált parancsfájl végrehajtása a virtuális gépen |
> | Műveletek | Microsoft. számítás/virtualMachines/indítás/művelet | Elindítja a virtuális gépet |
> | Műveletek | Microsoft. számítás/virtualMachines/méreteinek listáján/olvasás | A virtuális gép által frissíthető elérhető méretek listája |
> | Műveletek | Microsoft. számítás/virtualMachines/írás | Új virtuális gép létrehozása vagy meglévő virtuális gép frissítése |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/felszabadítás/művelet | Kikapcsolja és felszabadítja a virtuálisgép-méretezési csoport példányainak számítási erőforrásait  |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/törlés | A virtuálisgép-méretezési csoport törlése |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/törlés/művelet | A virtuálisgép-méretezési csoport példányainak törlése |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/Extensions/delete | A virtuálisgép-méretezési csoport bővítményének törlése |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/bővítmények/olvasás | Egy virtuálisgép-méretezési csoport bővítményének tulajdonságainak beolvasása |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/bővítmények/írás | Létrehoz egy új virtuálisgép-méretezési csoport bővítményt, vagy frissít egy meglévőt. |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/művelet | A Service Fabric virtuálisgép-méretezési csoport platform-frissítési tartományának manuális bejárásával fejezze be a függőben lévő frissítést, amely beragadt |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/instanceView/olvasás | A virtuálisgép-méretezési csoport példány nézetének beolvasása |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/manualUpgrade/művelet | A példányok manuális frissítése a virtuálisgép-méretezési csoport legújabb modelljére |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/networkInterfaces/olvasás | Virtuálisgép-méretezési csoport összes hálózati adapterének tulajdonságainak beolvasása |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/osRollingUpgrade/művelet | Elindítja a működés közbeni frissítést az összes virtuálisgép-méretezési csoport példányainak a legújabb elérhető platform-lemezkép operációsrendszer-verzióra való áthelyezéséhez. |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/osUpgradeHistory/olvasás | Lekéri a virtuálisgép-méretezési csoport operációsrendszer-frissítéseinek előzményeit |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/performMaintenance/művelet | A virtuálisgép-méretezési csoport példányain tervezett karbantartást végez |
> | Műveletek | Microsoft. számítási/virtualMachineScaleSets/erő/művelet | Kikapcsolja a virtuálisgép-méretezési csoport példányait |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/nyilvános IP/olvasás | Virtuálisgép-méretezési csoport összes nyilvános IP-címéhez tartozó tulajdonságok beolvasása |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/olvasás | Virtuálisgép-méretezési csoport tulajdonságainak beolvasása |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/újratelepítés/művelet | A virtuálisgép-méretezési csoport példányainak újbóli üzembe helyezése |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/rendszerkép/művelet | A virtuálisgép-méretezési csoport példányainak rendszerképbe állítása |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/reimageAll/művelet | A virtuálisgép-méretezési csoport példányaihoz tartozó összes lemez (operációsrendszer-lemez és adatlemez) rendszerképének alaphelyzetbe állítása  |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/újraindítás/művelet | Újraindítja a virtuálisgép-méretezési csoport példányait |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/rollingUpgrades/megszakítás/művelet | Egy virtuálisgép-méretezési csoport működés közbeni frissítésének megszakítása |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/rollingUpgrades/olvasás | Virtuálisgép-méretezési csoport legutóbbi működés közbeni frissítési állapotának beolvasása |
> | Műveletek | Microsoft. számítási/virtualMachineScaleSets/méretezés/művelet | Annak ellenőrzése, hogy egy meglévő virtuálisgép-méretezési csoport méretezhető-e/méretezhető-e a megadott példányok száma szerint |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/SKU/READ | Egy meglévő virtuálisgép-méretezési csoport érvényes SKU-készletét listázza |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/indítás/művelet | Elindítja a virtuálisgép-méretezési csoport példányait |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/virtualMachines/felszabadítás/művelet | Kikapcsolja a virtuálisgép-méretezési csoportokban lévő virtuális gépek számítási erőforrásait, és kibocsátja azokat. |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/virtualMachines/delete | Egy virtuálisgép-méretezési csoportba tartozó virtuális gép törlése. |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/virtualMachines/instanceView/READ | Egy virtuálisgép-méretezési csoportba tartozó virtuális gép példány nézetét kérdezi le. |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/nyilvános IP/READ | A virtuálisgép-méretezési csoport használatával létrehozott nyilvános IP-cím tulajdonságainak beolvasása. A virtuálisgép-méretezési csoport legfeljebb egy nyilvános IP-címet képes létrehozni ipconfiguration (magánhálózati IP) |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/olvasás | A virtuálisgép-méretezési csoport használatával létrehozott hálózati adapter egy vagy több IP-konfigurációjának tulajdonságainak beolvasása. Az IP-konfigurációk privát IP-címeket jelentenek |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/virtualMachines/networkInterfaces/READ | Virtuálisgép-méretezési csoport használatával létrehozott virtuális gép egy vagy több hálózati adaptere tulajdonságainak beolvasása |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/virtualMachines/performMaintenance/művelet | A virtuálisgép-méretezési csoportokban lévő virtuálisgép-példány tervezett karbantartásának végrehajtása |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/virtualMachines/erő/művelet | A virtuálisgép-méretezési csoportokban lévő virtuálisgép-példányok kikapcsolását. |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/virtualMachines/olvasás | Egy virtuálisgép-méretezési csoportba tartozó virtuális gép tulajdonságainak lekérése |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/virtualMachines/újratelepítés/művelet | Virtuálisgép-méretezési csoportokban lévő virtuálisgép-példány újratelepítése |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/virtualMachines/rendszerkép/művelet | A virtuálisgép-méretezési csoportokban lévő virtuálisgép-példányok rendszerképének alaphelyzetbe állítása. |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/virtualMachines/reimageAll/művelet | A virtuálisgép-méretezési csoportokban lévő virtuálisgép-példányok összes lemezének (operációsrendszer-lemezének és adatlemezének) rendszerképének alaphelyzetbe állítása. |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/virtualMachines/újraindítás/művelet | A virtuálisgép-méretezési csoportokban lévő virtuálisgép-példányok újraindítása. |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/virtualMachines/ParancsFuttatása/művelet | Egy előre definiált parancsfájl végrehajtása virtuálisgép-méretezési csoport virtuálisgép-példányain. |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/virtualMachines/indítás/művelet | A virtuálisgép-méretezési csoportokban elindítja a virtuálisgép-példányt. |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/virtualMachines/írás | Egy virtuálisgép-méretezési csoportba tartozó virtuális gép tulajdonságainak frissítése |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/méreteinek listáján/olvasás | A virtuálisgép-méretezési csoportba tartozó virtuális gépek létrehozásához vagy frissítéséhez elérhető méretek listázása |
> | Műveletek | Microsoft. számítás/virtualMachineScaleSets/írás | Új virtuálisgép-méretezési csoport létrehozása vagy egy meglévő frissítése |

## <a name="microsoftconsumption"></a>Microsoft. felhasználás

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. fogyasztás/aggregatedcost/olvasás | Felügyeleti csoport AggregatedCost listázása. |
> | Műveletek | Microsoft. fogyasztás/egyenlegek/olvasás | Egy felügyeleti csoport számlázási időszakának kihasználtsági összegzésének listázása. |
> | Műveletek | Microsoft. felhasználás/költségvetés/törlés | A költségvetést egy előfizetéssel vagy egy felügyeleti csoporttal törölheti. |
> | Műveletek | Microsoft. fogyasztás/költségvetés/olvasás | Egy előfizetés vagy egy felügyeleti csoport költségvetésének listázása. |
> | Műveletek | Microsoft. fogyasztás/költségvetés/írás | Előfizetéssel vagy egy felügyeleti csoporttal hozza létre és frissíti a költségvetést. |
> | Műveletek | Microsoft. fogyasztás/díjak/olvasás | Díjak listázása |
> | Műveletek | Microsoft. felhasználás/kreditek/olvasás | Kreditek listázása |
> | Műveletek | Microsoft. felhasználás/események/olvasás | Események listázása |
> | Műveletek | Microsoft. fogyasztás/externalBillingAccounts/címkék/olvasás | Az EA és az előfizetések címkéit listázhatja. |
> | Műveletek | Microsoft. fogyasztás/externalSubscriptions/címkék/olvasás | Az EA és az előfizetések címkéit listázhatja. |
> | Műveletek | Microsoft. fogyasztás/előrejelzés/olvasás | Előrejelzések listázása |
> | Műveletek | Microsoft. fogyasztás/tételek/olvasás | Tételek listázása |
> | Műveletek | Microsoft. fogyasztás/piactér/olvasás | A piactér erőforrás-használati adatainak listázása az EA és a webdirect-előfizetések hatóköréhez. |
> | Műveletek | Microsoft. fogyasztás/operationresults/olvasás | Operationresults listázása |
> | Műveletek | Microsoft. felhasználás/műveletek/olvasás | A Microsoft. reakciós erőforrás-szolgáltató által támogatott összes művelet listázása. |
> | Műveletek | Microsoft. fogyasztás/operationstatus/olvasás | Operationstatus listázása |
> | Műveletek | Microsoft. fogyasztás/pricesheets/olvasás | Egy előfizetés vagy egy felügyeleti csoport Pricesheets-adatbázisának listázása. |
> | Műveletek | Microsoft. felhasználás/regisztráció/művelet | Regisztrálás a használati RP-be |
> | Műveletek | Microsoft. fogyasztás/reservationDetails/olvasás | A fenntartott példányok kihasználtsági adatait foglalási sorrend vagy felügyeleti csoportok szerint sorolja fel. A részletek száma napi szintenként történik. |
> | Műveletek | Microsoft. fogyasztás/reservationRecommendations/olvasás | A fenntartott példányokra vonatkozó egyszeri vagy megosztott javaslatok listázása egy előfizetéshez. |
> | Műveletek | Microsoft. fogyasztás/reservationSummaries/olvasás | A fenntartott példányok kihasználtsági összegzésének listázása foglalási sorrend vagy felügyeleti csoportok szerint. Az összegző adatokat havi vagy napi szinten kell megválasztani. |
> | Műveletek | Microsoft. fogyasztás/reservationTransactions/olvasás | A fenntartott példányok tranzakciós előzményeinek listázása felügyeleti csoportok szerint. |
> | Műveletek | Microsoft. felhasználás/címkék/olvasás | Az EA és az előfizetések címkéit listázhatja. |
> | Műveletek | Microsoft. fogyasztás/bérlők/olvasás | Bérlők listázása |
> | Műveletek | Microsoft. fogyasztás/bérlők/regisztráció/művelet | Regisztrálja a Microsoft.-alkalmazás hatókörén belüli műveletet. |
> | Műveletek | Microsoft. felhasználás/feltételek/olvasás | Egy előfizetés vagy egy felügyeleti csoport feltételeit sorolja fel. |
> | Műveletek | Microsoft. fogyasztás/usageDetails/olvasás | Az EA-és webdirect-előfizetések hatókörének használati adatainak listázása. |

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. ContainerInstance/containerGroups/containers/buildlogs/READ | Hozzon létre egy adott tárolóhoz tartozó Build-naplókat. |
> | Műveletek | Microsoft. ContainerInstance/containerGroups/containers/exec/művelet | Egy adott tárolóban futtatható. |
> | Műveletek | Microsoft. ContainerInstance/containerGroups/tárolók/naplók/olvasás | Egy adott tárolóhoz tartozó naplók beolvasása. |
> | Műveletek | Microsoft. ContainerInstance/containerGroups/delete | Törölje az adott tároló csoportot. |
> | Műveletek | Microsoft. ContainerInstance/containerGroups/operationResults/READ | Aszinkron művelet eredményének beolvasása |
> | Műveletek | Microsoft. ContainerInstance/containerGroups/Providers/Microsoft. bepillantást/diagnosticSettings/READ | A tároló csoport diagnosztikai beállításainak beolvasása. |
> | Műveletek | Microsoft. ContainerInstance/containerGroups/Providers/Microsoft. bepillantások/diagnosticSettings/írás | Létrehozza vagy frissíti a tároló csoport diagnosztikai beállításait. |
> | Műveletek | Microsoft. ContainerInstance/containerGroups/Providers/Microsoft. bepillantást/metricDefinitions/READ | A tároló csoport elérhető metrikáinak beolvasása. |
> | Műveletek | Microsoft. ContainerInstance/containerGroups/READ | Minden tároló csoport beolvasása. |
> | Műveletek | Microsoft. ContainerInstance/containerGroups/újraindítás/művelet | Egy adott tároló csoport újraindítása. |
> | Műveletek | Microsoft. ContainerInstance/containerGroups/Start/művelet | Egy adott tároló csoportot indít el. |
> | Műveletek | Microsoft. ContainerInstance/containerGroups/leállítás/művelet | Leállítja egy adott tároló csoportot. A számítási erőforrások fel lesznek foglalva, és a számlázás leáll. |
> | Műveletek | Microsoft. ContainerInstance/containerGroups/Write | Hozzon létre vagy frissítsen egy adott tároló csoportot. |
> | Műveletek | Microsoft. ContainerInstance/Locations/cachedImages/READ | Az előfizetéshez tartozó gyorsítótárazott rendszerképek beolvasása egy régióban. |
> | Műveletek | Microsoft. ContainerInstance/Locations/képességek/olvasás | Egy régió képességeinek beszerzése. |
> | Műveletek | Microsoft. ContainerInstance/Locations/deleteVirtualNetworkOrSubnets/Action | Értesíti a Microsoft. ContainerInstance, hogy a virtuális hálózat vagy az alhálózat törölve lett. |
> | Műveletek | Microsoft. ContainerInstance/Locations/Operations/READ | Az Azure Container instance Service műveleteinek listázása. |
> | Műveletek | Microsoft. ContainerInstance/Locations/használati adatok/olvasás | Egy adott régió használatának beolvasása. |
> | Műveletek | Microsoft. ContainerInstance/Operations/READ | Az Azure Container instance Service műveleteinek listázása. |
> | Műveletek | Microsoft. ContainerInstance/regisztráció/művelet | Regisztrálja az előfizetést a tároló példány erőforrás-szolgáltatója számára, és lehetővé teszi a tároló csoportok létrehozását. |
> | Műveletek | Microsoft. ContainerInstance/serviceassociationlinks/delete | Törölje az Azure Container instance erőforrás-szolgáltató által létrehozott Service társítási hivatkozást egy alhálózaton. |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. ContainerRegistry/checkNameAvailability/READ | Ellenőrzi, hogy a tároló beállításjegyzékének neve használható-e. |
> | Műveletek | Microsoft. ContainerRegistry/Locations/deleteVirtualNetworkOrSubnets/Action | Értesíti a Microsoft. ContainerRegistry, hogy a virtuális hálózat vagy az alhálózat törölve lett |
> | Műveletek | Microsoft. ContainerRegistry/Locations/operationResults/READ | Aszinkron művelet eredményének beolvasása |
> | Műveletek | Microsoft. ContainerRegistry/Operations/READ | Az összes rendelkezésre álló Azure Container Registry REST API művelet listája |
> | Műveletek | Microsoft. ContainerRegistry/regisztráció/művelet | Regisztrálja az előfizetést a tároló-beállításjegyzék erőforrás-szolgáltatója számára, és lehetővé teszi a tároló-nyilvántartók létrehozását. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartások/összetevők/törlés | Összetevő törlése egy tároló-beállításjegyzékben. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartások/buildek/megszakítás/művelet | Egy meglévő Build megszakítása. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartások/buildek/getLogLink/művelet | Lekéri a Build naplók letöltésére szolgáló hivatkozást. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartások/buildek/olvasás | Lekéri a megadott Build vagy listázza a megadott tároló beállításjegyzékének összes buildjét. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartások/buildek/írás | A megadott paraméterekkel frissíti egy tároló-beállításjegyzék buildjét. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartó/buildTasks/delete | Kiépítési feladatot töröl egy tároló-beállításjegyzékből. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartó/buildTasks/listSourceRepositoryProperties/művelet | A felépítési feladat forrás vezérlőelemének tulajdonságait sorolja fel. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartó/buildTasks/READ | Lekéri a megadott felépítési feladat tulajdonságait, vagy felsorolja a megadott tároló-beállításjegyzékhez tartozó összes felépítési feladatot. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartások/buildTasks/lépések/törlés | Kiépített lépést töröl egy felépítési feladatból. |
> | Műveletek | Microsoft. ContainerRegistry/kibocsátásiegység-forgalmi/buildTasks/lépések/listBuildArguments/művelet | Felsorolja a fordítási argumentumokat a létrehozási lépésekhez, beleértve a titkos argumentumokat is. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartások/buildTasks/lépések/olvasás | Lekéri a megadott Build lépés tulajdonságait, vagy felsorolja a megadott Build feladathoz tartozó összes összeállítási lépést. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartások/buildTasks/lépések/írás | Létrehoz vagy frissít egy Build-lépést a megadott paraméterekkel rendelkező Build feladathoz. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartások/buildTasks/írás | Létrehoz vagy frissít egy Build feladatot egy tároló-beállításjegyzékhez a megadott paraméterekkel. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartások/törlés | Töröl egy tároló-beállításjegyzéket. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartó/eventGridFilters/delete | Egy Event Grid-szűrőt töröl egy tároló-beállításjegyzékből. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartó/eventGridFilters/READ | Lekéri a megadott Event Grid-szűrő tulajdonságait, vagy felsorolja az összes Event Grid-szűrőt a megadott tároló-beállításjegyzékhez. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartások/eventGridFilters/írás | Létrehoz vagy frissít egy Event Grid-szűrőt egy tároló-beállításjegyzékhez a megadott paraméterekkel. |
> | Műveletek | Microsoft. ContainerRegistry/kibocsátásiegység-forgalmi/generateCredentials/művelet | Kulcsok generálása egy megadott tároló-beállításjegyzék jogkivonatához. |
> | Műveletek | Microsoft. ContainerRegistry/kibocsátásiegység-forgalmi/getBuildSourceUploadUrl/művelet | Lekéri a felhasználó feltöltési helyét, hogy fel tudja tölteni a forrást. |
> | Műveletek | Microsoft. ContainerRegistry/kibocsátásiegység-forgalmi/importImage/művelet | Rendszerkép importálása a tároló-beállításjegyzékbe a megadott paraméterekkel. |
> | Műveletek | Microsoft. ContainerRegistry/kibocsátásiegység-forgalmi/listBuildSourceUploadUrl/művelet | Forrás feltöltési URL-címének beolvasása egy tároló-beállításjegyzékhez. |
> | Műveletek | Microsoft. ContainerRegistry/kibocsátásiegység-forgalmi/listCredentials/művelet | Felsorolja a megadott tároló-beállításjegyzék bejelentkezési hitelesítő adatait. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartó/listPolicies/READ | Felsorolja a megadott tároló-beállításjegyzék szabályzatait |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartó/listUsages/READ | Felsorolja a megadott tároló-beállításjegyzék kvótájának használatát. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartások/metaadatok/olvasás | Beolvassa egy tároló-beállításjegyzék egy adott tárházának metaadatait |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartások/metaadatok/írás | Frissíti egy tároló-beállításjegyzék tárházának metaadatait |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartó/operationStatuses/READ | Beállításjegyzék aszinkron műveleti állapotának beolvasása |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartó/lekérés/olvasás | Lemezképek lekérése vagy beolvasása egy tároló-beállításjegyzékből. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartó/leküldéses/írási | Képek leküldése vagy írása egy tároló-beállításjegyzékbe. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartó/karanténba helyezés/olvasás | Karanténba helyezett lemezképek lekérése vagy beolvasása a Container registryből |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartások/karanténba helyezés/írás | Karanténba helyezett lemezképek karantén állapotának írása/módosítása |
> | Műveletek | Microsoft. ContainerRegistry/kibocsátásiegység-forgalmi/queueBuild/művelet | Létrehoz egy új buildet a kérés paraméterei alapján, és hozzáadja azt a Build-várólistához. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartások/olvasás | Lekéri a megadott tároló-beállításjegyzék tulajdonságait, vagy felsorolja a megadott erőforráscsoport vagy előfizetés alatt lévő összes tároló-bejegyzést. |
> | Műveletek | Microsoft. ContainerRegistry/kibocsátásiegység-forgalmi/regenerateCredential/művelet | Újra létrehozza a megadott tároló-beállításjegyzékhez tartozó bejelentkezési hitelesítő adatok egyikét. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartások/replikálás/törlés | Törli a replikálást egy tároló-beállításjegyzékből. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartások/replikálás/operationStatuses/olvasás | Replikációs aszinkron műveleti állapot beolvasása |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartások/replikálás/olvasás | Lekéri a megadott replikálás tulajdonságait, vagy felsorolja a megadott tároló-beállításjegyzék összes replikációját. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartások/replikálás/írás | Létrehoz vagy frissít egy tároló-beállításjegyzék replikálását a megadott paraméterekkel. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartások/Futtatás/megszakítás/művelet | Meglévő Futtatás megszakítása. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartók/Run/listLogSasUrl/Action | Lekéri a log SAS URL-címét a futtatáshoz. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartó/Futtatás/olvasás | Lekéri egy futtatási tulajdonságot egy tároló beállításjegyzékében vagy a lista futtatásakor. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartó/Futtatás/írás | Frissíti a futtatást. |
> | Műveletek | Microsoft. ContainerRegistry/kibocsátásiegység-forgalmi/scheduleRun/művelet | Futtasson egy futtatást egy tároló-beállításjegyzékben. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartó/scopeMaps/delete | Hatókör-hozzárendelést töröl egy tároló-beállításjegyzékből. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartó/scopeMaps/operationStatuses/READ | A hatókör-leképezés aszinkron műveleti állapotának beolvasása. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartó/scopeMaps/READ | Lekéri a megadott hatókör-leképezés tulajdonságait, vagy felsorolja a megadott tároló-beállításjegyzékhez tartozó összes hatókör-leképezést. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartások/scopeMaps/írás | Egy tároló-beállításjegyzékhez tartozó hatókör-hozzárendelést hoz létre vagy frissít a megadott paraméterekkel. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartások/aláírás/írás | A tartalom megbízhatóságának leküldése/lekérése egy tároló-beállításjegyzékben. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartások/feladatok/törlés | Töröl egy feladatot egy tároló-beállításjegyzékhez. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartások/feladatok/listDetails/művelet | A tároló-beállításjegyzékhez tartozó feladat összes adatának listázása. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartások/feladatok/olvasás | Feladat beolvasása egy tároló-beállításjegyzékhez vagy az összes feladat listázása. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartások/feladatok/írás | Létrehoz vagy frissít egy feladatot egy tároló-beállításjegyzékhez. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartók/jogkivonatok/törlés | Töröl egy tokent egy tároló-beállításjegyzékből. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartó/token/operationStatuses/READ | Token aszinkron műveleti állapotának beolvasása. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartások/tokenek/olvasás | Lekéri a megadott jogkivonat tulajdonságait, vagy felsorolja a megadott tároló-beállításjegyzékhez tartozó összes jogkivonatot. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartó/jogkivonatok/írás | Létrehoz vagy frissít egy tokent egy tároló-beállításjegyzékhez a megadott paraméterekkel. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartások/updatePolicies/írás | Frissíti a megadott tároló-beállításjegyzék szabályzatait |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartások/webhookok/törlés | Egy webhookot töröl egy tároló-beállításjegyzékből. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartások/webhookok/getCallbackConfig/művelet | Beolvassa a webhookhoz tartozó szolgáltatás-URI és egyéni fejlécek konfigurációját. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartások/webhookok/listEvents/művelet | Felsorolja a megadott webhook legutóbbi eseményeit. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartások/webhookok/operationStatuses/olvasás | Webhook aszinkron műveleti állapotának beolvasása |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartó/webhook/ping/művelet | Elindít egy ping eseményt a webhookba. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartó/webhook/olvasás | Lekéri a megadott webhook tulajdonságait, vagy felsorolja a megadott tároló-beállításjegyzékhez tartozó összes webhookot. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartó/webhook/írás | Létrehoz vagy frissít egy webhookot egy tároló-beállításjegyzékhez a megadott paraméterekkel. |
> | Műveletek | Microsoft. ContainerRegistry/nyilvántartások/írás | Létrehoz vagy frissít egy tároló-beállításjegyzéket a megadott paraméterekkel. |

## <a name="microsoftcontainerservice"></a>Microsoft. Tárolószolgáltatás

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Tárolószolgáltatás/containerServices/delete | Tároló szolgáltatás törlése |
> | Műveletek | Microsoft. Tárolószolgáltatás/containerServices/READ | Tároló szolgáltatás beszerzése |
> | Műveletek | Microsoft. Tárolószolgáltatás/containerServices/Write | Létrehoz egy új Container Service-t, vagy frissít egy meglévőt |
> | Műveletek | Microsoft. Tárolószolgáltatás/Locations/operationresults/READ | Aszinkron műveleti eredmény állapotának beolvasása |
> | Műveletek | Microsoft. Tárolószolgáltatás/Locations/Operations/READ | Gets the status of an asynchronous operation |
> | Műveletek | Microsoft. Tárolószolgáltatás/Locations/vezényel/olvasás | A támogatott rendszerszervezők listája |
> | Műveletek | Microsoft. Tárolószolgáltatás/managedClusters/accessProfiles/listCredential/művelet | Felügyelt fürt hozzáférési profiljának beolvasása szerepkör neve alapján a lista hitelesítő adataival |
> | Műveletek | Microsoft. Tárolószolgáltatás/managedClusters/accessProfiles/READ | Felügyelt fürt hozzáférési profiljának beolvasása szerepkör neve alapján |
> | Műveletek | Microsoft. Tárolószolgáltatás/managedClusters/agentPools/delete | Ügynök-készlet törlése |
> | Műveletek | Microsoft. Tárolószolgáltatás/managedClusters/agentPools/READ | Ügynök készletének beolvasása |
> | Műveletek | Microsoft. Tárolószolgáltatás/managedClusters/agentPools/upgradeProfiles/READ | Az ügynök-készlet frissítési profiljának beolvasása |
> | Műveletek | Microsoft. Tárolószolgáltatás/managedClusters/agentPools/Write | Új ügynök készletének létrehozása vagy meglévő frissítése |
> | Műveletek | Microsoft. Tárolószolgáltatás/managedClusters/availableAgentPoolVersions/READ | Beolvassa a fürt rendelkezésre álló ügynök-készletének verzióját. |
> | Műveletek | Microsoft. Tárolószolgáltatás/managedClusters/delete | Felügyelt fürt törlése |
> | Műveletek | Microsoft. Tárolószolgáltatás/managedClusters/érzékelők/olvasás | Felügyelt fürt Detektorának beolvasása |
> | Műveletek | Microsoft. Tárolószolgáltatás/managedClusters/diagnosticsState/READ | A fürt diagnosztikai állapotának beolvasása |
> | Műveletek | Microsoft. Tárolószolgáltatás/managedClusters/listClusterAdminCredential/művelet | Felügyelt fürt clusterAdmin hitelesítő adatainak listázása |
> | Műveletek | Microsoft. Tárolószolgáltatás/managedClusters/listClusterMonitoringUserCredential/művelet | Felügyelt fürt clusterMonitoringUser hitelesítő adatainak listázása |
> | Műveletek | Microsoft. Tárolószolgáltatás/managedClusters/listClusterUserCredential/művelet | Felügyelt fürt clusterUser hitelesítő adatainak listázása |
> | Műveletek | Microsoft. Tárolószolgáltatás/managedClusters/privateEndpointConnectionsApproval/művelet | Determines if user is allowed to approve a private endpoint connection |
> | Műveletek | Microsoft. Tárolószolgáltatás/managedClusters/READ | Felügyelt fürt beszerzése |
> | Műveletek | Microsoft. Tárolószolgáltatás/managedClusters/resetAADProfile/művelet | Felügyelt fürt HRE-profiljának alaphelyzetbe állítása |
> | Műveletek | Microsoft. Tárolószolgáltatás/managedClusters/resetServicePrincipalProfile/művelet | Felügyelt fürt egyszerű szolgáltatásnév-profiljának alaphelyzetbe állítása |
> | Műveletek | Microsoft. Tárolószolgáltatás/managedClusters/rotateClusterCertificates/művelet | Felügyelt fürt tanúsítványainak elforgatása |
> | Műveletek | Microsoft. Tárolószolgáltatás/managedClusters/upgradeProfiles/READ | A fürt frissítési profiljának beolvasása |
> | Műveletek | Microsoft. Tárolószolgáltatás/managedClusters/Write | Létrehoz egy új felügyelt fürtöt, vagy frissít egy meglévőt |
> | Műveletek | Microsoft. Tárolószolgáltatás/openShiftClusters/delete | Nyitott SHIFT-fürt törlése |
> | Műveletek | Microsoft. Tárolószolgáltatás/openShiftClusters/READ | Nyitott SHIFT-fürt beszerzése |
> | Műveletek | Microsoft. Tárolószolgáltatás/openShiftClusters/Write | Új nyitott SHIFT-fürtöt hoz létre, vagy frissít egy meglévőt |
> | Műveletek | Microsoft. Tárolószolgáltatás/openShiftManagedClusters/delete | Nyitott eltolású felügyelt fürt törlése |
> | Műveletek | Microsoft. Tárolószolgáltatás/openShiftManagedClusters/READ | Nyitott eltolású felügyelt fürt beszerzése |
> | Műveletek | Microsoft. Tárolószolgáltatás/openShiftManagedClusters/Write | Létrehoz egy új, nyitott eltolású felügyelt fürtöt, vagy frissít egy meglévőt |
> | Műveletek | Microsoft. Tárolószolgáltatás/Operations/READ | A Microsoft. Tárolószolgáltatás erőforrás-szolgáltatón elérhető műveletek felsorolása |
> | Műveletek | Microsoft. Tárolószolgáltatás/regisztráció/művelet | Előfizetés regisztrálása a Microsoft. Tárolószolgáltatás erőforrás-szolgáltatónál |
> | Műveletek | Microsoft. Tárolószolgáltatás/regisztráció/művelet | Előfizetés regisztrációjának törlése a Microsoft. Tárolószolgáltatás erőforrás-szolgáltatóval |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. CostManagement/riasztások/írás | Riasztások frissítése. |
> | Műveletek | Microsoft. CostManagement/cloudConnectors/delete | A megadott cloudConnector törlése. |
> | Műveletek | Microsoft. CostManagement/cloudConnectors/READ | A hitelesített felhasználó cloudConnectors listázása. |
> | Műveletek | Microsoft. CostManagement/cloudConnectors/Write | A megadott cloudConnector létrehozása vagy frissítése. |
> | Műveletek | Microsoft. CostManagement/méretek/olvasás | A hatókör összes támogatott dimenziójának listázása. |
> | Műveletek | Microsoft. CostManagement/export/művelet | Futtassa a megadott exportálást. |
> | Műveletek | Microsoft. CostManagement/export/delete | A megadott exportálás törlése. |
> | Műveletek | Microsoft. CostManagement/exportálás/olvasás | Az export hatókör szerinti listázása. |
> | Műveletek | Microsoft. CostManagement/exportálás/Futtatás/művelet | Exportálás futtatása. |
> | Műveletek | Microsoft. CostManagement/exportálás/írás | A megadott exportálás létrehozása vagy frissítése. |
> | Műveletek | Microsoft. CostManagement/externalBillingAccounts/dimenzió/olvasás | A külső BillingAccounts összes támogatott dimenziójának listázása. |
> | Műveletek | Microsoft. CostManagement/externalBillingAccounts/externalSubscriptions/READ | A hitelesített felhasználó externalBillingAccount belüli externalSubscriptions listázása. |
> | Műveletek | Microsoft. CostManagement/externalBillingAccounts/előrejelzés/művelet | A külső BillingAccounts vonatkozó használati adatok előrejelzése. |
> | Műveletek | Microsoft. CostManagement/externalBillingAccounts/előrejelzés/olvasás | A külső BillingAccounts vonatkozó használati adatok előrejelzése. |
> | Műveletek | Microsoft. CostManagement/externalBillingAccounts/Query/Action | A külső BillingAccounts vonatkozó használati adatok lekérdezése. |
> | Műveletek | Microsoft. CostManagement/externalBillingAccounts/Query/READ | A külső BillingAccounts vonatkozó használati adatok lekérdezése. |
> | Műveletek | Microsoft. CostManagement/externalBillingAccounts/READ | A hitelesített felhasználó externalBillingAccounts listázása. |
> | Műveletek | Microsoft. CostManagement/externalSubscriptions/dimensions/READ | A külső előfizetés összes támogatott dimenziójának listázása. |
> | Műveletek | Microsoft. CostManagement/externalSubscriptions/előrejelzés/művelet | A külső BillingAccounts vonatkozó használati adatok előrejelzése. |
> | Műveletek | Microsoft. CostManagement/externalSubscriptions/előrejelzés/olvasás | A külső BillingAccounts vonatkozó használati adatok előrejelzése. |
> | Műveletek | Microsoft. CostManagement/externalSubscriptions/Query/Action | Külső előfizetés használati adatait kérdezheti le. |
> | Műveletek | Microsoft. CostManagement/externalSubscriptions/Query/READ | Külső előfizetés használati adatait kérdezheti le. |
> | Műveletek | Microsoft. CostManagement/externalSubscriptions/READ | A hitelesített felhasználó externalSubscriptions listázása. |
> | Műveletek | Microsoft. CostManagement/externalSubscriptions/Write | A externalSubscription társított felügyeleti csoportjának frissítése |
> | Műveletek | Microsoft. CostManagement/előrejelzés/művelet | Használati adatok előrejelzése hatókör alapján. |
> | Műveletek | Microsoft. CostManagement/előrejelzés/olvasás | Használati adatok előrejelzése hatókör alapján. |
> | Műveletek | Microsoft. CostManagement/Operations/READ | A Microsoft. CostManagement erőforrás-szolgáltató által támogatott összes művelet listázása. |
> | Műveletek | Microsoft. CostManagement/lekérdezés/művelet | Használati adatok lekérdezése hatókör alapján. |
> | Műveletek | Microsoft. CostManagement/lekérdezés/olvasás | Használati adatok lekérdezése hatókör alapján. |
> | Műveletek | Microsoft. CostManagement/regisztráció/művelet | Regisztrálja a Microsoft. CostManagement hatókörére vonatkozó műveletet egy előfizetés alapján. |
> | Műveletek | Microsoft. CostManagement/jelentések/művelet | A használati adatok hatókör szerint ütemezhetnek jelentéseket. |
> | Műveletek | Microsoft. CostManagement/jelentések/olvasás | A használati adatok hatókör szerint ütemezhetnek jelentéseket. |
> | Műveletek | Microsoft. CostManagement/bérlők/regisztráció/művelet | A Microsoft. CostManagement hatókörére vonatkozó művelet regisztrálása bérlő által. |
> | Műveletek | Microsoft. CostManagement/views/Action | Nézet létrehozása |
> | Műveletek | Microsoft. CostManagement/views/delete | Mentett nézetek törlése. |
> | Műveletek | Microsoft. CostManagement/megtekintés/olvasás | Az összes mentett nézet listázása. |
> | Műveletek | Microsoft. CostManagement/megtekintés/írás | Frissítési nézet. |

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. DataBox/Jobs/bookShipmentPickUp/művelet | Lehetővé teszi a visszaszállítások felvételének lefoglalását. |
> | Műveletek | Microsoft. DataBox/feladatok/megszakítás/művelet | Megszakítja a folyamatban lévő rendelést. |
> | Műveletek | Microsoft. DataBox/feladatok/törlés | Megrendelések törlése |
> | Műveletek | Microsoft. DataBox/Jobs/listCredentials/művelet | Felsorolja a rendeléshez kapcsolódó titkosítatlan hitelesítő adatokat. |
> | Műveletek | Microsoft. DataBox/feladatok/olvasás | Megrendelések listázása vagy beolvasása |
> | Műveletek | Microsoft. DataBox/feladatok/írás | Megrendelések létrehozása vagy frissítése |
> | Műveletek | Microsoft. DataBox/Locations/availableSkus/Action | Ez a metódus visszaadja az elérhető SKU-ket tartalmazó listát. |
> | Műveletek | Microsoft. DataBox/Locations/availableSkus/READ | Az elérhető SKU-ket listázhatja vagy beolvashatja |
> | Műveletek | Microsoft. DataBox/Locations/operationResults/READ | A művelet eredményeinek listázása vagy beolvasása |
> | Műveletek | Microsoft. DataBox/Locations/regionConfiguration/Action | Ez a metódus a régió konfigurációit adja vissza. |
> | Műveletek | Microsoft. DataBox/Locations/validateAddress/Action | Ellenőrzi a szállítási címet, és alternatív címeket biztosít, ha vannak ilyenek. |
> | Műveletek | Microsoft. DataBox/Locations/validateInputs/Action | Ez a metódus az összes érvényesítési típust támogatja. |
> | Műveletek | Microsoft. DataBox/Operations/READ | A műveletek listázása vagy beolvasása |
> | Műveletek | Microsoft. DataBox/regisztráció/művelet | Szolgáltató regisztrálása a Microsoft. Databox |
> | Műveletek | Microsoft. DataBox/előfizetések/resourceGroups/moveResources/művelet | Ez a metódus végrehajtja az erőforrás-áthelyezést. |
> | Műveletek | Microsoft. DataBox/előfizetések/resourceGroups/validateMoveResources/művelet | Ez a metódus ellenőrzi, hogy engedélyezett-e az erőforrás áthelyezése. |
> | Műveletek | Microsoft. DataBox/regisztráció/művelet | A Microsoft. Databox szolgáltató regisztrációjának megszüntetése |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/riasztások/olvasás | A riasztások listája vagy beolvasása |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/delete | A sávszélesség-ütemtervek törlése |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/operationResults/READ | A művelet eredményének megjelenítése vagy beolvasása |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/READ | A sávszélesség-ütemtervek listája vagy beolvasása |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/READ | A sávszélesség-ütemtervek listája vagy beolvasása |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/Write | A sávszélesség-ütemtervek létrehozása vagy frissítése |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/delete | A Data Box Edge-eszközök törlése |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/művelet | Frissítések letöltése az eszközön |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/művelet | Erőforrás-kibővített információk beolvasása |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/installUpdates/művelet | Frissítések telepítése az eszközön |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/feladatok/olvasás | A feladatok listája vagy beolvasása |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/networkSettings/READ | Az eszköz hálózati beállításainak listája vagy beolvasása |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Nodes/READ | A csomópontok listája vagy beolvasása |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/operationResults/READ | A művelet eredményének megjelenítése vagy beolvasása |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/operationsStatus/READ | A művelet állapotának listája vagy beolvasása |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Orders/delete | Törli a rendeléseket |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Orders/operationResults/READ | A művelet eredményének megjelenítése vagy beolvasása |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Orders/READ | A megrendelések listája vagy beolvasása |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Orders/READ | A megrendelések listája vagy beolvasása |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Orders/Write | Létrehozza vagy frissíti a rendeléseket |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/READ | A Data Box Edge eszközök listája vagy beolvasása |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/READ | A Data Box Edge eszközök listája vagy beolvasása |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/READ | A Data Box Edge eszközök listája vagy beolvasása |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/roles/delete | A szerepkörök törlése |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/roles/operationResults/READ | A művelet eredményének megjelenítése vagy beolvasása |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/szerepkörök/olvasás | A szerepkörök listája vagy beolvasása |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/szerepkörök/olvasás | A szerepkörök listája vagy beolvasása |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/roles/Write | Szerepkörök létrehozása vagy frissítése |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/művelet | Frissítések keresése |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/securitySettings/operationResults/READ | A művelet eredményének megjelenítése vagy beolvasása |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/securitySettings/Update/Action | Biztonsági beállítások frissítése |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/shares/delete | A megosztások törlése |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/shares/operationResults/READ | A művelet eredményének megjelenítése vagy beolvasása |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/shares/READ | A megosztások listája vagy beolvasása |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/shares/READ | A megosztások listája vagy beolvasása |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/shares/refresh/Action | A megosztási metaadatok frissítése a felhőből származó adatokkal |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/shares/Write | A megosztások létrehozása vagy frissítése |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/delete | A Storage-fiók hitelesítő adatainak törlése |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/operationResults/READ | A művelet eredményének megjelenítése vagy beolvasása |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/READ | A Storage-fiók hitelesítő adatainak listája vagy beolvasása |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/READ | A Storage-fiók hitelesítő adatainak listája vagy beolvasása |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/Write | A Storage-fiók hitelesítő adatainak létrehozása vagy frissítése |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/trigger/delete | Az eseményindítók törlése |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/triggerek/operationResults/READ | A művelet eredményének megjelenítése vagy beolvasása |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/triggerek/olvasás | Az eseményindítók listája vagy beolvasása |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/triggerek/olvasás | Az eseményindítók listája vagy beolvasása |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/triggerek/írás | Az eseményindítók létrehozása vagy frissítése |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/updateSummary típusú/READ | Felsorolja vagy lekéri a frissítés összegzését |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/művelet | Tanúsítvány feltöltése az eszköz regisztrálásához |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Users/delete | Törli a felhasználók megosztása |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Users/operationResults/READ | A művelet eredményének megjelenítése vagy beolvasása |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Users/READ | A megosztási felhasználók listája vagy beolvasása |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Users/READ | A megosztási felhasználók listája vagy beolvasása |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Users/Write | A felhasználók megosztásának létrehozása vagy frissítése |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Write | A Data Box Edge-eszközök létrehozása vagy frissítése |
> | Műveletek | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Write | A Data Box Edge-eszközök létrehozása vagy frissítése |

## <a name="microsoftdatabricks"></a>Microsoft. Databricks

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Databricks/Locations/getNetworkPolicies/Action | Hálózati leképezési házirendek beolvasása az egyes alhálózatok számára az NRP által használt hely alapján |
> | Műveletek | Microsoft. Databricks/Locations/operationstatuses/READ | Reads the operation status for the resource. |
> | Műveletek | Microsoft. Databricks/Operations/READ | Gets the list of operations. |
> | Műveletek | Microsoft. Databricks/regisztráció/művelet | Regisztráljon a Databricks. |
> | Műveletek | Microsoft. Databricks/munkaterületek/törlés | Eltávolít egy Databricks-munkaterületet. |
> | Műveletek | Microsoft. Databricks/munkaterületek/szolgáltatók/Microsoft. bepillantást/diagnosticSettings/READ | A Databricks munkaterület elérhető diagnosztikai beállításainak megadása |
> | Műveletek | Microsoft. Databricks/munkaterületek/szolgáltatók/Microsoft. bepillantások/diagnosticSettings/írás | Diagnosztikai beállítások hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. Databricks/munkaterületek/szolgáltatók/Microsoft. bepillantást/logDefinitions/READ | A Databricks munkaterület elérhető naplózási definícióinak beolvasása |
> | Műveletek | Microsoft. Databricks/munkaterületek/olvasás | A Databricks-munkaterületek listájának beolvasása. |
> | Műveletek | Microsoft. Databricks/munkaterületek/refreshPermissions/művelet | Munkaterületre vonatkozó engedélyek frissítése |
> | Műveletek | Microsoft. Databricks/munkaterületek/updateDenyAssignment/művelet | Munkaterület felügyelt erőforráscsoport nem műveleteinek megtagadási hozzárendelésének frissítése |
> | Műveletek | Microsoft. Databricks/munkaterület/virtualNetworkPeerings/delete | Deletes a virtual network peering |
> | Műveletek | Microsoft. Databricks/munkaterületek/virtualNetworkPeerings/olvasás | A virtuális hálózat társításának beolvasása. |
> | Műveletek | Microsoft. Databricks/munkaterületek/virtualNetworkPeerings/írás | Virtuális hálózati társak hozzáadása vagy módosítása |
> | Műveletek | Microsoft. Databricks/munkaterületek/írás | Létrehoz egy Databricks-munkaterületet. |

## <a name="microsoftdatacatalog"></a>Microsoft. DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. DataCatalog/katalógusok/törlés | Data Catalog erőforrás-szolgáltatóhoz tartozó katalógusok erőforrásának törlése. |
> | Műveletek | Microsoft. DataCatalog/katalógusok/olvasás | Data Catalog erőforrás-szolgáltatóhoz tartozó katalógusok erőforrásának olvasása. |
> | Műveletek | Microsoft. DataCatalog/katalógusok/írás | Data Catalog erőforrás-szolgáltatónál írja be a katalógusok erőforrását. |
> | Műveletek | Microsoft. DataCatalog/checkNameAvailability/READ | A Data Catalog erőforrás-szolgáltatóhoz tartozó katalógus nevének rendelkezésre állásának keresése. |
> | Műveletek | Microsoft. DataCatalog/datacatalogs/delete | Data Catalog erőforrás-szolgáltatóhoz tartozó DataCatalog-erőforrás törlése. |
> | Műveletek | Microsoft. DataCatalog/datacatalogs/READ | Data Catalog erőforrás-szolgáltató DataCatalog-erőforrásának olvasása. |
> | Műveletek | Microsoft. DataCatalog/datacatalogs/Write | DataCatalog-erőforrás írása Data Catalog erőforrás-szolgáltatóhoz. |
> | Műveletek | Microsoft. DataCatalog/Operations/READ | A Data Catalog erőforrás-szolgáltató összes elérhető műveletének beolvasása. |
> | Műveletek | Microsoft. DataCatalog/regisztráció/művelet | Az előfizetés regisztrálása Data Catalog erőforrás-szolgáltatóhoz |
> | Műveletek | Microsoft. DataCatalog/regisztráció/művelet | Data Catalog erőforrás-szolgáltatóhoz tartozó előfizetés regisztrációjának törlése |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. DataFactory/checkazuredatafactorynameavailability/READ | Ellenőrzi, hogy a Data Factory neve elérhető-e a használatra. |
> | Műveletek | Microsoft. DataFactory/datafactories/activitywindows/READ | A Data Factory a megadott paraméterekkel olvassa be a tevékenységek ablakait. |
> | Műveletek | Microsoft. DataFactory/datafactories/datapipelines/tevékenységek/activitywindows/olvasás | Beolvassa a tevékenység ablakait a folyamat tevékenységéhez a megadott paraméterekkel. |
> | Műveletek | Microsoft. DataFactory/datafactories/datapipelines/activitywindows/READ | A megadott paraméterekkel olvassa be a folyamathoz tartozó Windows-tevékenységeket. |
> | Műveletek | Microsoft. DataFactory/datafactories/datapipelines/delete | Törli a folyamatokat. |
> | Műveletek | Microsoft. DataFactory/datafactories/datapipelines/szüneteltetés/művelet | Szünetelteti a folyamatokat. |
> | Műveletek | Microsoft. DataFactory/datafactories/datapipelines/READ | Bármely folyamat beolvasása. |
> | Műveletek | Microsoft. DataFactory/datafactories/datapipelines/Resume/művelet | Egy folyamat folytatása. |
> | Műveletek | Microsoft. DataFactory/datafactories/datapipelines/Update/Action | Frissíti a folyamatokat. |
> | Műveletek | Microsoft. DataFactory/datafactories/datapipelines/Write | Egy folyamat létrehozása vagy frissítése. |
> | Műveletek | Microsoft. DataFactory/datafactories/adatkészletek/activitywindows/olvasás | A megadott paraméterekkel beolvassa az adatkészlethez tartozó tevékenységek ablakait. |
> | Műveletek | Microsoft. DataFactory/datafactories/adatkészletek/törlés | Töröl minden adatkészletet. |
> | Műveletek | Microsoft. DataFactory/datafactories/adatkészletek/olvasás | Bármilyen adatkészlet beolvasása. |
> | Műveletek | Microsoft. DataFactory/datafactories/adatkészletek/sliceruns/olvasás | Az adott adathalmazhoz tartozó adatszelet futtatásának beolvasása a megadott kezdési időponttal. |
> | Műveletek | Microsoft. DataFactory/datafactories/adatkészletek/szeletek/olvasás | Az adatszeletek lekérése az adott időszakban. |
> | Műveletek | Microsoft. DataFactory/datafactories/adatkészletek/szeletek/írás | Az adatszelet állapotának frissítése |
> | Műveletek | Microsoft. DataFactory/datafactories/adatkészletek/írás | Létrehoz vagy frissít egy adatkészletet. |
> | Műveletek | Microsoft. DataFactory/datafactories/delete | Törli a Data Factory. |
> | Műveletek | Microsoft. DataFactory/datafactories/átjárók/ConnectionInfo/művelet | Az átjáróhoz tartozó kapcsolatok adatainak beolvasása. |
> | Műveletek | Microsoft. DataFactory/datafactories/átjárók/törlés | Bármely átjáró törlése. |
> | Műveletek | Microsoft. DataFactory/datafactories/átjárók/listauthkeys/művelet | Felsorolja az átjárók hitelesítési kulcsait. |
> | Műveletek | Microsoft. DataFactory/datafactories/átjárók/olvasás | Bármely átjáró beolvasása. |
> | Műveletek | Microsoft. DataFactory/datafactories/átjárók/regenerateauthkey/művelet | Újragenerálja a hitelesítési kulcsokat bármely átjáró számára. |
> | Műveletek | Microsoft. DataFactory/datafactories/átjárók/írás | Létrehoz vagy frissít egy átjárót. |
> | Műveletek | Microsoft. DataFactory/datafactories/linkedServices/delete | Törli a társított szolgáltatásokat. |
> | Műveletek | Microsoft. DataFactory/datafactories/linkedServices/READ | A társított szolgáltatás beolvasása. |
> | Műveletek | Microsoft. DataFactory/datafactories/linkedServices/Write | Létrehozza vagy frissíti a társított szolgáltatásokat. |
> | Műveletek | Microsoft. DataFactory/datafactories/READ | A Data Factory beolvasása. |
> | Műveletek | Microsoft. DataFactory/datafactories/Run/LogInfo/READ | Egy SAS URI-t olvas be a naplókat tartalmazó blob-tárolóba. |
> | Műveletek | Microsoft. DataFactory/datafactories/Tables/delete | Töröl minden adatkészletet. |
> | Műveletek | Microsoft. DataFactory/datafactories/Tables/READ | Bármilyen adatkészlet beolvasása. |
> | Műveletek | Microsoft. DataFactory/datafactories/Tables/Write | Létrehoz vagy frissít egy adatkészletet. |
> | Műveletek | Microsoft. DataFactory/datafactories/Write | Létrehozza vagy frissíti a Data Factory. |
> | Műveletek | Microsoft. DataFactory/gyárak/addDataFlowToDebugSession/művelet | Adatfolyamatok hozzáadása a hibakeresési munkamenethez az előzetes verzióhoz. |
> | Műveletek | Microsoft. DataFactory/gyárak/cancelpipelinerun/művelet | Megszakítja a futtatási azonosító által megadott folyamat futtatását. |
> | Műveletek | Microsoft. DataFactory/gyárak/cancelSandboxPipelineRun/művelet | A folyamat hibakeresési futtatásának megszakítása. |
> | Műveletek | Microsoft. DataFactory/gyárak/createdataflowdebugsession/művelet | Létrehoz egy adatfolyam-hibakeresési munkamenetet. |
> | Műveletek | Microsoft. DataFactory/gyárak/adatfolyamok/delete | Törli az adatfolyamatot. |
> | Műveletek | Microsoft. DataFactory/gyárak/adatfolyamok/READ | Beolvassa az adatfolyamot. |
> | Műveletek | Microsoft. DataFactory/gyárak/adatfolyamok/írás | Adatfolyam létrehozása vagy frissítése |
> | Műveletek | Microsoft. DataFactory/gyárak/adatkészletek/törlés | Töröl minden adatkészletet. |
> | Műveletek | Microsoft. DataFactory/gyárak/adatkészletek/olvasás | Bármilyen adatkészlet beolvasása. |
> | Műveletek | Microsoft. DataFactory/gyárak/adatkészletek/írás | Létrehoz vagy frissít egy adatkészletet. |
> | Műveletek | Microsoft. DataFactory/gyárak/debugpipelineruns/megszakítás/művelet | A folyamat hibakeresési futtatásának megszakítása. |
> | Műveletek | Microsoft. DataFactory/gyárak/törlés | Data Factory törlése. |
> | Műveletek | Microsoft. DataFactory/gyárak/deletedataflowdebugsession/művelet | Egy adatfolyam-hibakeresési munkamenet törlése. |
> | Műveletek | Microsoft. DataFactory/gyárak/executeDataFlowDebugCommand/művelet | Adatfolyam-hibakeresési parancs végrehajtása. |
> | Műveletek | Microsoft. DataFactory/gyárak/getDataPlaneAccess/művelet | Az ADF Adatsík szolgáltatás elérésének beolvasása. |
> | Műveletek | Microsoft. DataFactory/gyárak/getDataPlaneAccess/READ | Olvasási hozzáférés az ADF Adatsík szolgáltatáshoz. |
> | Műveletek | Microsoft. DataFactory/gyárak/getFeatureValue/művelet | A kitettség-vezérlési funkció értékének beolvasása az adott helyen. |
> | Műveletek | Microsoft. DataFactory/gyárak/getFeatureValue/READ | Beolvassa az expozíció-vezérlés funkció értékét az adott helyen. |
> | Műveletek | Microsoft. DataFactory/gyárak/getGitHubAccessToken/művelet | Lekéri a GitHub hozzáférési tokenjét. |
> | Műveletek | Microsoft. DataFactory/gyárak/integrationruntimes/delete | Töröl minden Integration Runtime. |
> | Műveletek | Microsoft. DataFactory/gyárak/integrationruntimes/getconnectioninfo/READ | A Integration Runtime a kapcsolatok adatainak beolvasása. |
> | Műveletek | Microsoft. DataFactory/gyárak/integrationruntimes/getObjectMetadata/művelet | A megadott Integration Runtime SSIS Integration Runtime metaadatok beolvasása. |
> | Műveletek | Microsoft. DataFactory/gyárak/integrationruntimes/getstatus/READ | Integration Runtime állapot beolvasása. |
> | Műveletek | Microsoft. DataFactory/gyárak/integrationruntimes/linkedIntegrationRuntime/művelet | Csatolt Integration Runtime-hivatkozás létrehozása a megadott megosztott Integration Runtime. |
> | Műveletek | Microsoft. DataFactory/gyárak/integrationruntimes/listauthkeys/READ | Felsorolja az egyes Integration Runtime hitelesítő kulcsait. |
> | Műveletek | Microsoft. DataFactory/gyárak/integrationruntimes/monitoringdata/READ | Beolvassa az egyes Integration Runtime figyelési szolgáltatásait. |
> | Műveletek | Microsoft. DataFactory/gyárak/integrationruntimes/csomópontok/törlés | A megadott Integration Runtime csomópontjának törlése. |
> | Műveletek | Microsoft. DataFactory/gyárak/integrationruntimes/csomópontok/IP-cím/művelet | A Integration Runtime megadott csomópontjának IP-címét adja vissza. |
> | Műveletek | Microsoft. DataFactory/gyárak/integrationruntimes/csomópontok/olvasás | A megadott Integration Runtime csomópontjának beolvasása. |
> | Műveletek | Microsoft. DataFactory/gyárak/integrationruntimes/csomópontok/írás | Frissíti a saját üzemeltetésű Integration Runtime csomópontot. |
> | Műveletek | Microsoft. DataFactory/gyárak/integrationruntimes/READ | Bármilyen Integration Runtime beolvasása. |
> | Műveletek | Microsoft. DataFactory/gyárak/integrationruntimes/refreshObjectMetadata/művelet | A megadott Integration Runtime SSIS frissítése Integration Runtime metaadatokat. |
> | Műveletek | Microsoft. DataFactory/gyárak/integrationruntimes/regenerateauthkey/művelet | Újragenerálja a megadott Integration Runtime hitelesítési kulcsait. |
> | Műveletek | Microsoft. DataFactory/gyárak/integrationruntimes/removelinks/művelet | Eltávolítja a megadott Integration Runtime csatolt Integration Runtime hivatkozásait. |
> | Műveletek | Microsoft. DataFactory/gyárak/integrationruntimes/Start/művelet | A Integration Runtime elindítása. |
> | Műveletek | Microsoft. DataFactory/gyárak/integrationruntimes/leállítás/művelet | Leállítja a Integration Runtime. |
> | Műveletek | Microsoft. DataFactory/gyárak/integrationruntimes/synccredentials/művelet | Szinkronizálja a megadott Integration Runtime hitelesítő adatait. |
> | Műveletek | Microsoft. DataFactory/gyárak/integrationruntimes/frissítés/művelet | A megadott Integration Runtime frissítése. |
> | Műveletek | Microsoft. DataFactory/gyárak/integrationruntimes/írás | Bármilyen Integration Runtime létrehozása vagy frissítése. |
> | Műveletek | Microsoft. DataFactory/gyárak/linkedServices/delete | A társított szolgáltatás törlése. |
> | Műveletek | Microsoft. DataFactory/gyárak/linkedServices/READ | A társított szolgáltatás beolvasása. |
> | Műveletek | Microsoft. DataFactory/gyárak/linkedServices/írás | Társított szolgáltatás létrehozása vagy frissítése |
> | Műveletek | Microsoft. DataFactory/gyárak/operationResults/READ | A művelet eredményeinek beolvasása. |
> | Műveletek | Microsoft. DataFactory/gyárak/pipelineruns/activityruns/READ | Beolvassa a megadott folyamat futtatási AZONOSÍTÓjának tevékenységeit. |
> | Műveletek | Microsoft. DataFactory/gyárak/pipelineruns/megszakítás/művelet | Megszakítja a futtatási azonosító által megadott folyamat futtatását. |
> | Műveletek | Microsoft. DataFactory/gyárak/pipelineruns/queryactivityruns/művelet | A megadott folyamat futtatási AZONOSÍTÓjának lekérdezése a tevékenység futásakor. |
> | Műveletek | Microsoft. DataFactory/gyárak/pipelineruns/queryactivityruns/READ | Beolvassa a lekérdezési tevékenység futásának eredményét a megadott folyamat futtatási AZONOSÍTÓJÁHOZ. |
> | Műveletek | Microsoft. DataFactory/gyárak/pipelineruns/READ | A folyamat futtatásának beolvasása. |
> | Műveletek | Microsoft. DataFactory/gyárak/folyamatok/createrun/művelet | Létrehoz egy futtatást a folyamathoz. |
> | Műveletek | Microsoft. DataFactory/gyárak/folyamatok/törlés | A folyamat törlése. |
> | Műveletek | Microsoft. DataFactory/gyárak/folyamatok/pipelineruns/activityruns/folyamat/olvasás | Lekéri a tevékenységek futtatásának folyamatát. |
> | Műveletek | Microsoft. DataFactory/gyárak/folyamatok/pipelineruns/olvasás | A folyamat futtatásának beolvasása. |
> | Műveletek | Microsoft. DataFactory/gyárak/folyamatok/olvasás | Folyamatban van a folyamat beolvasása. |
> | Műveletek | Microsoft. DataFactory/gyárak/folyamatok/homokozó/művelet | Hibakeresési futtatási környezetet hoz létre a folyamathoz. |
> | Műveletek | Microsoft. DataFactory/gyárak/folyamatok/homokozó/létrehozás/művelet | Hibakeresési futtatási környezetet hoz létre a folyamathoz. |
> | Műveletek | Microsoft. DataFactory/gyárak/folyamatok/homokozó/Futtatás/művelet | Hibakeresési futtatást hoz létre a folyamathoz. |
> | Műveletek | Microsoft. DataFactory/gyárak/folyamatok/írás | Folyamat létrehozása vagy frissítése |
> | Műveletek | Microsoft. DataFactory/gyárak/querydataflowdebugsessions/művelet | Egy adatfolyam-hibakeresési munkamenet lekérdezése. |
> | Műveletek | Microsoft. DataFactory/gyárak/querydebugpipelineruns/művelet | Lekérdezi a hibakeresési folyamat futtatását. |
> | Műveletek | Microsoft. DataFactory/gyárak/querypipelineruns/művelet | Lekérdezi a folyamat futtatását. |
> | Műveletek | Microsoft. DataFactory/gyárak/querypipelineruns/READ | Beolvassa a lekérdezési folyamat futtatásának eredményét. |
> | Műveletek | Microsoft. DataFactory/gyárak/querytriggerruns/művelet | Az trigger futtatását kérdezi le. |
> | Műveletek | Microsoft. DataFactory/gyárak/querytriggerruns/READ | Beolvassa az trigger futtatásának eredményét. |
> | Műveletek | Microsoft. DataFactory/gyárak/olvasás | Data Factory olvasása. |
> | Műveletek | Microsoft. DataFactory/gyárak/sandboxpipelineruns/művelet | Lekérdezi a hibakeresési folyamat futtatását. |
> | Műveletek | Microsoft. DataFactory/gyárak/sandboxpipelineruns/READ | A folyamat hibakeresési futtatási adatainak beolvasása. |
> | Műveletek | Microsoft. DataFactory/gyárak/sandboxpipelineruns/sandboxActivityRuns/READ | A tevékenység hibakeresési futtatási adatainak beolvasása. |
> | Műveletek | Microsoft. DataFactory/gyárak/startdataflowdebugsession/művelet | Elindít egy adatfolyam-hibakeresési munkamenetet. |
> | Műveletek | Microsoft. DataFactory/gyárak/triggerruns/READ | A trigger futtatásának beolvasása. |
> | Műveletek | Microsoft. DataFactory/gyárak/eseményindítók/törlés | Töröl minden triggert. |
> | Műveletek | Microsoft. DataFactory/gyárak/eseményindítók/geteventsubscriptionstatus/művelet | Esemény-előfizetés állapota. |
> | Műveletek | Microsoft. DataFactory/gyárak/eseményindítók/olvasás | Bármely trigger beolvasása. |
> | Műveletek | Microsoft. DataFactory/gyárak/eseményindítók/indítás/művelet | Elindítja az összes triggert. |
> | Műveletek | Microsoft. DataFactory/gyárak/eseményindítók/leállítás/művelet | Leállítja az összes triggert. |
> | Műveletek | Microsoft. DataFactory/gyárak/eseményindítók/subscribetoevents/művelet | Előfizetés az eseményekre. |
> | Műveletek | Microsoft. DataFactory/gyárak/triggerek/triggerruns/READ | A trigger futtatásának beolvasása. |
> | Műveletek | Microsoft. DataFactory/gyárak/eseményindítók/unsubscribefromevents/művelet | Leiratkozás az eseményekről. |
> | Műveletek | Microsoft. DataFactory/gyárak/triggerek/írás | Minden trigger létrehozása vagy frissítése. |
> | Műveletek | Microsoft. DataFactory/gyárak/írás | Data Factory létrehozása vagy frissítése |
> | Műveletek | Microsoft. DataFactory/Locations/configureFactoryRepo/Action | A gyári tárházat konfigurálja. |
> | Műveletek | Microsoft. DataFactory/Locations/getFeatureValue/Action | A kitettség-vezérlési funkció értékének beolvasása az adott helyen. |
> | Műveletek | Microsoft. DataFactory/Locations/getFeatureValue/READ | Beolvassa az expozíció-vezérlés funkció értékét az adott helyen. |
> | Műveletek | Microsoft. DataFactory/Operations/READ | A Microsoft Data Factory-szolgáltató összes műveletének beolvasása. |
> | Műveletek | Microsoft. DataFactory/regisztráció/művelet | Regisztrálja az előfizetést a Data Factory erőforrás-szolgáltatóhoz. |
> | Műveletek | Microsoft. DataFactory/regisztráció/művelet | A Data Factory erőforrás-szolgáltatóhoz tartozó előfizetés regisztrációjának törlése. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. DataLakeAnalytics/accounts/computePolicies/delete | Számítási szabályzat törlése. |
> | Műveletek | Microsoft. DataLakeAnalytics/fiókok/computePolicies/olvasás | A számítási szabályzattal kapcsolatos információk beolvasása. |
> | Műveletek | Microsoft. DataLakeAnalytics/fiókok/computePolicies/írás | Számítási szabályzat létrehozása vagy frissítése. |
> | Műveletek | Microsoft. DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | Data Lake Store-fiók DataLakeAnalytics-fiókból való leválasztása. |
> | Műveletek | Microsoft. DataLakeAnalytics/fiókok/dataLakeStoreAccounts/olvasás | Egy DataLakeAnalytics-fiók csatolt Data Lake Store-fiókjával kapcsolatos információk beolvasása. |
> | Műveletek | Microsoft. DataLakeAnalytics/fiókok/dataLakeStoreAccounts/írás | Hozzon létre vagy frissítsen egy DataLakeAnalytics-fiók csatolt Data Lake Store-fiókját. |
> | Műveletek | Microsoft. DataLakeAnalytics/fiókok/törlés | DataLakeAnalytics-fiók törlése. |
> | Műveletek | Microsoft. DataLakeAnalytics/accounts/firewallRules/delete | Tűzfalszabály törlése. |
> | Műveletek | Microsoft. DataLakeAnalytics/fiókok/firewallRules/olvasás | Tűzfalszabály adatainak beolvasása. |
> | Műveletek | Microsoft. DataLakeAnalytics/fiókok/firewallRules/írás | Tűzfalszabály létrehozása vagy frissítése. |
> | Műveletek | Microsoft. DataLakeAnalytics/fiókok/operationResults/olvasás | DataLakeAnalytics-fiók művelete eredményének beolvasása. |
> | Műveletek | Microsoft. DataLakeAnalytics/fiókok/olvasás | Egy meglévő DataLakeAnalytics-fiók adatainak beolvasása. |
> | Műveletek | Microsoft. DataLakeAnalytics/accounts/storageAccounts/containers/listSasTokens/Action | Egy DataLakeAnalytics-fiók csatolt Storage-fiókjának tároló-jogkivonatait listázza. |
> | Műveletek | Microsoft. DataLakeAnalytics/fiókok/storageAccounts/tárolók/olvasás | DataLakeAnalytics-fiókhoz tartozó társított Storage-fiók tárolóinak beolvasása. |
> | Műveletek | Microsoft. DataLakeAnalytics/accounts/storageAccounts/delete | Egy DataLakeAnalytics-fiókhoz tartozó Storage-fiók leválasztása. |
> | Műveletek | Microsoft. DataLakeAnalytics/fiókok/storageAccounts/olvasás | DataLakeAnalytics-fiókhoz tartozó társított Storage-fiók adatainak beolvasása. |
> | Műveletek | Microsoft. DataLakeAnalytics/fiókok/storageAccounts/írás | Hozzon létre vagy frissítsen egy DataLakeAnalytics-fiók csatolt Storage-fiókját. |
> | Műveletek | Microsoft. DataLakeAnalytics/accounts/TakeOwnership/Action | Engedélyek megadása a más felhasználók által küldött feladatok megszakításához. |
> | Műveletek | Microsoft. DataLakeAnalytics/accounts/transferAnalyticsUnits/Action | SystemMaxAnalyticsUnits átvitele DataLakeAnalytics-fiókok között. |
> | Műveletek | Microsoft. DataLakeAnalytics/accounts/virtualNetworkRules/delete | Virtuális hálózati szabály törlése. |
> | Műveletek | Microsoft. DataLakeAnalytics/fiókok/virtualNetworkRules/olvasás | Egy virtuális hálózati szabály adatainak beolvasása. |
> | Műveletek | Microsoft. DataLakeAnalytics/fiókok/virtualNetworkRules/írás | Virtuális hálózati szabály létrehozása vagy frissítése. |
> | Műveletek | Microsoft. DataLakeAnalytics/fiókok/írás | DataLakeAnalytics-fiók létrehozása vagy frissítése. |
> | Műveletek | Microsoft. DataLakeAnalytics/Locations/képesség/olvasás | A DataLakeAnalytics használatával kapcsolatos előfizetések funkcióinak beolvasása. |
> | Műveletek | Microsoft. DataLakeAnalytics/Locations/checkNameAvailability/Action | DataLakeAnalytics-fióknév rendelkezésre állásának keresése. |
> | Műveletek | Microsoft. DataLakeAnalytics/Locations/operationResults/READ | DataLakeAnalytics-fiók művelete eredményének beolvasása. |
> | Műveletek | Microsoft. DataLakeAnalytics/Locations/használati adatok/olvasás | Az előfizetéshez tartozó kvóta-használati információk beolvasása a DataLakeAnalytics használatával kapcsolatban. |
> | Műveletek | Microsoft. DataLakeAnalytics/Operations/READ | A DataLakeAnalytics elérhető műveleteinek beolvasása. |
> | Műveletek | Microsoft. DataLakeAnalytics/regisztráció/művelet | Regisztrálja az előfizetést a DataLakeAnalytics. |

## <a name="microsoftdatalakestore"></a>Microsoft. Data Lake Store

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Data Lake Store/fiókok/törlés | Data Lake Store-fiók törlése. |
> | Műveletek | Microsoft. Data Lake Store/accounts/enableKeyVault/Action | A kulcstartó engedélyezése egy Data Lake Store-fiókhoz. |
> | Műveletek | Microsoft. Data Lake Store/accounts/eventGridFilters/delete | EventGrid-szűrő törlése. |
> | Műveletek | Microsoft. Data Lake Store/fiókok/eventGridFilters/olvasás | EventGrid-szűrő beolvasása. |
> | Műveletek | Microsoft. Data Lake Store/fiókok/eventGridFilters/írás | EventGrid-szűrő létrehozása vagy frissítése. |
> | Műveletek | Microsoft. Data Lake Store/accounts/firewallRules/delete | Tűzfalszabály törlése. |
> | Műveletek | Microsoft. Data Lake Store/fiókok/firewallRules/olvasás | Tűzfalszabály adatainak beolvasása. |
> | Műveletek | Microsoft. Data Lake Store/fiókok/firewallRules/írás | Tűzfalszabály létrehozása vagy frissítése. |
> | Műveletek | Microsoft. Data Lake Store/fiókok/operationResults/olvasás | Data Lake Store-fiók művelete eredményének beolvasása. |
> | Műveletek | Microsoft. Data Lake Store/fiókok/olvasás | Egy meglévő Data Lake Store-fiók adatainak beolvasása. |
> | Műveletek | Microsoft. Data Lake Store/fiókok/megosztások/törlés | Megosztás törlése. |
> | Műveletek | Microsoft. Data Lake Store/fiókok/megosztások/olvasás | Megosztással kapcsolatos információk lekérése. |
> | Műveletek | Microsoft. Data Lake Store/fiókok/megosztások/írás | Hozzon létre vagy frissítsen egy megosztást. |
> | Műveletek | Microsoft.DataLakeStore/accounts/Superuser/action | Adja meg a rendszeradminisztrátort Data Lake Store ha a Microsoft. Authorization/roleAssignments/Write. |
> | Műveletek | Microsoft. Data Lake Store/accounts/trustedIdProviders/delete | Megbízható identitás szolgáltatójának törlése. |
> | Műveletek | Microsoft. Data Lake Store/fiókok/trustedIdProviders/olvasás | Információ kérése egy megbízható identitás-szolgáltatóról. |
> | Műveletek | Microsoft. Data Lake Store/fiókok/trustedIdProviders/írás | Hozzon létre vagy frissítsen egy megbízható identitás-szolgáltatót. |
> | Műveletek | Microsoft. Data Lake Store/accounts/virtualNetworkRules/delete | Virtuális hálózati szabály törlése. |
> | Műveletek | Microsoft. Data Lake Store/fiókok/virtualNetworkRules/olvasás | Egy virtuális hálózati szabály adatainak beolvasása. |
> | Műveletek | Microsoft. Data Lake Store/fiókok/virtualNetworkRules/írás | Virtuális hálózati szabály létrehozása vagy frissítése. |
> | Műveletek | Microsoft. Data Lake Store/fiókok/írás | Data Lake Store-fiók létrehozása vagy frissítése. |
> | Műveletek | Microsoft. Data Lake Store/Locations/képesség/olvasás | A Data Lake Store használatával kapcsolatos előfizetések funkcióinak beolvasása. |
> | Műveletek | Microsoft. Data Lake Store/Locations/checkNameAvailability/Action | Data Lake Store-fióknév rendelkezésre állásának keresése. |
> | Műveletek | Microsoft. Data Lake Store/Locations/operationResults/READ | Data Lake Store-fiók művelete eredményének beolvasása. |
> | Műveletek | Microsoft. Data Lake Store/Locations/használati adatok/olvasás | Az előfizetéshez tartozó kvóta-használati információk beolvasása a Data Lake Store használatával kapcsolatban. |
> | Műveletek | Microsoft. Data Lake Store/Operations/READ | A Data Lake Store elérhető műveleteinek beolvasása. |
> | Műveletek | Microsoft. Data Lake Store/regisztráció/művelet | Regisztrálja az előfizetést a Data Lake Store. |

## <a name="microsoftdatamigration"></a>Microsoft. DataMigration

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. DataMigration/Locations/operationResults/READ | Egy 202 elfogadott válaszhoz kapcsolódó hosszan futó művelet állapotának beolvasása |
> | Műveletek | Microsoft. DataMigration/Locations/operationStatuses/READ | Egy 202 elfogadott válaszhoz kapcsolódó hosszan futó művelet állapotának beolvasása |
> | Műveletek | Microsoft. DataMigration/regisztráció/művelet | Az előfizetés regisztrálása a Azure Database Migration Service szolgáltatóval |
> | Műveletek | Microsoft. DataMigration/szolgáltatások/addWorker/művelet | A DMS-feldolgozót hozzáadja a szolgáltatás elérhető munkamenet-feldolgozói számára |
> | Műveletek | Microsoft. DataMigration/szolgáltatások/checkStatus/művelet | Annak megállapítása, hogy a szolgáltatás telepítve van-e és fut-e |
> | Műveletek | Microsoft. DataMigration/szolgáltatások/configureWorker/művelet | Egy DMS-feldolgozót konfigurál a szolgáltatás elérhető munkamenet-feldolgozói számára |
> | Műveletek | Microsoft. DataMigration/Services/delete | Töröl egy erőforrást és annak összes gyermekét |
> | Műveletek | Microsoft. DataMigration/Services/projects/accessArtifacts/Action | Létrehozhat egy URL-címet, amely a projekt összetevőinek beszerzéséhez vagy elvégzéséhez használható. |
> | Műveletek | Microsoft. DataMigration/szolgáltatások/projektek/törlés | Töröl egy erőforrást és annak összes gyermekét |
> | Műveletek | Microsoft. DataMigration/Services/projects/Files/delete | Töröl egy erőforrást és annak összes gyermekét |
> | Műveletek | Microsoft. DataMigration/szolgáltatások/projektek/fájlok/olvasás | Az erőforrásokkal kapcsolatos információk beolvasása |
> | Műveletek | Microsoft. DataMigration/szolgáltatások/projektek/fájlok/olvasás/művelet | A fájl tartalmának olvasásához használható URL-cím beszerzése |
> | Műveletek | Microsoft. DataMigration/Services/projects/Files/readWrite/Action | A fájl tartalmának olvasásához vagy írásához használható URL-cím beszerzése |
> | Műveletek | Microsoft. DataMigration/szolgáltatások/projektek/fájlok/írás | Erőforrások és tulajdonságaik létrehozása vagy frissítése |
> | Műveletek | Microsoft. DataMigration/szolgáltatások/projektek/olvasás | Az erőforrásokkal kapcsolatos információk beolvasása |
> | Műveletek | Microsoft. DataMigration/szolgáltatások/projektek/feladatok/megszakítás/művelet | A feladat megszakítása, ha az éppen fut |
> | Műveletek | Microsoft. DataMigration/szolgáltatások/projektek/feladatok/törlés | Töröl egy erőforrást és annak összes gyermekét |
> | Műveletek | Microsoft. DataMigration/szolgáltatások/projektek/feladatok/olvasás | Az erőforrásokkal kapcsolatos információk beolvasása |
> | Műveletek | Microsoft. DataMigration/szolgáltatások/projektek/feladatok/írás | Feladatok futtatása Azure Database Migration Service feladatokkal |
> | Műveletek | Microsoft. DataMigration/szolgáltatások/projektek/írás | Feladatok futtatása Azure Database Migration Service feladatokkal |
> | Műveletek | Microsoft. DataMigration/szolgáltatások/olvasás | Az erőforrásokkal kapcsolatos információk beolvasása |
> | Műveletek | Microsoft. DataMigration/szolgáltatások/removeWorker/művelet | DMS-feldolgozó eltávolítása a szolgáltatás elérhető munkamenet-feldolgozói számára |
> | Műveletek | Microsoft. DataMigration/szolgáltatások/serviceTasks/megszakítás/művelet | A feladat megszakítása, ha az éppen fut |
> | Műveletek | Microsoft. DataMigration/Services/serviceTasks/delete | Töröl egy erőforrást és annak összes gyermekét |
> | Műveletek | Microsoft. DataMigration/Services/serviceTasks/READ | Az erőforrásokkal kapcsolatos információk beolvasása |
> | Műveletek | Microsoft. DataMigration/szolgáltatások/serviceTasks/írás | Feladatok futtatása Azure Database Migration Service feladatokkal |
> | Műveletek | Microsoft. DataMigration/szolgáltatások/tárolóhelyek/törlés | Töröl egy erőforrást és annak összes gyermekét |
> | Műveletek | Microsoft. DataMigration/szolgáltatások/bővítőhely/olvasás | Az erőforrásokkal kapcsolatos információk beolvasása |
> | Műveletek | Microsoft. DataMigration/szolgáltatások/bővítőhely/írás | Erőforrások és tulajdonságaik létrehozása vagy frissítése |
> | Műveletek | Microsoft. DataMigration/szolgáltatások/indítás/művelet | A DMS szolgáltatás elindítása, amely lehetővé teszi, hogy újra feldolgozza az áttelepítést |
> | Műveletek | Microsoft. DataMigration/szolgáltatások/leállítás/művelet | A DMS szolgáltatás leállításával csökkentheti a költségeit |
> | Műveletek | Microsoft. DataMigration/szolgáltatások/updateAgentConfig/művelet | Frissíti a DMS-ügynök konfigurációját a megadott értékekkel. |
> | Műveletek | Microsoft. DataMigration/szolgáltatások/írás | Erőforrások és tulajdonságaik létrehozása vagy frissítése |
> | Műveletek | Microsoft. DataMigration/SKU/READ | A DMS-erőforrások által támogatott SKU-ket tartalmazó lista beolvasása. |

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. DBforMariaDB/checkNameAvailability/művelet | Verify whether given server name is available for provisioning worldwide for a given subscription. |
> | Műveletek | Microsoft. DBforMariaDB/Locations/azureAsyncOperation/READ | MariaDB-kiszolgáló műveleti eredményeinek visszaadása |
> | Műveletek | Microsoft. DBforMariaDB/Locations/operationResults/READ | ResourceGroup-alapú MariaDB-kiszolgáló műveleti eredményeinek visszaadása |
> | Műveletek | Microsoft. DBforMariaDB/Locations/operationResults/READ | MariaDB-kiszolgáló műveleti eredményeinek visszaadása |
> | Műveletek | Microsoft. DBforMariaDB/Locations/performanceTiers/READ | A rendelkezésre álló teljesítményi rétegek listáját adja vissza. |
> | Műveletek | Microsoft. DBforMariaDB/Locations/privateEndpointConnectionAzureAsyncOperation/READ | Gets the result for a private endpoint connection operation |
> | Műveletek | Microsoft. DBforMariaDB/Locations/privateEndpointConnectionOperationResults/READ | Gets the result for a private endpoint connection operation |
> | Műveletek | Microsoft. DBforMariaDB/Locations/privateEndpointConnectionProxyAzureAsyncOperation/READ | Gets the result for a private endpoint connection proxy operation |
> | Műveletek | Microsoft. DBforMariaDB/Locations/privateEndpointConnectionProxyOperationResults/READ | Gets the result for a private endpoint connection proxy operation |
> | Műveletek | Microsoft. DBforMariaDB/Locations/securityAlertPoliciesAzureAsyncOperation/READ | A kiszolgáló fenyegetés-észlelési műveletének eredményét visszaadó lista. |
> | Műveletek | Microsoft. DBforMariaDB/Locations/securityAlertPoliciesOperationResults/READ | A kiszolgáló fenyegetés-észlelési műveletének eredményét visszaadó lista. |
> | Műveletek | Microsoft. DBforMariaDB/Locations/serverKeyAzureAsyncOperation/READ | Gets in-progress operations on transparent data encryption server keys |
> | Műveletek | Microsoft. DBforMariaDB/Locations/serverKeyOperationResults/READ | Gets in-progress operations on transparent data encryption server keys |
> | Műveletek | Microsoft. DBforMariaDB/Operations/READ | A MariaDB-műveletek listájának visszaadása. |
> | Műveletek | Microsoft. DBforMariaDB/performanceTiers/READ | A rendelkezésre álló teljesítményi rétegek listáját adja vissza. |
> | Műveletek | Microsoft. DBforMariaDB/regisztráció/művelet | MariaDB erőforrás-szolgáltató regisztrálása |
> | Műveletek | Microsoft. DBforMariaDB/kiszolgálók/rendszergazdák/törlés | Törli a MariaDB-kiszolgáló egy meglévő rendszergazdáját. |
> | Műveletek | Microsoft. DBforMariaDB/kiszolgálók/rendszergazdák/olvasás | Lekéri a MariaDB-kiszolgáló rendszergazdáinak listáját. |
> | Műveletek | Microsoft. DBforMariaDB/kiszolgálók/rendszergazdák/írás | Létrehozza vagy frissíti a MariaDB-kiszolgáló rendszergazdáját a megadott paraméterekkel. |
> | Műveletek | Microsoft. DBforMariaDB/kiszolgálók/tanácsadók/createRecommendedActionSession/művelet | Új javaslat műveleti munkamenet létrehozása |
> | Műveletek | Microsoft. DBforMariaDB/kiszolgálók/tanácsadók/olvasás | A tanácsadók listájának visszaadása |
> | Műveletek | Microsoft. DBforMariaDB/kiszolgálók/tanácsadók/olvasás | Advisor visszaküldése |
> | Műveletek | Microsoft. DBforMariaDB/kiszolgálók/tanácsadók/recommendedActions/READ | A javasolt műveletek listájának visszaadása |
> | Műveletek | Microsoft. DBforMariaDB/kiszolgálók/tanácsadók/recommendedActions/READ | A javasolt műveletek listájának visszaadása |
> | Műveletek | Microsoft. DBforMariaDB/kiszolgálók/tanácsadók/recommendedActions/READ | Javasolt művelet visszaadása |
> | Műveletek | Microsoft. DBforMariaDB/kiszolgálók/konfigurációk/olvasás | Visszaadja egy kiszolgáló konfigurációinak listáját, vagy lekéri a megadott konfiguráció tulajdonságait. |
> | Műveletek | Microsoft. DBforMariaDB/kiszolgálók/konfigurációk/írás | A megadott konfiguráció értékének frissítése |
> | Műveletek | Microsoft. DBforMariaDB/kiszolgálók/adatbázisok/törlés | Töröl egy meglévő MariaDB-adatbázist. |
> | Műveletek | Microsoft. DBforMariaDB/kiszolgálók/adatbázisok/olvasás | Visszaadja a MariaDB-adatbázisok listáját, vagy lekéri a megadott adatbázis tulajdonságait. |
> | Műveletek | Microsoft. DBforMariaDB/kiszolgálók/adatbázisok/írás | Létrehoz egy MariaDB-adatbázist a megadott paraméterekkel, vagy frissíti a megadott adatbázis tulajdonságait. |
> | Műveletek | Microsoft. DBforMariaDB/kiszolgálók/törlés | Töröl egy meglévő kiszolgálót. |
> | Műveletek | Microsoft. DBforMariaDB/Servers/firewallRules/delete | Töröl egy meglévő tűzfalszabály-szabályt. |
> | Műveletek | Microsoft. DBforMariaDB/Servers/firewallRules/READ | Visszaadja egy kiszolgáló tűzfalszabályok listáját, vagy lekéri a megadott tűzfalszabály tulajdonságait. |
> | Műveletek | Microsoft. DBforMariaDB/Servers/firewallRules/Write | Tűzfalszabály létrehozása a megadott paraméterekkel vagy egy meglévő szabály frissítése. |
> | Műveletek | Microsoft. DBforMariaDB/kiszolgálók/kulcsok/törlés | Deletes an existing server key. |
> | Műveletek | Microsoft. DBforMariaDB/kiszolgálók/kulcsok/olvasás | Return the list of server keys or gets the properties for the specified server key. |
> | Műveletek | Microsoft. DBforMariaDB/kiszolgálók/kulcsok/írás | Creates a key with the specified parameters or update the properties or tags for the specified server key. |
> | Műveletek | Microsoft. DBforMariaDB/kiszolgálók/naplófájlok/olvasás | A MariaDB-naplófájlok listájának visszaadása. |
> | Műveletek | Microsoft. DBforMariaDB/Servers/privateEndpointConnectionProxies/delete | Deletes an existing private endpoint connection proxy |
> | Műveletek | Microsoft. DBforMariaDB/Servers/privateEndpointConnectionProxies/READ | Returns the list of private endpoint connection proxies or gets the properties for the specified private endpoint connection proxy. |
> | Műveletek | Microsoft. DBforMariaDB/Servers/privateEndpointConnectionProxies/validate/Action | Validates a private endpoint connection create call from NRP side |
> | Műveletek | Microsoft. DBforMariaDB/Servers/privateEndpointConnectionProxies/Write | Creates a private endpoint connection proxy with the specified parameters or updates the properties or tags for the specified private endpoint connection proxy. |
> | Műveletek | Microsoft. DBforMariaDB/Servers/privateEndpointConnections/delete | Deletes an existing private endpoint connection |
> | Műveletek | Microsoft. DBforMariaDB/Servers/privateEndpointConnections/READ | Returns the list of private endpoint connections or gets the properties for the specified private endpoint connection. |
> | Műveletek | Microsoft. DBforMariaDB/Servers/privateEndpointConnections/Write | Approves or rejects an existing private endpoint connection |
> | Műveletek | Microsoft. DBforMariaDB/Servers/privateLinkResources/READ | A kapcsolódó MariaDB-kiszolgáló magánhálózati kapcsolati erőforrásainak beolvasása |
> | Műveletek | Microsoft. DBforMariaDB/Servers/Providers/Microsoft. bepillantást/diagnosticSettings/READ | Az erőforráshoz tartozó nem független beállítás beolvasása |
> | Műveletek | Microsoft. DBforMariaDB/kiszolgálók/szolgáltatók/Microsoft. bepillantások/diagnosticSettings/írás | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft. DBforMariaDB/Servers/Providers/Microsoft. bepillantást/logDefinitions/READ | A MariaDB-kiszolgálók számára elérhető naplók beolvasása |
> | Műveletek | Microsoft. DBforMariaDB/Servers/Providers/Microsoft. bepillantást/metricDefinitions/READ | Az adatbázisokhoz elérhető metrikák visszatérési típusai |
> | Műveletek | Microsoft. DBforMariaDB/Servers/queryTexts/Action | Return the texts for a list of queries |
> | Műveletek | Microsoft. DBforMariaDB/Servers/queryTexts/Action | Return the text of a query |
> | Műveletek | Microsoft. DBforMariaDB/kiszolgálók/olvasás | Return the list of servers or gets the properties for the specified server. |
> | Műveletek | Microsoft. DBforMariaDB/Servers/recoverableServers/READ | A helyreállítható MariaDB-kiszolgáló adatainak visszaadása |
> | Műveletek | Microsoft. DBforMariaDB/kiszolgálók/replikák/olvasás | MariaDB-kiszolgáló olvasási replikáinak beolvasása |
> | Műveletek | Microsoft. DBforMariaDB/kiszolgálók/újraindítás/művelet | Restarts a specific server. |
> | Műveletek | Microsoft. DBforMariaDB/Servers/securityAlertPolicies/READ | Retrieve details of the server threat detection policy configured on a given server |
> | Műveletek | Microsoft. DBforMariaDB/Servers/securityAlertPolicies/Write | A kiszolgálói veszélyforrások észlelési házirendjének módosítása egy adott kiszolgálóhoz |
> | Műveletek | Microsoft. DBforMariaDB/Servers/topQueryStatistics/READ | Return the list of Query Statistics for the top queries. |
> | Műveletek | Microsoft. DBforMariaDB/Servers/topQueryStatistics/READ | Lekérdezési statisztika visszaadása |
> | Műveletek | Microsoft. DBforMariaDB/Servers/updateConfigurations/Action | A megadott kiszolgálóhoz tartozó konfigurációk frissítése |
> | Műveletek | Microsoft. DBforMariaDB/Servers/virtualNetworkRules/delete | Meglévő Virtual Network-szabály törlése |
> | Műveletek | Microsoft. DBforMariaDB/Servers/virtualNetworkRules/READ | Visszaadja a virtuális hálózati szabályok listáját, vagy lekéri a megadott virtuális hálózati szabály tulajdonságait. |
> | Műveletek | Microsoft. DBforMariaDB/Servers/virtualNetworkRules/Write | Létrehoz egy virtuális hálózati szabályt a megadott paraméterekkel, vagy frissíti a megadott virtuális hálózati szabály tulajdonságait vagy címkéit. |
> | Műveletek | Microsoft. DBforMariaDB/Servers/waitStatistics/READ | Return wait statistics for an instance |
> | Műveletek | Microsoft. DBforMariaDB/Servers/waitStatistics/READ | Várakozási statisztika visszaküldése |
> | Műveletek | Microsoft. DBforMariaDB/kiszolgálók/írás | Létrehoz egy kiszolgálót a megadott paraméterekkel, vagy frissíti a megadott kiszolgálóhoz tartozó tulajdonságokat vagy címkéket. |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. DBforMySQL/checkNameAvailability/művelet | Verify whether given server name is available for provisioning worldwide for a given subscription. |
> | Műveletek | Microsoft. DBforMySQL/Locations/azureAsyncOperation/READ | A MySQL-kiszolgáló műveleti eredményeinek visszaadása |
> | Műveletek | Microsoft. DBforMySQL/Locations/operationResults/READ | ResourceGroup-alapú MySQL-kiszolgáló műveleti eredményeinek visszaadása |
> | Műveletek | Microsoft. DBforMySQL/Locations/operationResults/READ | A MySQL-kiszolgáló műveleti eredményeinek visszaadása |
> | Műveletek | Microsoft. DBforMySQL/Locations/performanceTiers/READ | A rendelkezésre álló teljesítményi rétegek listáját adja vissza. |
> | Műveletek | Microsoft. DBforMySQL/Locations/privateEndpointConnectionAzureAsyncOperation/READ | Gets the result for a private endpoint connection operation |
> | Műveletek | Microsoft. DBforMySQL/Locations/privateEndpointConnectionOperationResults/READ | Gets the result for a private endpoint connection operation |
> | Műveletek | Microsoft. DBforMySQL/Locations/privateEndpointConnectionProxyAzureAsyncOperation/READ | Gets the result for a private endpoint connection proxy operation |
> | Műveletek | Microsoft. DBforMySQL/Locations/privateEndpointConnectionProxyOperationResults/READ | Gets the result for a private endpoint connection proxy operation |
> | Műveletek | Microsoft. DBforMySQL/Locations/securityAlertPoliciesAzureAsyncOperation/READ | A kiszolgáló fenyegetés-észlelési műveletének eredményét visszaadó lista. |
> | Műveletek | Microsoft. DBforMySQL/Locations/securityAlertPoliciesOperationResults/READ | A kiszolgáló fenyegetés-észlelési műveletének eredményét visszaadó lista. |
> | Műveletek | Microsoft. DBforMySQL/Locations/serverKeyAzureAsyncOperation/READ | Gets in-progress operations on transparent data encryption server keys |
> | Műveletek | Microsoft. DBforMySQL/Locations/serverKeyOperationResults/READ | Gets in-progress operations on transparent data encryption server keys |
> | Műveletek | Microsoft. DBforMySQL/Operations/READ | A MySQL-műveletek listájának visszaadása. |
> | Műveletek | Microsoft. DBforMySQL/performanceTiers/READ | A rendelkezésre álló teljesítményi rétegek listáját adja vissza. |
> | Műveletek | Microsoft. DBforMySQL/regisztráció/művelet | MySQL erőforrás-szolgáltató regisztrálása |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/rendszergazdák/törlés | A MySQL-kiszolgáló meglévő rendszergazdájának törlése. |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/rendszergazdák/olvasás | Lekéri a MySQL-kiszolgáló rendszergazdáinak listáját. |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/rendszergazdák/írás | Létrehozza vagy frissíti a MySQL-kiszolgáló rendszergazdáját a megadott paraméterekkel. |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/tanácsadók/createRecommendedActionSession/művelet | Új javaslat műveleti munkamenet létrehozása |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/tanácsadók/olvasás | A tanácsadók listájának visszaadása |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/tanácsadók/olvasás | Advisor visszaküldése |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/tanácsadók/recommendedActions/READ | A javasolt műveletek listájának visszaadása |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/tanácsadók/recommendedActions/READ | A javasolt műveletek listájának visszaadása |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/tanácsadók/recommendedActions/READ | Javasolt művelet visszaadása |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/konfigurációk/olvasás | Visszaadja egy kiszolgáló konfigurációinak listáját, vagy lekéri a megadott konfiguráció tulajdonságait. |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/konfigurációk/írás | A megadott konfiguráció értékének frissítése |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/adatbázisok/törlés | Töröl egy meglévő MySQL-adatbázist. |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/adatbázisok/olvasás | A MySQL-adatbázisok listájának visszaadása vagy a megadott adatbázis tulajdonságainak beolvasása. |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/adatbázisok/írás | Egy MySQL-adatbázist hoz létre a megadott paraméterekkel, vagy frissíti a megadott adatbázis tulajdonságait. |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/törlés | Töröl egy meglévő kiszolgálót. |
> | Műveletek | Microsoft. DBforMySQL/Servers/firewallRules/delete | Töröl egy meglévő tűzfalszabály-szabályt. |
> | Műveletek | Microsoft. DBforMySQL/Servers/firewallRules/READ | Visszaadja egy kiszolgáló tűzfalszabályok listáját, vagy lekéri a megadott tűzfalszabály tulajdonságait. |
> | Műveletek | Microsoft. DBforMySQL/Servers/firewallRules/Write | Tűzfalszabály létrehozása a megadott paraméterekkel vagy egy meglévő szabály frissítése. |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/kulcsok/törlés | Deletes an existing server key. |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/kulcsok/olvasás | Return the list of server keys or gets the properties for the specified server key. |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/kulcsok/írás | Creates a key with the specified parameters or update the properties or tags for the specified server key. |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/naplófájlok/olvasás | A MySQL-naplófájlok listájának visszaadása. |
> | Műveletek | Microsoft. DBforMySQL/Servers/privateEndpointConnectionProxies/delete | Deletes an existing private endpoint connection proxy |
> | Műveletek | Microsoft. DBforMySQL/Servers/privateEndpointConnectionProxies/READ | Returns the list of private endpoint connection proxies or gets the properties for the specified private endpoint connection proxy. |
> | Műveletek | Microsoft. DBforMySQL/Servers/privateEndpointConnectionProxies/validate/Action | Validates a private endpoint connection create call from NRP side |
> | Műveletek | Microsoft. DBforMySQL/Servers/privateEndpointConnectionProxies/Write | Creates a private endpoint connection proxy with the specified parameters or updates the properties or tags for the specified private endpoint connection proxy. |
> | Műveletek | Microsoft. DBforMySQL/Servers/privateEndpointConnections/delete | Deletes an existing private endpoint connection |
> | Műveletek | Microsoft. DBforMySQL/Servers/privateEndpointConnections/READ | Returns the list of private endpoint connections or gets the properties for the specified private endpoint connection. |
> | Műveletek | Microsoft. DBforMySQL/Servers/privateEndpointConnections/Write | Approves or rejects an existing private endpoint connection |
> | Műveletek | Microsoft. DBforMySQL/Servers/privateLinkResources/READ | A kapcsolódó MySQL-kiszolgáló magánhálózati kapcsolati erőforrásainak beolvasása |
> | Műveletek | Microsoft. DBforMySQL/Servers/Providers/Microsoft. bepillantást/diagnosticSettings/READ | Az erőforráshoz tartozó nem független beállítás beolvasása |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/szolgáltatók/Microsoft. bepillantások/diagnosticSettings/írás | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft. DBforMySQL/Servers/Providers/Microsoft. bepillantást/logDefinitions/READ | A MySQL-kiszolgálókhoz elérhető naplók beolvasása |
> | Műveletek | Microsoft. DBforMySQL/Servers/Providers/Microsoft. bepillantást/metricDefinitions/READ | Az adatbázisokhoz elérhető metrikák visszatérési típusai |
> | Műveletek | Microsoft. DBforMySQL/Servers/queryTexts/Action | Return the texts for a list of queries |
> | Műveletek | Microsoft. DBforMySQL/Servers/queryTexts/Action | Return the text of a query |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/olvasás | Return the list of servers or gets the properties for the specified server. |
> | Műveletek | Microsoft. DBforMySQL/Servers/recoverableServers/READ | A helyreállítható MySQL-kiszolgáló adatainak visszaadása |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/replikák/olvasás | MySQL-kiszolgáló olvasási replikáinak beolvasása |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/újraindítás/művelet | Restarts a specific server. |
> | Műveletek | Microsoft. DBforMySQL/Servers/securityAlertPolicies/READ | Retrieve details of the server threat detection policy configured on a given server |
> | Műveletek | Microsoft. DBforMySQL/Servers/securityAlertPolicies/Write | A kiszolgálói veszélyforrások észlelési házirendjének módosítása egy adott kiszolgálóhoz |
> | Műveletek | Microsoft. DBforMySQL/Servers/topQueryStatistics/READ | Return the list of Query Statistics for the top queries. |
> | Műveletek | Microsoft. DBforMySQL/Servers/topQueryStatistics/READ | Lekérdezési statisztika visszaadása |
> | Műveletek | Microsoft. DBforMySQL/Servers/updateConfigurations/Action | A megadott kiszolgálóhoz tartozó konfigurációk frissítése |
> | Műveletek | Microsoft. DBforMySQL/Servers/virtualNetworkRules/delete | Meglévő Virtual Network-szabály törlése |
> | Műveletek | Microsoft. DBforMySQL/Servers/virtualNetworkRules/READ | Visszaadja a virtuális hálózati szabályok listáját, vagy lekéri a megadott virtuális hálózati szabály tulajdonságait. |
> | Műveletek | Microsoft. DBforMySQL/Servers/virtualNetworkRules/Write | Létrehoz egy virtuális hálózati szabályt a megadott paraméterekkel, vagy frissíti a megadott virtuális hálózati szabály tulajdonságait vagy címkéit. |
> | Műveletek | Microsoft. DBforMySQL/Servers/waitStatistics/READ | Return wait statistics for an instance |
> | Műveletek | Microsoft. DBforMySQL/Servers/waitStatistics/READ | Várakozási statisztika visszaküldése |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/írás | Létrehoz egy kiszolgálót a megadott paraméterekkel, vagy frissíti a megadott kiszolgálóhoz tartozó tulajdonságokat vagy címkéket. |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. DBforPostgreSQL/checkNameAvailability/művelet | Verify whether given server name is available for provisioning worldwide for a given subscription. |
> | Műveletek | Microsoft. DBforPostgreSQL/Locations/azureAsyncOperation/READ | A PostgreSQL-kiszolgáló műveleti eredményeinek visszaadása |
> | Műveletek | Microsoft. DBforPostgreSQL/Locations/operationResults/READ | ResourceGroup-alapú PostgreSQL-kiszolgáló műveleti eredményeinek visszaadása |
> | Műveletek | Microsoft. DBforPostgreSQL/Locations/operationResults/READ | A PostgreSQL-kiszolgáló műveleti eredményeinek visszaadása |
> | Műveletek | Microsoft. DBforPostgreSQL/Locations/performanceTiers/READ | A rendelkezésre álló teljesítményi rétegek listáját adja vissza. |
> | Műveletek | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionAzureAsyncOperation/READ | Gets the result for a private endpoint connection operation |
> | Műveletek | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionOperationResults/READ | Gets the result for a private endpoint connection operation |
> | Műveletek | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionProxyAzureAsyncOperation/READ | Gets the result for a private endpoint connection proxy operation |
> | Műveletek | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionProxyOperationResults/READ | Gets the result for a private endpoint connection proxy operation |
> | Műveletek | Microsoft. DBforPostgreSQL/Locations/securityAlertPoliciesAzureAsyncOperation/READ | A kiszolgáló fenyegetés-észlelési műveletének eredményét visszaadó lista. |
> | Műveletek | Microsoft. DBforPostgreSQL/Locations/securityAlertPoliciesOperationResults/READ | A kiszolgáló fenyegetés-észlelési műveletének eredményét visszaadó lista. |
> | Műveletek | Microsoft. DBforPostgreSQL/Locations/serverKeyAzureAsyncOperation/READ | Gets in-progress operations on transparent data encryption server keys |
> | Műveletek | Microsoft. DBforPostgreSQL/Locations/serverKeyOperationResults/READ | Gets in-progress operations on transparent data encryption server keys |
> | Műveletek | Microsoft. DBforPostgreSQL/Operations/READ | A PostgreSQL-műveletek listájának visszaadása. |
> | Műveletek | Microsoft. DBforPostgreSQL/performanceTiers/READ | A rendelkezésre álló teljesítményi rétegek listáját adja vissza. |
> | Műveletek | Microsoft. DBforPostgreSQL/regisztráció/művelet | A PostgreSQL erőforrás-szolgáltató regisztrálása |
> | Műveletek | Microsoft. DBforPostgreSQL/kiszolgálók/rendszergazdák/törlés | A PostgreSQL-kiszolgáló meglévő rendszergazdájának törlése. |
> | Műveletek | Microsoft. DBforPostgreSQL/kiszolgálók/rendszergazdák/olvasás | Lekéri a PostgreSQL-kiszolgáló rendszergazdáinak listáját. |
> | Műveletek | Microsoft. DBforPostgreSQL/kiszolgálók/rendszergazdák/írás | Létrehozza vagy frissíti a PostgreSQL-kiszolgáló rendszergazdáját a megadott paraméterekkel. |
> | Műveletek | Microsoft. DBforPostgreSQL/kiszolgálók/tanácsadók/olvasás | A tanácsadók listájának visszaadása |
> | Műveletek | Microsoft. DBforPostgreSQL/kiszolgálók/tanácsadók/recommendedActions/READ | A javasolt műveletek listájának visszaadása |
> | Műveletek | Microsoft. DBforPostgreSQL/kiszolgálók/tanácsadók/recommendedActionSessions/művelet | Javaslatok készítése |
> | Műveletek | Microsoft. DBforPostgreSQL/kiszolgálók/konfigurációk/olvasás | Visszaadja egy kiszolgáló konfigurációinak listáját, vagy lekéri a megadott konfiguráció tulajdonságait. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/configurations/write | A megadott konfiguráció értékének frissítése |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/databases/delete | Deletes an existing PostgreSQL Database. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/databases/read | Return the list of PostgreSQL Databases or gets the properties for the specified Database. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/databases/write | Creates a PostgreSQL Database with the specified parameters or update the properties for the specified Database. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/delete | Töröl egy meglévő kiszolgálót. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | Töröl egy meglévő tűzfalszabály-szabályt. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/firewallRules/read | Visszaadja egy kiszolgáló tűzfalszabályok listáját, vagy lekéri a megadott tűzfalszabály tulajdonságait. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/firewallRules/write | Tűzfalszabály létrehozása a megadott paraméterekkel vagy egy meglévő szabály frissítése. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/keys/delete | Deletes an existing server key. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/keys/read | Return the list of server keys or gets the properties for the specified server key. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/keys/write | Creates a key with the specified parameters or update the properties or tags for the specified server key. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/logFiles/read | Return the list of PostgreSQL LogFiles. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/privateEndpointConnectionProxies/delete | Deletes an existing private endpoint connection proxy |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/privateEndpointConnectionProxies/read | Returns the list of private endpoint connection proxies or gets the properties for the specified private endpoint connection proxy. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/privateEndpointConnectionProxies/validate/action | Validates a private endpoint connection create call from NRP side |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/privateEndpointConnectionProxies/write | Creates a private endpoint connection proxy with the specified parameters or updates the properties or tags for the specified private endpoint connection proxy. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/privateEndpointConnections/delete | Deletes an existing private endpoint connection |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/privateEndpointConnections/read | Returns the list of private endpoint connections or gets the properties for the specified private endpoint connection. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/privateEndpointConnections/write | Approves or rejects an existing private endpoint connection |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/privateLinkResources/read | Get the private link resources for the corresponding PostgreSQL Server |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforráshoz tartozó nem független beállítás beolvasása |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | A postgres-kiszolgálók számára elérhető naplók beolvasása |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Az adatbázisokhoz elérhető metrikák visszatérési típusai |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/queryTexts/action | Return the text of a query |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/queryTexts/read | Return the texts for a list of queries |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/read | Return the list of servers or gets the properties for the specified server. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | Return the recoverable PostgreSQL Server info |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/replicas/read | Get read replicas of a PostgreSQL server |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/restart/action | Restarts a specific server. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | Retrieve details of the server threat detection policy configured on a given server |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | A kiszolgálói veszélyforrások észlelési házirendjének módosítása egy adott kiszolgálóhoz |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/topQueryStatistics/read | Return the list of Query Statistics for the top queries. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/updateConfigurations/action | A megadott kiszolgálóhoz tartozó konfigurációk frissítése |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | Meglévő Virtual Network-szabály törlése |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | Visszaadja a virtuális hálózati szabályok listáját, vagy lekéri a megadott virtuális hálózati szabály tulajdonságait. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | Létrehoz egy virtuális hálózati szabályt a megadott paraméterekkel, vagy frissíti a megadott virtuális hálózati szabály tulajdonságait vagy címkéit. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/waitStatistics/read | Return wait statistics for an instance |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/write | Létrehoz egy kiszolgálót a megadott paraméterekkel, vagy frissíti a megadott kiszolgálóhoz tartozó tulajdonságokat vagy címkéket. |
> | Műveletek | Microsoft.DBforPostgreSQL/serversv2/configurations/read | Visszaadja egy kiszolgáló konfigurációinak listáját, vagy lekéri a megadott konfiguráció tulajdonságait. |
> | Műveletek | Microsoft. DBforPostgreSQL/serversv2/konfigurációk/írás | A megadott konfiguráció értékének frissítése |
> | Műveletek | Microsoft. DBforPostgreSQL/serversv2/delete | Töröl egy meglévő kiszolgálót. |
> | Műveletek | Microsoft. DBforPostgreSQL/serversv2/firewallRules/delete | Töröl egy meglévő tűzfalszabály-szabályt. |
> | Műveletek | Microsoft. DBforPostgreSQL/serversv2/firewallRules/READ | Visszaadja egy kiszolgáló tűzfalszabályok listáját, vagy lekéri a megadott tűzfalszabály tulajdonságait. |
> | Műveletek | Microsoft. DBforPostgreSQL/serversv2/firewallRules/Write | Tűzfalszabály létrehozása a megadott paraméterekkel vagy egy meglévő szabály frissítése. |
> | Műveletek | Microsoft. DBforPostgreSQL/serversv2/Providers/Microsoft. bepillantást/diagnosticSettings/READ | Az erőforráshoz tartozó nem független beállítás beolvasása |
> | Műveletek | Microsoft. DBforPostgreSQL/serversv2/Providers/Microsoft. bepillantások/diagnosticSettings/írás | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft. DBforPostgreSQL/serversv2/Providers/Microsoft. bepillantást/logDefinitions/READ | A postgres-kiszolgálók számára elérhető naplók beolvasása |
> | Műveletek | Microsoft. DBforPostgreSQL/serversv2/Providers/Microsoft. bepillantást/metricDefinitions/READ | Az adatbázisokhoz elérhető metrikák visszatérési típusai |
> | Műveletek | Microsoft. DBforPostgreSQL/serversv2/READ | Return the list of servers or gets the properties for the specified server. |
> | Műveletek | Microsoft. DBforPostgreSQL/serversv2/updateConfigurations/művelet | A megadott kiszolgálóhoz tartozó konfigurációk frissítése |
> | Műveletek | Microsoft. DBforPostgreSQL/serversv2/Write | Létrehoz egy kiszolgálót a megadott paraméterekkel, vagy frissíti a megadott kiszolgálóhoz tartozó tulajdonságokat vagy címkéket. |

## <a name="microsoftdevices"></a>Microsoft. Devices

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Devices/Account/diagnosticSettings/READ | Az erőforrás diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft. Devices/Account/diagnosticSettings/Write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft. Devices/Account/logDefinitions/READ | Gets the available log definitions for the IotHub Service |
> | Műveletek | Microsoft. Devices/Account/metricDefinitions/READ | Gets the available metrics for the IotHub service |
> | Műveletek | Microsoft. Devices/checkNameAvailability/Action | Ellenőrizze, hogy elérhető-e a IotHub neve |
> | Műveletek | Microsoft. Devices/checkNameAvailability/Action | Ellenőrizze, hogy elérhető-e a IotHub neve |
> | Műveletek | Microsoft. Devices/checkProvisioningServiceNameAvailability/Action | Ellenőrizze, hogy elérhető-e a kiépítési szolgáltatás neve |
> | Műveletek | Microsoft. Devices/checkProvisioningServiceNameAvailability/Action | Ellenőrizze, hogy elérhető-e a kiépítési szolgáltatás neve |
> | Műveletek | Microsoft. Devices/digitalTwins/delete | Meglévő digitális Twins-fiók és annak összes gyermekének törlése |
> | Műveletek | Microsoft. Devices/digitalTwins/operationresults/READ | Egy művelet állapotának lekérése egy digitális ikrek-fiókkal |
> | Műveletek | Microsoft. Devices/digitalTwins/READ | Az előfizetéshez társított digitális Twins-fiókok listájának beolvasása |
> | Műveletek | Microsoft. Devices/digitalTwins/SKU/READ | A digitális Twins-fiókok érvényes SKU-fiókjainak listájának beolvasása |
> | Műveletek | Microsoft. Devices/digitalTwins/Write | Új, többjegyű ikrek-fiók létrehozása |
> | Műveletek | Microsoft. Devices/ElasticPools/diagnosticSettings/READ | Az erőforrás diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft. Devices/ElasticPools/diagnosticSettings/Write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft. Devices/elasticPools/eventGridFilters/delete | Törli a rugalmas készlet Event Grid szűrőt |
> | Műveletek | Microsoft. Devices/elasticPools/eventGridFilters/READ | Lekéri a rugalmas készlet Event Grid szűrőt |
> | Műveletek | Microsoft. Devices/elasticPools/eventGridFilters/Write | Új létrehozása vagy meglévő rugalmas készlet Event Grid szűrő frissítése |
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/Certificates/delete | Deletes Certificate |
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/Certificates/generateVerificationCode/Action | Generate Verification code |
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/tanúsítványok/olvasás | Gets the Certificate |
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/Certificates/ellenőrzés/művelet | Verify Certificate resource |
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/tanúsítványok/írás | Create or Update Certificate |
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/delete | Az IotHub-bérlő erőforrásának törlése |
> | Műveletek | Microsoft. Devices/ElasticPools/IotHubTenants/diagnosticSettings/READ | Az erőforrás diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft. Devices/ElasticPools/IotHubTenants/diagnosticSettings/Write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/delete | Delete EventHub Consumer Group |
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/READ | Get EventHub Consumer Group(s) |
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | Create EventHub Consumer Group |
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/exportDevices/Action | Export Devices |
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/getStats/READ | A IotHub bérlői statisztika erőforrásának beolvasása |
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/importDevices/Action | Import Devices |
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys műveletének beolvasása/művelet | A IotHub-bérlő kulcsának beolvasása |
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/Jobs/READ | Get Job(s) details submitted on given IotHub |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | Gets IotHub tenant keys |
> | Műveletek | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | Gets the available log definitions for the IotHub Service |
> | Műveletek | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | Gets the available metrics for the IotHub service |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | Get Quota Metrics |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/Read | Gets the IotHub tenant resource |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | Test a message against all existing Routes |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | Test a message against a provided test Route |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | Gets the health of all routing Endpoints for an IotHub |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/securitySettings/operationResults/Read | Get the result of the Async Put operation for Iot Tenant Hub SecuritySettings |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/securitySettings/Read | Get the Azure Security Center settings on Iot Tenant Hub |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/securitySettings/Write | Update the Azure Security Center settings on Iot Tenant Hub |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/Write | Create or Update the IotHub tenant resource |
> | Műveletek | Microsoft.Devices/ElasticPools/metricDefinitions/read | Gets the available metrics for the IotHub service |
> | Műveletek | Microsoft.Devices/iotHubs/certificates/Delete | Deletes Certificate |
> | Műveletek | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | Generate Verification code |
> | Műveletek | Microsoft.Devices/iotHubs/certificates/Read | Gets the Certificate |
> | Műveletek | Microsoft.Devices/iotHubs/certificates/verify/Action | Verify Certificate resource |
> | Műveletek | Microsoft.Devices/iotHubs/certificates/Write | Create or Update Certificate |
> | Műveletek | Microsoft.Devices/iotHubs/Delete | Delete IotHub Resource |
> | Műveletek | Microsoft.Devices/IotHubs/diagnosticSettings/read | Az erőforrás diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft.Devices/IotHubs/diagnosticSettings/write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft.Devices/iotHubs/eventGridFilters/Delete | Deletes the Event Grid filter |
> | Műveletek | Microsoft.Devices/iotHubs/eventGridFilters/Read | Gets the Event Grid filter |
> | Műveletek | Microsoft.Devices/iotHubs/eventGridFilters/Write | Create new or Update existing Event Grid filter |
> | Műveletek | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | Delete EventHub Consumer Group |
> | Műveletek | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | Get EventHub Consumer Group(s) |
> | Műveletek | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | Create EventHub Consumer Group |
> | Műveletek | Microsoft.Devices/iotHubs/exportDevices/Action | Export Devices |
> | Műveletek | Microsoft.Devices/iotHubs/importDevices/Action | Import Devices |
> | Műveletek | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | Get IotHub Key for the given name |
> | Műveletek | Microsoft.Devices/iotHubs/iotHubStats/Read | Get IotHub Statistics |
> | Műveletek | Microsoft.Devices/iotHubs/jobs/Read | Get Job(s) details submitted on given IotHub |
> | Műveletek | Microsoft.Devices/iotHubs/listkeys/Action | Get all IotHub Keys |
> | Műveletek | Microsoft.Devices/IotHubs/logDefinitions/read | Gets the available log definitions for the IotHub Service |
> | Műveletek | Microsoft.Devices/IotHubs/metricDefinitions/read | Gets the available metrics for the IotHub service |
> | Műveletek | Microsoft.Devices/iotHubs/operationresults/Read | Get Operation Result (Obsolete API) |
> | Műveletek | Microsoft.Devices/iotHubs/quotaMetrics/Read | Get Quota Metrics |
> | Műveletek | Microsoft.Devices/iotHubs/Read | Gets the IotHub resource(s) |
> | Műveletek | Microsoft.Devices/iotHubs/routing/$testall/Action | Test a message against all existing Routes |
> | Műveletek | Microsoft.Devices/iotHubs/routing/$testnew/Action | Test a message against a provided test Route |
> | Műveletek | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | Gets the health of all routing Endpoints for an IotHub |
> | Műveletek | Microsoft.Devices/iotHubs/securitySettings/operationResults/Read | Get the result of the Async Put operation for Iot Hub SecuritySettings |
> | Műveletek | Microsoft.Devices/iotHubs/securitySettings/Read | Get the Azure Security Center settings on Iot Hub |
> | Műveletek | Microsoft.Devices/iotHubs/securitySettings/Write | Update the Azure Security Center settings on Iot Hub |
> | Műveletek | Microsoft.Devices/iotHubs/skus/Read | Get valid IotHub Skus |
> | Műveletek | Microsoft.Devices/iotHubs/Write | Create or update IotHub Resource |
> | Műveletek | Microsoft.Devices/locations/operationresults/Read | Get Location based Operation Result |
> | Műveletek | Microsoft.Devices/operationresults/Read | Get Operation Result |
> | Műveletek | Microsoft.Devices/operations/Read | Get All ResourceProvider Operations |
> | Műveletek | Microsoft.Devices/provisioningServices/certificates/Delete | Deletes Certificate |
> | Műveletek | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | Generate Verification code |
> | Műveletek | Microsoft.Devices/provisioningServices/certificates/Read | Gets the Certificate |
> | Műveletek | Microsoft.Devices/provisioningServices/certificates/verify/Action | Verify Certificate resource |
> | Műveletek | Microsoft.Devices/provisioningServices/certificates/Write | Create or Update Certificate |
> | Műveletek | Microsoft.Devices/provisioningServices/Delete | Delete IotDps resource |
> | Műveletek | Microsoft.Devices/provisioningServices/diagnosticSettings/read | Az erőforrás diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft.Devices/provisioningServices/diagnosticSettings/write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft.Devices/provisioningServices/keys/listkeys/Action | Get IotDps Keys for key name |
> | Műveletek | Microsoft.Devices/provisioningServices/listkeys/Action | Get all IotDps keys |
> | Műveletek | Microsoft.Devices/provisioningServices/logDefinitions/read | Gets the available log definitions for the provisioning Service |
> | Műveletek | Microsoft.Devices/provisioningServices/metricDefinitions/read | Gets the available metrics for the provisioning service |
> | Műveletek | Microsoft.Devices/provisioningServices/operationresults/Read | Get DPS Operation Result |
> | Műveletek | Microsoft.Devices/provisioningServices/Read | Get IotDps resource |
> | Műveletek | Microsoft.Devices/provisioningServices/skus/Read | Get valid IotDps Skus |
> | Műveletek | Microsoft.Devices/provisioningServices/Write | Create IotDps resource |
> | Műveletek | Microsoft.Devices/register/action | Register the subscription for the IotHub resource provider and enables the creation of IotHub resources |
> | Műveletek | Microsoft.Devices/usages/Read | Get subscription usage details for this provider. |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.DevSpaces/controllers/delete | Delete Azure Dev Spaces Controller and dataplane services |
> | Műveletek | Microsoft.DevSpaces/controllers/listConnectionDetails/action | List connection details for the Azure Dev Spaces Controller's infrastructure |
> | Műveletek | Microsoft.DevSpaces/controllers/read | Read Azure Dev Spaces Controller properties |
> | Műveletek | Microsoft.DevSpaces/controllers/write | Create or Update Azure Dev Spaces Controller properties |
> | Műveletek | Microsoft.DevSpaces/locations/checkContainerHostMapping/action | Check existing controller mapping for a container host |
> | Műveletek | Microsoft.DevSpaces/locations/operationresults/read | Read status for an asynchronous operation |
> | Műveletek | Microsoft.DevSpaces/register/action | Register Microsoft Dev Spaces resource provider with a subscription |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.DevTestLab/labCenters/delete | Delete lab centers. |
> | Műveletek | Microsoft.DevTestLab/labCenters/read | Read lab centers. |
> | Műveletek | Microsoft.DevTestLab/labCenters/write | Add or modify lab centers. |
> | Műveletek | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | Read azure resource manager templates. |
> | Műveletek | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | Generates an Azure Resource Manager template for the given artifact, uploads the required files to a storage account, and validates the generated artifact. |
> | Műveletek | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | Read artifacts. |
> | Műveletek | Microsoft.DevTestLab/labs/artifactSources/delete | Delete artifact sources. |
> | Műveletek | Microsoft.DevTestLab/labs/artifactSources/read | Read artifact sources. |
> | Műveletek | Microsoft.DevTestLab/labs/artifactSources/write | Add or modify artifact sources. |
> | Műveletek | Microsoft.DevTestLab/labs/ClaimAnyVm/action | Claim a random claimable virtual machine in the lab. |
> | Műveletek | Microsoft.DevTestLab/labs/costs/read | Read costs. |
> | Műveletek | Microsoft.DevTestLab/labs/costs/write | Add or modify costs. |
> | Műveletek | Microsoft.DevTestLab/labs/CreateEnvironment/action | Create virtual machines in a lab. |
> | Műveletek | Microsoft.DevTestLab/labs/customImages/delete | Delete custom images. |
> | Műveletek | Microsoft.DevTestLab/labs/customImages/read | Read custom images. |
> | Műveletek | Microsoft.DevTestLab/labs/customImages/write | Add or modify custom images. |
> | Műveletek | Microsoft.DevTestLab/labs/delete | Delete labs. |
> | Műveletek | Microsoft.DevTestLab/labs/EnsureCurrentUserProfile/action | Ensure the current user has a valid profile in the lab. |
> | Műveletek | Microsoft.DevTestLab/labs/ExportResourceUsage/action | Exports the lab resource usage into a storage account |
> | Műveletek | Microsoft.DevTestLab/labs/formulas/delete | Delete formulas. |
> | Műveletek | Microsoft.DevTestLab/labs/formulas/read | Read formulas. |
> | Műveletek | Microsoft.DevTestLab/labs/formulas/write | Add or modify formulas. |
> | Műveletek | Microsoft.DevTestLab/labs/galleryImages/read | Read gallery images. |
> | Műveletek | Microsoft.DevTestLab/labs/GenerateUploadUri/action | Generate a URI for uploading custom disk images to a Lab. |
> | Műveletek | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | Import a virtual machine into a different lab. |
> | Műveletek | Microsoft.DevTestLab/labs/ListVhds/action | List disk images available for custom image creation. |
> | Műveletek | Microsoft.DevTestLab/labs/notificationChannels/delete | Delete notification channels. |
> | Műveletek | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | Send notification to provided channel. |
> | Műveletek | Microsoft.DevTestLab/labs/notificationChannels/read | Read notification channels. |
> | Műveletek | Microsoft.DevTestLab/labs/notificationChannels/write | Add or modify notification channels. |
> | Műveletek | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | Evaluates lab policy. |
> | Műveletek | Microsoft.DevTestLab/labs/policySets/policies/delete | Delete policies. |
> | Műveletek | Microsoft.DevTestLab/labs/policySets/policies/read | Read policies. |
> | Műveletek | Microsoft.DevTestLab/labs/policySets/policies/write | Add or modify policies. |
> | Műveletek | Microsoft.DevTestLab/labs/policySets/read | Read policy sets. |
> | Műveletek | Microsoft.DevTestLab/labs/read | Read labs. |
> | Műveletek | Microsoft.DevTestLab/labs/schedules/delete | Delete schedules. |
> | Műveletek | Microsoft.DevTestLab/labs/schedules/Execute/action | Ütemezett végrehajtás. |
> | Műveletek | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | Lists all applicable schedules |
> | Műveletek | Microsoft.DevTestLab/labs/schedules/read | Read schedules. |
> | Műveletek | Microsoft.DevTestLab/labs/schedules/write | Add or modify schedules. |
> | Műveletek | Microsoft.DevTestLab/labs/serviceRunners/delete | Delete service runners. |
> | Műveletek | Microsoft.DevTestLab/labs/serviceRunners/read | Read service runners. |
> | Műveletek | Microsoft.DevTestLab/labs/serviceRunners/write | Add or modify service runners. |
> | Műveletek | Microsoft.DevTestLab/labs/sharedGalleries/delete | Delete shared galleries. |
> | Műveletek | Microsoft.DevTestLab/labs/sharedGalleries/read | Read shared galleries. |
> | Műveletek | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/delete | Delete shared images. |
> | Műveletek | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/read | Read shared images. |
> | Műveletek | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/write | Add or modify shared images. |
> | Műveletek | Microsoft.DevTestLab/labs/sharedGalleries/write | Add or modify shared galleries. |
> | Műveletek | Microsoft.DevTestLab/labs/users/delete | Delete user profiles. |
> | Műveletek | Microsoft.DevTestLab/labs/users/disks/Attach/action | Attach and create the lease of the disk to the virtual machine. |
> | Műveletek | Microsoft.DevTestLab/labs/users/disks/delete | Delete disks. |
> | Műveletek | Microsoft.DevTestLab/labs/users/disks/Detach/action | Detach and break the lease of the disk attached to the virtual machine. |
> | Műveletek | Microsoft.DevTestLab/labs/users/disks/read | Read disks. |
> | Műveletek | Microsoft.DevTestLab/labs/users/disks/write | Add or modify disks. |
> | Műveletek | Microsoft.DevTestLab/labs/users/environments/delete | Delete environments. |
> | Műveletek | Microsoft.DevTestLab/labs/users/environments/read | Read environments. |
> | Műveletek | Microsoft.DevTestLab/labs/users/environments/write | Add or modify environments. |
> | Műveletek | Microsoft.DevTestLab/labs/users/read | Read user profiles. |
> | Műveletek | Microsoft.DevTestLab/labs/users/secrets/delete | Delete secrets. |
> | Műveletek | Microsoft.DevTestLab/labs/users/secrets/read | Read secrets. |
> | Műveletek | Microsoft.DevTestLab/labs/users/secrets/write | Add or modify secrets. |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | Delete service fabrics. |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | Lists the applicable start/stop schedules, if any. |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/read | Read service fabrics. |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | Delete schedules. |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | Ütemezett végrehajtás. |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | Read schedules. |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | Add or modify schedules. |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | Start a service fabric. |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | Stop a service fabric |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/write | Add or modify service fabrics. |
> | Műveletek | Microsoft.DevTestLab/labs/users/write | Add or modify user profiles. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | Attach a new or existing data disk to virtual machine. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | Apply artifacts to virtual machine. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | Take ownership of an existing virtual machine |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/ClearArtifactResults/action | Clears the artifact results of the virtual machine. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/delete | Delete virtual machines. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | Detach the specified disk from the virtual machine. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/GetRdpFileContents/action | Gets a string that represents the contents of the RDP file for the virtual machine |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | Lists the applicable start/stop schedules, if any. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/read | Read virtual machines. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | Redeploy a virtual machine |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | Resize Virtual Machine. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | Restart a virtual machine. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | Delete schedules. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | Ütemezett végrehajtás. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | Read schedules. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | Add or modify schedules. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/Start/action | Start a virtual machine. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | Stop a virtual machine |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | Transfers all data disks attached to the virtual machine to be owned by the current user. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | Release ownership of an existing virtual machine |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/write | Add or modify virtual machines. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualNetworks/bastionHosts/delete | Delete bastionhosts. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualNetworks/bastionHosts/read | Read bastionhosts. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualNetworks/bastionHosts/write | Add or modify bastionhosts. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualNetworks/delete | Delete virtual networks. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualNetworks/read | Read virtual networks. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualNetworks/write | Add or modify virtual networks. |
> | Műveletek | Microsoft.DevTestLab/labs/vmPools/delete | Delete virtual machine pools. |
> | Műveletek | Microsoft. segédösszetevője/Labs/vmPools/READ | Virtuális gépek készletének olvasása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/vmPools/Write | Virtuálisgép-készletek hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/írás | Add or modify labs. |
> | Műveletek | Microsoft. segédösszetevője/Locations/Operations/READ | Read operations. |
> | Műveletek | Microsoft. segédösszetevője/regisztráció/művelet | Registers the subscription |
> | Műveletek | Microsoft. segédösszetevője/ütemtervek/törlés | Delete schedules. |
> | Műveletek | Microsoft. segédösszetevője/ütemtervek/végrehajtás/művelet | Ütemezett végrehajtás. |
> | Műveletek | Microsoft. segédösszetevője/ütemezett/beolvasás | Read schedules. |
> | Műveletek | Microsoft. segédösszetevője/ütemtervek/újracélzás/művelet | Frissíti az ütemterv cél erőforrás-azonosítóját. |
> | Műveletek | Microsoft. segédösszetevője/ütemterv/írás | Add or modify schedules. |

## <a name="microsoftdocumentdb"></a>Microsoft. DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. DocumentDB/databaseAccountNames/READ | A név rendelkezésre állását ellenőrzi. |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/adatbázisok/gyűjtemények/törlés | Gyűjtemény törlése. Csak API-típusokra alkalmazható: "mongodb". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/adatbázisok/gyűjtemények/operationResults/READ | Az aszinkron művelet állapotának olvasása. Csak API-típusokra alkalmazható: "mongodb". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/adatbázisok/gyűjtemények/olvasás | Beolvashat egy gyűjteményt, vagy listázhatja az összes gyűjteményt. Csak API-típusokra alkalmazható: "mongodb". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/adatbázisok/gyűjtemények/beállítások/operationResults/olvasás | Az aszinkron művelet állapotának olvasása. Csak API-típusokra alkalmazható: "mongodb". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/adatbázisok/gyűjtemények/beállítások/olvasás | A gyűjtemény teljesítményének beolvasása. Csak API-típusokra alkalmazható: "mongodb". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/adatbázisok/gyűjtemények/beállítások/írás | Egy gyűjtemény átviteli sebességének frissítése. Csak API-típusokra alkalmazható: "mongodb". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/adatbázisok/gyűjtemények/írás | Gyűjtemény létrehozása vagy frissítése. Csak API-típusokra alkalmazható: "mongodb". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/adatbázisok/tárolók/törlés | Tároló törlése. Csak API-típusokra alkalmazható: "SQL". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/adatbázisok/tárolók/operationResults/olvasás | Az aszinkron művelet állapotának olvasása. Csak API-típusokra alkalmazható: "SQL". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/adatbázisok/tárolók/olvasás | Olvassa el a tárolót, vagy sorolja fel az összes tárolót. Csak API-típusokra alkalmazható: "SQL". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/adatbázisok/tárolók/beállítások/operationResults/olvasás | Az aszinkron művelet állapotának olvasása. Csak API-típusokra alkalmazható: "SQL". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/adatbázisok/tárolók/beállítások/olvasás | A tároló átviteli sebességének beolvasása. Csak API-típusokra alkalmazható: "SQL". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/adatbázisok/tárolók/beállítások/írás | Tároló átviteli sebességének frissítése. Csak API-típusokra alkalmazható: "SQL". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/adatbázisok/tárolók/írás | Tároló létrehozása vagy frissítése. Csak API-típusokra alkalmazható: "SQL". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/adatbázisok/törlés | Adatbázis törlése. Csak API-típusokra alkalmazható: "SQL", "mongodb", "gremlin'". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/adatbázisok/gráfok/törlés | Gráf törlése. Csak az "gremlin'" API-típusokra alkalmazható. |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/adatbázisok/gráfok/operationResults/olvasás | Az aszinkron művelet állapotának olvasása. Csak az "gremlin'" API-típusokra alkalmazható. |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/adatbázisok/gráfok/olvasás | Egy gráf beolvasása vagy az összes gráf listázása. Csak az "gremlin'" API-típusokra alkalmazható. |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/adatbázisok/gráfok/beállítások/operationResults/olvasás | Az aszinkron művelet állapotának olvasása. Csak az "gremlin'" API-típusokra alkalmazható. Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/adatbázisok/diagramok/beállítások/olvasás | Diagram átviteli sebességének beolvasása. Csak az "gremlin'" API-típusokra alkalmazható. Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/adatbázisok/diagramok/beállítások/írás | Egy gráf átviteli sebességének frissítése. Csak az "gremlin'" API-típusokra alkalmazható. Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/adatbázisok/diagramok/írás | Gráf létrehozása vagy frissítése. Csak az "gremlin'" API-típusokra alkalmazható. |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/adatbázisok/operationResults/olvasás | Az aszinkron művelet állapotának olvasása. Csak API-típusokra alkalmazható: "SQL", "mongodb", "gremlin'". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/adatbázisok/olvasás | Adatbázis beolvasása vagy az összes adatbázis listázása. Csak API-típusokra alkalmazható: "SQL", "mongodb", "gremlin'". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/adatbázisok/beállítások/operationResults/olvasás | Az aszinkron művelet állapotának olvasása. Csak API-típusokra alkalmazható: "SQL", "mongodb", "gremlin'". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/adatbázisok/beállítások/olvasás | Olvassa el az adatbázis átviteli sebességét. Csak API-típusokra alkalmazható: "SQL", "mongodb", "gremlin'". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/adatbázisok/beállítások/írás | Adatbázis átviteli sebességének frissítése. Csak API-típusokra alkalmazható: "SQL", "mongodb", "gremlin'". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/adatbázisok/írás | Hozzon létre egy adatbázist. Csak API-típusokra alkalmazható: "SQL", "mongodb", "gremlin'". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/alapterületek/törlés | Szóköz törlése. Csak az API-típusokra vonatkozik: "Cassandra". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/alapterületek/operationResults/olvasás | Az aszinkron művelet állapotának olvasása. Csak az API-típusokra vonatkozik: "Cassandra". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/alapterületek/olvasás | A szóköz beolvasása vagy az összes szóköz felsorolása. Csak az API-típusokra vonatkozik: "Cassandra". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/alapterületek/beállítások/operationResults/olvasás | Az aszinkron művelet állapotának olvasása. Csak az API-típusokra vonatkozik: "Cassandra". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/alapterületek/beállítások/olvasás | Lemezterület-átviteli sebesség beolvasása. Csak az API-típusokra vonatkozik: "Cassandra". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/alapterületek/beállítások/írás | Lemezterület-átviteli sebesség frissítése Csak az API-típusokra vonatkozik: "Cassandra". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/alapterületek/táblák/törlés | Tábla törlése. Csak az API-típusokra vonatkozik: "Cassandra". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/alapterületek/táblák/operationResults/olvasás | Az aszinkron művelet állapotának olvasása. Csak az API-típusokra vonatkozik: "Cassandra". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/alapterületek/táblák/olvasás | Az összes tábla beolvasása vagy listázása. Csak az API-típusokra vonatkozik: "Cassandra". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/alapterületek/táblák/beállítások/operationResults/olvasás | Az aszinkron művelet állapotának olvasása. Csak az API-típusokra vonatkozik: "Cassandra". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/alapterületek/táblák/beállítások/olvasás | Olvassa el a tábla átviteli sebességét. Csak az API-típusokra vonatkozik: "Cassandra". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/alapterületek/táblák/beállítások/írás | Egy tábla átviteli sebességének frissítése. Csak az API-típusokra vonatkozik: "Cassandra". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/alapterületek/táblák/írás | Tábla létrehozása vagy frissítése. Csak az API-típusokra vonatkozik: "Cassandra". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/alapterületek/írás | Hozzon létre egy lemezterületet. Csak az API-típusokra vonatkozik: "Cassandra". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/táblák/törlés | Tábla törlése. Csak API-típusokra alkalmazható: "Table". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/táblák/operationResults/olvasás | Az aszinkron művelet állapotának olvasása. Csak API-típusokra alkalmazható: "Table". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/táblák/olvasás | Az összes tábla beolvasása vagy listázása. Csak API-típusokra alkalmazható: "Table". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/táblák/beállítások/operationResults/olvasás | Az aszinkron művelet állapotának olvasása. Csak API-típusokra alkalmazható: "Table". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/táblák/beállítások/olvasás | Olvassa el a tábla átviteli sebességét. Csak API-típusokra alkalmazható: "Table". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/táblák/beállítások/írás | Egy tábla átviteli sebességének frissítése. Csak API-típusokra alkalmazható: "Table". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/API-k/táblák/írás | Tábla létrehozása vagy frissítése. Csak API-típusokra alkalmazható: "Table". |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/Backup/művelet | A biztonsági mentés konfigurálására vonatkozó kérelem küldése |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/changeResourceGroup/művelet | Adatbázis-fiók erőforráscsoportának módosítása |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/adatbázisok/gyűjtemények/metricDefinitions/READ | Beolvassa a gyűjtemény metrikájának definícióit. |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/adatbázisok/gyűjtemények/mérőszámok/olvasás | A gyűjtemény metrikáinak beolvasása. |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/adatbázisok/gyűjtemények/partitionKeyRangeId/mérőszámok/olvasás | Adatbázis-fiók partíciós kulcs szintű metrikáinak olvasása |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/adatbázisok/gyűjtemények/partíciók/mérőszámok/olvasás | Adatbázis-fiók partíciós szintjének metrikáinak olvasása |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/adatbázisok/gyűjtemények/partíciók/olvasás | Adatbázis-fiók partícióinak beolvasása gyűjteményben |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/adatbázisok/gyűjtemények/partíciók/használati adatok/olvasás | Adatbázis-fiók partíciós szintjének használatának elolvasása |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/adatbázisok/gyűjtemények/használati adatok/olvasás | Beolvassa a gyűjtemény használati adatait. |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/adatbázisok/metricDefinitions/READ | Az adatbázis metrika-definícióinak beolvasása |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/adatbázisok/mérőszámok/olvasás | Az adatbázis-metrikák beolvasása. |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/adatbázisok/használati adatok/olvasás | Az adatbázis-használat beolvasása. |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/delete | Törli az adatbázis-fiókokat. |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/failoverPriorityChange/művelet | Egy adatbázis-fiók régiói feladatátvételi prioritásainak módosítása. Ez a manuális feladatátvételi művelet végrehajtásához használatos. |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/getBackupPolicy/művelet | Az adatbázis-fiók biztonsági mentési szabályzatának beolvasása |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/listConnectionStrings/művelet | Adatbázis-fiókhoz tartozó kapcsolatok karakterláncának beolvasása |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/Listkeys műveletének beolvasása/művelet | Adatbázis-fiók kulcsainak listázása |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/metricDefinitions/READ | Az adatbázis-fiók metrikáinak definícióinak beolvasása. |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/metrika/olvasás | Az adatbázis-fiók metrikáinak beolvasása. |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/offlineRegion/művelet | Offline egy adatbázis-fiók régiója.  |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/onlineRegion/művelet | Egy adatbázis-fiókhoz tartozó régió online. |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/operationResults/READ | Az aszinkron művelet olvasási állapota |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/percentilis/mérőszámok/olvasás | Olvasási késési metrikák |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/percentilis/olvasás | Replikációs késések százalékos értékének olvasása |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/percentilis/sourceRegion/targetRegion/metrika/olvasás | Adott forrás-és célobjektum késési metrikáinak olvasása |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/percentilis/targetRegion/metrika/olvasás | Adott célcsoport késési metrikáinak olvasása |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnectionProxies/delete | Az adatbázis-fiókhoz tartozó privát végponti kapcsolatok proxyjának törlése |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnectionProxies/operationResults/READ | A magánhálózati végponti kapcsolatok proxyjának aszinkron műveletének olvasási állapota |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnectionProxies/READ | Az adatbázis-fiók privát végpont-ügyfélkapcsolati proxyjának beolvasása |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnectionProxies/validate/Action | Az adatbázis-fiókhoz tartozó magánhálózati végponti kapcsolatok proxyjának ellenőrzése |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnectionProxies/Write | Az adatbázis-fiókhoz tartozó magánhálózati végponti kapcsolatok proxyjának létrehozása vagy frissítése |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnections/delete | Adatbázis-fiókhoz tartozó magánhálózati végponti kapcsolatok törlése |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnections/operationResults/READ | PrivateEndpointConnenctions aszinkron művelet olvasási állapota |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnections/READ | Privát végponti kapcsolat beolvasása vagy az adatbázis-fiók összes privát végpont-kapcsolatának listázása |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnections/Write | Adatbázis-fiókhoz tartozó magánhálózati végponti kapcsolatok létrehozása vagy frissítése |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/privateLinkResources/READ | Egy privát kapcsolat erőforrásának beolvasása vagy az adatbázis-fiók összes privát kapcsolati erőforrásának listázása |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/READ | Egy adatbázis-fiók beolvasása. |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/readonlykeys/művelet | Az adatbázis-fiók írásvédett kulcsának beolvasása. |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/readonlykeys/READ | Az adatbázis-fiók írásvédett kulcsának beolvasása. |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/regenerateKey/művelet | Adatbázis-fiók kulcsainak elforgatása |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/régió/adatbázisok/gyűjtemények/mérőszámok/olvasás | A regionális gyűjtemény metrikáinak beolvasása. |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/régió/adatbázisok/gyűjtemények/partitionKeyRangeId/mérőszámok/olvasás | Regionális adatbázis-fiók partíciós kulcs szintű metrikáinak olvasása |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/régió/adatbázisok/gyűjtemények/partíciók/mérőszámok/olvasás | A regionális adatbázis-fiók partíciós szintjén lévő metrikák olvasása |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/régió/adatbázisok/gyűjtemények/partíciók/olvasás | A regionális adatbázis-fiók partícióinak beolvasása egy gyűjteményben |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/régió/metrika/olvasás | A régió és az adatbázis-fiók metrikáinak beolvasása. |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/Restore/Action | Visszaállítási kérelem elküldése |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/használat/olvasás | Az adatbázis-fiók használatának beolvasása. |
> | Műveletek | Microsoft. DocumentDB/databaseAccounts/Write | Adatbázis-fiókok frissítése. |
> | Műveletek | Microsoft. DocumentDB/Locations/deleteVirtualNetworkOrSubnets/Action | Értesíti a Microsoft. DocumentDB, hogy a VirtualNetwork vagy az alhálózat törölve lett |
> | Műveletek | Microsoft. DocumentDB/Locations/deleteVirtualNetworkOrSubnets/operationResults/READ | DeleteVirtualNetworkOrSubnets aszinkron művelet olvasási állapota |
> | Műveletek | Microsoft. DocumentDB/Locations/operationsStatus/READ | Aszinkron műveletek állapotának olvasása |
> | Műveletek | Microsoft. DocumentDB/operationResults/READ | Az aszinkron művelet olvasási állapota |
> | Műveletek | Microsoft. DocumentDB/Operations/READ | A Microsoft DocumentDB elérhető olvasási műveletek  |
> | Műveletek | Microsoft. DocumentDB/regisztráció/művelet |  Regisztrálja a Microsoft DocumentDB erőforrás-szolgáltatót az előfizetéshez |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. DomainRegistration/checkDomainAvailability/művelet | Ellenőrizze, hogy van-e elérhető tartomány a vásárláshoz |
> | Műveletek | Microsoft. DomainRegistration/tartományok/törlés | Meglévő tartomány törlése. |
> | Műveletek | Microsoft. DomainRegistration/Domains/domainownershipidentifiers/delete | Tulajdonos azonosítójának törlése |
> | Műveletek | Microsoft. DomainRegistration/tartományok/domainownershipidentifiers/olvasás | Tulajdonosi azonosítók listázása |
> | Műveletek | Microsoft. DomainRegistration/tartományok/domainownershipidentifiers/olvasás | Tulajdonos azonosítójának beolvasása |
> | Műveletek | Microsoft. DomainRegistration/tartományok/domainownershipidentifiers/írás | Azonosító létrehozása vagy frissítése |
> | Műveletek | Microsoft. DomainRegistration/tartományok/operationresults/olvasás | Tartományi művelet beolvasása |
> | Műveletek | Microsoft. DomainRegistration/tartományok/olvasás | Tartományok listájának beolvasása |
> | Műveletek | Microsoft. DomainRegistration/tartományok/olvasás | Tartomány beolvasása |
> | Műveletek | Microsoft. DomainRegistration/tartományok/megújítás/művelet | Meglévő tartomány megújítása. |
> | Műveletek | Microsoft. DomainRegistration/tartományok/írás | Új tartomány hozzáadása vagy meglévő frissítése |
> | Műveletek | Microsoft. DomainRegistration/generateSsoRequest/művelet | Kérelem létrehozása a tartomány-ellenőrzési központba való bejelentkezéshez. |
> | Műveletek | Microsoft. DomainRegistration/listDomainRecommendations/művelet | A listához tartozó javaslatok lekérdezése kulcsszavak alapján |
> | Műveletek | Microsoft. DomainRegistration/Operations/READ | Az App Service-tartomány regisztrációjának összes műveletének listázása |
> | Műveletek | Microsoft. DomainRegistration/regisztráció/művelet | Regisztrálja a Microsoft Domains erőforrás-szolgáltatót az előfizetéshez |
> | Műveletek | Microsoft. DomainRegistration/topLevelDomains/listAgreements/művelet | Szerződés listázása művelet |
> | Műveletek | Microsoft. DomainRegistration/topLevelDomains/READ | TopLevel-tartományok beolvasása |
> | Műveletek | Microsoft. DomainRegistration/topLevelDomains/READ | TopLevel-tartomány beolvasása |
> | Műveletek | Microsoft. DomainRegistration/validateDomainRegistrationInformation/művelet | Tartományi vásárlási objektum ellenőrzése a küldés nélkül |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. EventGrid/tartományok/törlés | Tartomány törlése |
> | Műveletek | Microsoft. EventGrid/Domains/Listkeys műveletének beolvasása/Action | Tartomány kulcsainak listázása |
> | Műveletek | Microsoft. EventGrid/Domains/Providers/Microsoft. bepillantást/logDefinitions/READ | A diagnosztikai naplókhoz való hozzáférés engedélyezése |
> | Műveletek | Microsoft. EventGrid/Domains/Providers/Microsoft. bepillantást/metricDefinitions/READ | A tartományok elérhető metrikáinak beolvasása |
> | Műveletek | Microsoft. EventGrid/tartományok/olvasás | Tartomány beolvasása |
> | Műveletek | Microsoft. EventGrid/Domains/regenerateKey/Action | Tartomány kulcsának újralétrehozása |
> | Műveletek | Microsoft. EventGrid/tartományok/témakörök/törlés | Tartományi témakör törlése |
> | Műveletek | Microsoft. EventGrid/tartományok/témakörök/olvasás | Tartományi témakör beolvasása |
> | Műveletek | Microsoft. EventGrid/tartományok/témakörök/írás | Tartományi témakör létrehozása vagy frissítése |
> | Műveletek | Microsoft. EventGrid/tartományok/írás | Tartomány létrehozása vagy frissítése |
> | Műveletek | Microsoft. EventGrid/eventSubscriptions/delete | EventSubscription törlése |
> | Műveletek | Microsoft. EventGrid/eventSubscriptions/getFullUrl/művelet | Az esemény-előfizetés teljes URL-címének beolvasása |
> | Műveletek | Microsoft. EventGrid/eventSubscriptions/Providers/Microsoft. bepillantást/diagnosticSettings/READ | Az esemény-előfizetések diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft. EventGrid/eventSubscriptions/Providers/Microsoft. bepillantások/diagnosticSettings/írás | Az esemény-előfizetések diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft. EventGrid/eventSubscriptions/Providers/Microsoft. bepillantást/metricDefinitions/READ | A eventSubscriptions elérhető metrikáinak beolvasása |
> | Műveletek | Microsoft. EventGrid/eventSubscriptions/READ | EventSubscription beolvasása |
> | Műveletek | Microsoft. EventGrid/eventSubscriptions/Write | EventSubscription létrehozása vagy frissítése |
> | Műveletek | Microsoft. EventGrid/extensionTopics/Providers/Microsoft. bepillantást/diagnosticSettings/READ | A témakörök diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft. EventGrid/extensionTopics/Providers/Microsoft. bepillantások/diagnosticSettings/írás | A témakörök diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft. EventGrid/extensionTopics/Providers/Microsoft. bepillantást/metricDefinitions/READ | A témakörökhöz elérhető metrikák beolvasása |
> | Műveletek | Microsoft. EventGrid/extensionTopics/READ | ExtensionTopic beolvasása. |
> | Műveletek | Microsoft. EventGrid/Locations/eventSubscriptions/READ | Regionális esemény-előfizetések listázása |
> | Műveletek | Microsoft. EventGrid/Locations/operationResults/READ | Regionális művelet eredményének beolvasása |
> | Műveletek | Microsoft. EventGrid/Locations/operationsStatus/READ | Regionális művelet állapotának beolvasása |
> | Műveletek | Microsoft. EventGrid/Locations/topictypes/eventSubscriptions/READ | Regionális esemény-előfizetések listázása topictype szerint |
> | Műveletek | Microsoft. EventGrid/operationResults/READ | Művelet eredményének beolvasása |
> | Műveletek | Microsoft. EventGrid/Operations/READ | EventGrid-műveletek listázása. |
> | Műveletek | Microsoft. EventGrid/operationsStatus/READ | Művelet állapotának beolvasása |
> | Műveletek | Microsoft. EventGrid/regisztráció/művelet | Regisztrálja az előfizetést a EventGrid erőforrás-szolgáltatóhoz. |
> | Műveletek | Microsoft. EventGrid/témakörök/törlés | Témakör törlése |
> | Műveletek | Microsoft. EventGrid/témakörök/Listkeys műveletének beolvasása/művelet | Témakör kulcsainak listázása |
> | Műveletek | Microsoft. EventGrid/témakörök/szolgáltatók/Microsoft. bepillantást/diagnosticSettings/READ | A témakörök diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft. EventGrid/témakörök/szolgáltatók/Microsoft. bepillantások/diagnosticSettings/írás | A témakörök diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft. EventGrid/témakörök/szolgáltatók/Microsoft. bepillantást/logDefinitions/READ | A diagnosztikai naplókhoz való hozzáférés engedélyezése |
> | Műveletek | Microsoft. EventGrid/témakörök/szolgáltatók/Microsoft. bepillantást/metricDefinitions/READ | A témakörökhöz elérhető metrikák beolvasása |
> | Műveletek | Microsoft. EventGrid/témakörök/olvasás | Témakör beolvasása |
> | Műveletek | Microsoft. EventGrid/témakörök/regenerateKey/művelet | Témakör kulcsának újralétrehozása |
> | Műveletek | Microsoft. EventGrid/témakörök/írás | Témakör létrehozása vagy frissítése |
> | Műveletek | Microsoft. EventGrid/topictypes/eventSubscriptions/READ | Globális esemény-előfizetések listázása a témakör típusa szerint |
> | Műveletek | Microsoft. EventGrid/topictypes/eventtypes/READ | Topictype által támogatott eventtypes olvasása |
> | Műveletek | Microsoft. EventGrid/topictypes/READ | Topictype beolvasása |
> | Műveletek | Microsoft. EventGrid/regisztráció/művelet | A EventGrid erőforrás-szolgáltatóhoz tartozó előfizetés regisztrációjának törlése. |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. EventHub/availableClusterRegions/READ | Olvasási művelet az elérhető előre kiépített fürtök Azure-régió általi listázásához. |
> | Műveletek | Microsoft. EventHub/checkNameAvailability/művelet | Checks availability of namespace under given subscription. |
> | Műveletek | Microsoft. EventHub/checkNamespaceAvailability/művelet | Checks availability of namespace under given subscription. This API is deprecated please use CheckNameAvailability instead. |
> | Műveletek | Microsoft. EventHub/fürtök/törlés | Töröl egy meglévő fürterőforrás-erőforrást. |
> | Műveletek | Microsoft. EventHub/fürtök/névterek/olvasás | Névtér Azure Resource Manager azonosítóinak listázása a fürtön belüli névterekhez. |
> | Műveletek | Microsoft. EventHub/fürtök/operationresults/READ | Aszinkron fürt művelet állapotának beolvasása. |
> | Műveletek | Microsoft. EventHub/fürtök/szolgáltatók/Microsoft. bepillantást/metricDefinitions/READ | A fürt metrikáinak erőforrásaihoz tartozó leírások listájának beolvasása |
> | Műveletek | Microsoft. EventHub/fürtök/olvasás | A fürterőforrás leírásának beolvasása |
> | Műveletek | Microsoft. EventHub/fürtök/írás | Létrehoz vagy módosít egy meglévő fürterőforrás-erőforrást. |
> | Műveletek | Microsoft. EventHub/Locations/deleteVirtualNetworkOrSubnets/Action | A megadott VNet tartozó EventHub erőforrás-szolgáltató VNet-szabályainak törlése |
> | Műveletek | Microsoft. EventHub/névterek/engedélyezési szabályok/művelet | Updates Namespace Authorization Rule. This API is deprecated. Please use a PUT call to update the Namespace Authorization Rule instead.. This operation is not supported on API version 2017-04-01. |
> | Műveletek | Microsoft. EventHub/névterek/engedélyezési szabályok/delete | Delete Namespace Authorization Rule. The Default Namespace Authorization Rule cannot be deleted.  |
> | Műveletek | Microsoft. EventHub/névterek/engedélyezési szabályok/listkeys műveletének beolvasása/művelet | Get the Connection String to the Namespace |
> | Műveletek | Microsoft. EventHub/névterek/engedélyezési szabályok/olvasás | Get the list of Namespaces Authorization Rules description. |
> | Műveletek | Microsoft. EventHub/névterek/engedélyezési szabályok/regenerateKeys/művelet | Regenerate the Primary or Secondary key to the Resource |
> | Műveletek | Microsoft. EventHub/névterek/engedélyezési szabályok/írás | Create a Namespace level Authorization Rules and update its properties. The Authorization Rules Access Rights, the Primary and Secondary Keys can be updated. |
> | Műveletek | Microsoft. EventHub/névterek/törlés | Delete Namespace Resource |
> | Műveletek | Microsoft. EventHub/névterek/disasterRecoveryConfigs/engedélyezési szabályok/listkeys műveletének beolvasása/művelet | Gets the authorization rules keys for the Disaster Recovery primary namespace |
> | Műveletek | Microsoft. EventHub/névterek/disasterRecoveryConfigs/engedélyezési szabályok/READ | Get Disaster Recovery Primary Namespace's Authorization Rules |
> | Műveletek | Microsoft. EventHub/névterek/disasterRecoveryConfigs/breakPairing/művelet | Disables Disaster Recovery and stops replicating changes from primary to secondary namespaces. |
> | Műveletek | Microsoft. EventHub/névterek/disasterrecoveryconfigs/checkNameAvailability/művelet | Checks availability of namespace alias under given subscription. |
> | Műveletek | Microsoft. EventHub/névterek/disasterRecoveryConfigs/delete | Deletes the Disaster Recovery configuration associated with the namespace. This operation can only be invoked via the primary namespace. |
> | Műveletek | Microsoft. EventHub/névterek/disasterRecoveryConfigs/feladatátvétel/művelet | Invokes a GEO DR failover and reconfigures the namespace alias to point to the secondary namespace. |
> | Műveletek | Microsoft. EventHub/névterek/disasterRecoveryConfigs/olvasás | Gets the Disaster Recovery configuration associated with the namespace. |
> | Műveletek | Microsoft. EventHub/névterek/disasterRecoveryConfigs/írás | Creates or Updates the Disaster Recovery configuration associated with the namespace. |
> | Műveletek | Microsoft. EventHub/névterek/eventhubs/engedélyezési szabályok/művelet | A EventHub frissítésének művelete. This operation is not supported on API version 2017-04-01. Authorization Rules. Please use a PUT call to update Authorization Rule. |
> | Műveletek | Microsoft. EventHub/névterek/eventhubs/engedélyezési szabályok/delete | EventHub-engedélyezési szabályok törlésének művelete |
> | Műveletek | Microsoft. EventHub/névterek/eventhubs/engedélyezési szabályok/listkeys műveletének beolvasása/művelet | A EventHub tartozó kapcsolódási karakterlánc beolvasása |
> | Műveletek | Microsoft. EventHub/névterek/eventhubs/engedélyezési szabályok/READ |  A EventHub-engedélyezési szabályok listájának beolvasása |
> | Műveletek | Microsoft. EventHub/névterek/eventhubs/engedélyezési szabályok/regenerateKeys/művelet | Regenerate the Primary or Secondary key to the Resource |
> | Műveletek | Microsoft. EventHub/névterek/eventhubs/engedélyezési szabályok/Write | Hozzon létre EventHub engedélyezési szabályokat, és frissítse annak tulajdonságait. The Authorization Rules Access Rights can be updated. |
> | Műveletek | Microsoft. EventHub/névterek/eventHubs/consumergroups/delete | ConsumerGroup-erőforrás törlésére szolgáló művelet |
> | Műveletek | Microsoft. EventHub/névterek/eventHubs/consumergroups/READ | ConsumerGroup-erőforrás leírásának beolvasása |
> | Műveletek | Microsoft.EventHub/namespaces/eventHubs/consumergroups/write | Create or Update ConsumerGroup properties. |
> | Műveletek | Microsoft.EventHub/namespaces/eventhubs/Delete | Operation to delete EventHub Resource |
> | Műveletek | Microsoft.EventHub/namespaces/eventhubs/read | Get list of EventHub Resource Descriptions |
> | Műveletek | Microsoft.EventHub/namespaces/eventhubs/write | Create or Update EventHub properties. |
> | Műveletek | Microsoft.EventHub/namespaces/ipFilterRules/delete | Delete IP Filter Resource |
> | Műveletek | Microsoft.EventHub/namespaces/ipFilterRules/read | Get IP Filter Resource |
> | Műveletek | Microsoft.EventHub/namespaces/ipFilterRules/write | Create IP Filter Resource |
> | DataAction | Microsoft.EventHub/namespaces/messages/receive/action | Üzenetek fogadása |
> | DataAction | Microsoft.EventHub/namespaces/messages/send/action | Üzenetek küldése |
> | Műveletek | Microsoft.EventHub/namespaces/messagingPlan/read | Gets the Messaging Plan for a namespace.<br>This API is deprecated.<br>Properties exposed via the MessagingPlan resource are moved to the (parent) Namespace resource in later API versions..<br>This operation is not supported on API version 2017-04-01. |
> | Műveletek | Microsoft.EventHub/namespaces/messagingPlan/write | Updates the Messaging Plan for a namespace.<br>This API is deprecated.<br>Properties exposed via the MessagingPlan resource are moved to the (parent) Namespace resource in later API versions..<br>This operation is not supported on API version 2017-04-01. |
> | Műveletek | Microsoft.EventHub/namespaces/networkruleset/delete | Delete VNET Rule Resource |
> | Műveletek | Microsoft.EventHub/namespaces/networkruleset/read | Gets NetworkRuleSet Resource |
> | Műveletek | Microsoft.EventHub/namespaces/networkruleset/write | Create VNET Rule Resource |
> | Műveletek | Microsoft.EventHub/namespaces/networkrulesets/delete | Delete VNET Rule Resource |
> | Műveletek | Microsoft.EventHub/namespaces/networkrulesets/read | Gets NetworkRuleSet Resource |
> | Műveletek | Microsoft.EventHub/namespaces/networkrulesets/write | Create VNET Rule Resource |
> | Műveletek | Microsoft.EventHub/namespaces/operationresults/read | Get the status of Namespace operation |
> | Műveletek | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Get list of Namespace diagnostic settings Resource Descriptions |
> | Műveletek | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Get list of Namespace diagnostic settings Resource Descriptions |
> | Műveletek | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/logDefinitions/read | Get list of Namespace logs Resource Descriptions |
> | Műveletek | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Get list of Namespace metrics Resource Descriptions |
> | Műveletek | Microsoft.EventHub/namespaces/read | Get the list of Namespace Resource Description |
> | Műveletek | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | Remove ACS namespace |
> | Műveletek | Microsoft.EventHub/namespaces/virtualNetworkRules/delete | Delete VNET Rule Resource |
> | Műveletek | Microsoft.EventHub/namespaces/virtualNetworkRules/read | Gets VNET Rule Resource |
> | Műveletek | Microsoft.EventHub/namespaces/virtualNetworkRules/write | Create VNET Rule Resource |
> | Műveletek | Microsoft.EventHub/namespaces/write | Create a Namespace Resource and Update its properties. Tags and Capacity of the Namespace are the properties which can be updated. |
> | Műveletek | Microsoft.EventHub/operations/read | Műveletek beolvasása |
> | Műveletek | Microsoft.EventHub/register/action | Registers the subscription for the EventHub resource provider and enables the creation of EventHub resources |
> | Műveletek | Microsoft.EventHub/sku/read | SKU-erőforrások leírásának beolvasása |
> | Műveletek | Microsoft.EventHub/sku/regions/read | SkuRegions-erőforrás leírásának beolvasása |
> | Műveletek | Microsoft.EventHub/unregister/action | Registers the EventHub Resource Provider |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Features/features/read | Gets the features of a subscription. |
> | Műveletek | Microsoft.Features/operations/read | Gets the list of operations. |
> | Műveletek | Microsoft.Features/providers/features/read | Gets the feature of a subscription in a given resource provider. |
> | Műveletek | Microsoft.Features/providers/features/register/action | Registers the feature for a subscription in a given resource provider. |
> | Műveletek | Microsoft.Features/providers/features/unregister/action | Unregisters the feature for a subscription in a given resource provider. |
> | Műveletek | Microsoft.Features/register/action | Registers the feature of a subscription. |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.GuestConfiguration/guestConfigurationAssignments/delete | Delete guest configuration assignment. |
> | Műveletek | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | Get guest configuration assignment. |
> | Műveletek | Microsoft.GuestConfiguration/guestConfigurationAssignments/reports/read | Get guest configuration assignment report. |
> | Műveletek | Microsoft.GuestConfiguration/guestConfigurationAssignments/write | Create new guest configuration assignment. |
> | Műveletek | Microsoft.GuestConfiguration/operations/read | Gets the operations for the Microsoft.GuestConfiguration resource provider |
> | Műveletek | Microsoft.GuestConfiguration/register/action | Registers the subscription for the Microsoft.GuestConfiguration resource provider. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.HDInsight/clusters/applications/delete | Delete Application for HDInsight Cluster |
> | Műveletek | Microsoft.HDInsight/clusters/applications/read | Get Application for HDInsight Cluster |
> | Műveletek | Microsoft.HDInsight/clusters/applications/write | Create or Update Application for HDInsight Cluster |
> | Műveletek | Microsoft.HDInsight/clusters/changerdpsetting/action | Change RDP setting for HDInsight Cluster |
> | Műveletek | Microsoft.HDInsight/clusters/configurations/action | Update HDInsight Cluster Configuration |
> | Műveletek | Microsoft.HDInsight/clusters/configurations/action | Get HDInsight Cluster Configurations |
> | Műveletek | Microsoft.HDInsight/clusters/configurations/read | Get HDInsight Cluster Configurations |
> | Műveletek | Microsoft.HDInsight/clusters/delete | Delete a HDInsight Cluster |
> | Műveletek | Microsoft.HDInsight/clusters/extensions/delete | Delete Cluster Extension for HDInsight Cluster |
> | Műveletek | Microsoft.HDInsight/clusters/extensions/read | Get Cluster Extension for HDInsight Cluster |
> | Műveletek | Microsoft.HDInsight/clusters/extensions/write | Create Cluster Extension for HDInsight Cluster |
> | Műveletek | Microsoft.HDInsight/clusters/getGatewaySettings/action | Get gateway settings for HDInsight Cluster |
> | Műveletek | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/read | Gets the diagnostic setting for the resource HDInsight Cluster |
> | Műveletek | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/write | Creates or updates the diagnostic setting for the resource HDInsight Cluster |
> | Műveletek | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/metricDefinitions/read | Gets the available metrics for HDInsight Cluster |
> | Műveletek | Microsoft.HDInsight/clusters/read | Get details about HDInsight Cluster |
> | Műveletek | Microsoft.HDInsight/clusters/roles/resize/action | Resize a HDInsight Cluster |
> | Műveletek | Microsoft.HDInsight/clusters/updateGatewaySettings/action | Update gateway settings for HDInsight Cluster |
> | Műveletek | Microsoft.HDInsight/clusters/write | Create or Update HDInsight Cluster |
> | Műveletek | Microsoft.HDInsight/locations/capabilities/read | Get Subscription Capabilities |
> | Műveletek | Microsoft.HDInsight/locations/checkNameAvailability/read | Check Name Availability |
> | Műveletek | Microsoft.HDInsight/register/action | Register HDInsight resource provider for the subscription |
> | Műveletek | Microsoft.HDInsight/unregister/action | Unregister HDInsight resource provider for the subscription |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ImportExport/jobs/delete | Deletes an existing job. |
> | Műveletek | Microsoft.ImportExport/jobs/listBitLockerKeys/action | Gets the BitLocker keys for the specified job. |
> | Műveletek | Microsoft.ImportExport/jobs/read | Gets the properties for the specified job or returns the list of jobs. |
> | Műveletek | Microsoft.ImportExport/jobs/write | Creates a job with the specified parameters or update the properties or tags for the specified job. |
> | Műveletek | Microsoft.ImportExport/locations/read | Gets the properties for the specified location or returns the list of locations. |
> | Műveletek | Microsoft.ImportExport/operations/read | Gets the operations supported by the Resource Provider. |
> | Műveletek | Microsoft.ImportExport/register/action | Registers the subscription for the import/export resource provider and enables the creation of import/export jobs. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Insights/ActionGroups/Delete | Delete an action group |
> | Műveletek | Microsoft.Insights/ActionGroups/Read | Read an action group |
> | Műveletek | Microsoft.Insights/ActionGroups/Write | Create or update an action group |
> | Műveletek | Microsoft.Insights/ActivityLogAlerts/Activated/Action | Activity Log Alert activated |
> | Műveletek | Microsoft.Insights/ActivityLogAlerts/Delete | Delete an activity log alert |
> | Műveletek | Microsoft.Insights/ActivityLogAlerts/Read | Read an activity log alert |
> | Műveletek | Microsoft.Insights/ActivityLogAlerts/Write | Create or update an activity log alert |
> | Műveletek | Microsoft.Insights/AlertRules/Activated/Action | Classic metric alert activated |
> | Műveletek | Microsoft.Insights/AlertRules/Delete | Delete a classic metric alert |
> | Műveletek | Microsoft.Insights/AlertRules/Incidents/Read | Read a classic metric alert incident |
> | Műveletek | Microsoft.Insights/AlertRules/Read | Read a classic metric alert |
> | Műveletek | Microsoft.Insights/AlertRules/Resolved/Action | Classic metric alert resolved |
> | Műveletek | Microsoft.Insights/AlertRules/Throttled/Action | Classic metric alert rule throttled |
> | Műveletek | Microsoft.Insights/AlertRules/Write | Create or update a classic metric alert |
> | Műveletek | Microsoft.Insights/AutoscaleSettings/Delete | Delete an autoscale setting |
> | Műveletek | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Read | Erőforrás diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Write | Erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/logDefinitions/Read | Napló-definíciók olvasása |
> | Műveletek | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | Read metric definitions |
> | Műveletek | Microsoft.Insights/AutoscaleSettings/Read | Read an autoscale setting |
> | Műveletek | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | Autoscale scale down initiated |
> | Műveletek | Microsoft.Insights/AutoscaleSettings/ScaledownResult/Action | Autoscale scale down completed |
> | Műveletek | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | Autoscale scale up initiated |
> | Műveletek | Microsoft.Insights/AutoscaleSettings/ScaleupResult/Action | Autoscale scale up completed |
> | Műveletek | Microsoft.Insights/AutoscaleSettings/Write | Create or update an autoscale setting |
> | Műveletek | Microsoft.Insights/Baseline/Read | Read a metric baseline (preview) |
> | Műveletek | Microsoft.Insights/CalculateBaseline/Read | Calculate baseline for metric values (preview) |
> | Műveletek | Microsoft.Insights/Components/AnalyticsItems/Delete | Deleting an Application Insights analytics item |
> | Műveletek | Microsoft.Insights/Components/AnalyticsItems/Read | Reading an Application Insights analytics item |
> | Műveletek | Microsoft.Insights/Components/AnalyticsItems/Write | Application Insights Analytics-elemek írása |
> | Műveletek | Microsoft. bepillantások/összetevők/AnalyticsTables/művelet | Application Insights Analytics-tábla művelet |
> | Műveletek | Microsoft. bepillantások/összetevők/AnalyticsTables/törlés | Application Insights Analytics-tábla sémájának törlése |
> | Műveletek | Microsoft. bepillantások/összetevők/AnalyticsTables/olvasás | Application Insights Analytics-táblázat sémájának olvasása |
> | Műveletek | Microsoft. bepillantások/összetevők/AnalyticsTables/írás | Application Insights Analytics-táblázat sémájának írása |
> | Műveletek | Microsoft. bepillantások/összetevők/jegyzetek/törlés | Application Insights jegyzet törlése |
> | Műveletek | Microsoft. bepillantások/összetevők/jegyzetek/olvasás | Application Insights jegyzet beolvasása |
> | Műveletek | Microsoft. bepillantást/összetevők/jegyzetek/írás | Application Insights Jegyzet írása |
> | Műveletek | Microsoft. bepillantások/összetevők/API/olvasás | Az Application Insights Component adatapi olvasása |
> | Műveletek | Microsoft. bepillantások/összetevők/ApiKeys/művelet | Application Insights API-kulcs létrehozása |
> | Műveletek | Microsoft. bepillantások/összetevők/ApiKeys/törlés | Application Insights API-kulcs törlése |
> | Műveletek | Microsoft. bepillantások/összetevők/ApiKeys/olvasás | Application Insights API-kulcs olvasása |
> | Műveletek | Microsoft. bepillantások/összetevők/BillingPlanForComponent/olvasás | Application Insights-összetevő számlázási tervének beolvasása |
> | Műveletek | Microsoft. bepillantások/összetevők/CurrentBillingFeatures/olvasás | Application Insights összetevő aktuális számlázási funkcióinak olvasása |
> | Műveletek | Microsoft. bepillantások/összetevők/CurrentBillingFeatures/írás | Application Insights összetevő aktuális számlázási funkcióinak írása |
> | Műveletek | Microsoft. bepillantások/összetevők/DailyCapReached/művelet | Elérte a Application Insights-összetevő napi korlátját |
> | Műveletek | Microsoft. bepillantások/összetevők/DailyCapWarningThresholdReached/művelet | Elérte a Application Insights összetevő napi korlátjának figyelmeztetési küszöbértékét |
> | Műveletek | Microsoft. bepillantások/összetevők/DefaultWorkItemConfig/olvasás | Application Insights alapértelmezett ALM-integráció konfigurációjának olvasása |
> | Műveletek | Microsoft. bepillantások/összetevők/törlés | Application ininsight-összetevő konfigurációjának törlése |
> | Műveletek | Microsoft. bepillantások/összetevők/események/olvasás | Naplók beolvasása Application Insights OData-lekérdezési formátum használatával |
> | Műveletek | Microsoft. bepillantások/összetevők/ExportConfiguration/művelet | Application Insights exportálási beállítások művelet |
> | Műveletek | Microsoft. bepillantások/összetevők/ExportConfiguration/törlés | Application Insights exportálási beállítások törlése |
> | Műveletek | Microsoft. bepillantások/összetevők/ExportConfiguration/olvasás | Application Insights exportálási beállítások olvasása |
> | Műveletek | Microsoft. bepillantások/összetevők/ExportConfiguration/írás | Application Insights exportálási beállítások írása |
> | Műveletek | Microsoft. bepillantások/összetevők/ExtendQueries/olvasás | Application Insights összetevő bővített lekérdezési eredményeinek olvasása |
> | Műveletek | Microsoft. bepillantások/összetevők/Kedvencek/törlés | Application Insights kedvenc törlése |
> | Műveletek | Microsoft. bepillantások/összetevők/Kedvencek/olvasás | Application Insights kedvenc olvasása |
> | Műveletek | Microsoft. bepillantások/összetevők/Kedvencek/írás | Application Insights kedvenc írása |
> | Műveletek | Microsoft. bepillantások/összetevők/FeatureCapabilities/olvasás | Application Insights összetevő-szolgáltatás képességeinek olvasása |
> | Műveletek | Microsoft. bepillantások/összetevők/GetAvailableBillingFeatures/olvasás | Application Insights összetevő elérhető számlázási funkcióinak olvasása |
> | Műveletek | Microsoft. bepillantások/összetevők/GetToken/olvasás | Application Insights összetevő-jogkivonat olvasása |
> | Műveletek | Microsoft. bepillantások/összetevők/MetricDefinitions/olvasás | Application Insights összetevő metrikájának definícióinak olvasása |
> | Műveletek | Microsoft. bepillantások/összetevők/mérőszámok/olvasás | Application Insights-összetevő metrikáinak olvasása |
> | Műveletek | Microsoft. bepillantások/összetevők/áthelyezés/művelet | Application Insights-összetevő áthelyezése másik erőforráscsoporthoz vagy előfizetésbe |
> | Műveletek | Microsoft. bepillantások/összetevők/MyAnalyticsItems/törlés | Application Insights személyes elemzési elemek törlése |
> | Műveletek | Microsoft. bepillantások/összetevők/MyAnalyticsItems/olvasás | Application Insights személyes elemzési elemek olvasása |
> | Műveletek | Microsoft. bepillantások/összetevők/MyAnalyticsItems/írás | Application Insights személyes elemzési elemek írása |
> | Műveletek | Microsoft. bepillantások/összetevők/MyFavorites/olvasás | Application Insights személyes kedvenc olvasása |
> | Műveletek | Microsoft. bepillantások/összetevők/műveletek/olvasás | A hosszan futó műveletek állapotának lekérése Application Insights |
> | Műveletek | Microsoft. bepillantások/összetevők/PricingPlans/olvasás | Application Insights összetevő árképzési tervének olvasása |
> | Műveletek | Microsoft. bepillantások/összetevők/PricingPlans/írás | Application Insights összetevő árképzési tervének írása |
> | Műveletek | Microsoft. bepillantások/összetevők/ProactiveDetectionConfigs/olvasás | Application Insights proaktív észlelési konfiguráció olvasása |
> | Műveletek | Microsoft. bepillantások/összetevők/ProactiveDetectionConfigs/írás | Application Insights proaktív észlelési konfiguráció írása |
> | Műveletek | Microsoft. betekintő/összetevők/szolgáltatók/Microsoft. bepillantást/MetricDefinitions/READ | Read metric definitions |
> | Műveletek | Microsoft. bepillantások/összetevők/kiürítés/művelet | Adatok törlése a Application Insightsból |
> | Műveletek | Microsoft. bepillantások/összetevők/lekérdezés/olvasás | Lekérdezések futtatása Application Insights naplókon |
> | Műveletek | Microsoft. bepillantások/összetevők/QuotaStatus/olvasás | Application Insights összetevő-kvóta állapotának olvasása |
> | Műveletek | Microsoft. bepillantások/összetevők/olvasás | Application betekintő összetevő konfigurációjának olvasása |
> | Műveletek | Microsoft. bepillantások/összetevők/SyntheticMonitorLocations/olvasás | Application Insights webteszt helyeinek olvasása |
> | Műveletek | Microsoft. bepillantások/összetevők/webtesztek/olvasás | Reading a webtest configuration |
> | Műveletek | Microsoft. bepillantások/összetevők/WorkItemConfigs/törlés | Application Insights ALM-integráció konfigurációjának törlése |
> | Műveletek | Microsoft. bepillantások/összetevők/WorkItemConfigs/olvasás | Application Insights ALM-integrációs konfiguráció olvasása |
> | Műveletek | Microsoft. bepillantások/összetevők/WorkItemConfigs/írás | Application Insights ALM-integráció konfigurációjának írása |
> | Műveletek | Microsoft. bepillantások/összetevők/írás | Alkalmazás-ellenőrzési összetevők konfigurációjának írása |
> | Műveletek | Microsoft. bepillantást/DataCollectionRuleAssociations/delete | Erőforrás társításának törlése adatgyűjtési szabállyal |
> | Műveletek | Microsoft. bepillantások/DataCollectionRuleAssociations/olvasás | Erőforrás társításának beolvasása egy adatgyűjtési szabállyal |
> | Műveletek | Microsoft. bepillantások/DataCollectionRuleAssociations/írás | Erőforrás társításának létrehozása vagy frissítése adatgyűjtési szabállyal |
> | DataAction | Microsoft. elemzések/DataCollectionRules/adatfeldolgozás/írás | Az adatgyűjtési szabályba való adatküldés |
> | Műveletek | Microsoft. bepillantást/DataCollectionRules/delete | Adatgyűjtési szabály törlése |
> | Műveletek | Microsoft. bepillantások/DataCollectionRules/olvasás | Adatgyűjtés szabályának beolvasása |
> | Műveletek | Microsoft. bepillantások/DataCollectionRules/írás | Adatgyűjtés szabályának létrehozása vagy frissítése |
> | Műveletek | Microsoft. bepillantást/DiagnosticSettings/delete | Erőforrás diagnosztikai beállításának törlése |
> | Műveletek | Microsoft. bepillantások/DiagnosticSettings/olvasás | Erőforrás diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft. bepillantások/DiagnosticSettings/írás | Erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft. bepillantások/EventCategories/olvasás | Beolvasható tevékenység naplójának eseményeinek kategóriái |
> | Műveletek | Microsoft. bepillantások/eventtypes/digestevents/olvasás | Olvasási felügyeleti eseménytípus kivonata |
> | Műveletek | Microsoft. bepillantások/eventtypes/értékek/olvasás | Tevékenység-naplózási események olvasása |
> | Műveletek | Microsoft. bepillantást/ExtendedDiagnosticSettings/delete | Hálózati adatfolyam-napló diagnosztikai beállításának törlése |
> | Műveletek | Microsoft. bepillantások/ExtendedDiagnosticSettings/olvasás | Hálózati adatfolyam-napló diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft. bepillantások/ExtendedDiagnosticSettings/írás | Hálózati adatfolyam-napló diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft. bepillantások/ListMigrationDate/művelet | Előfizetés áttelepítési dátumának visszaolvasása |
> | Műveletek | Microsoft. bepillantások/ListMigrationDate/olvasás | Előfizetés áttelepítési dátumának visszaolvasása |
> | Műveletek | Microsoft. bepillantások/LogDefinitions/olvasás | Napló-definíciók olvasása |
> | Műveletek | Microsoft. bepillantást/LogProfiles/delete | Műveletnapló-profil törlése |
> | Műveletek | Microsoft. bepillantások/LogProfiles/olvasás | Tevékenységi napló naplójának profiljának olvasása |
> | Műveletek | Microsoft. bepillantások/LogProfiles/írás | Műveletnapló-napló létrehozása vagy frissítése |
> | Műveletek | Microsoft. bepillantások/naplók/ADAssessmentRecommendation/olvasás | Read data from the ADAssessmentRecommendation table |
> | Műveletek | Microsoft. bepillantások/naplók/ADReplicationResult/olvasás | Read data from the ADReplicationResult table |
> | Műveletek | Microsoft. bepillantások/naplók/ADSecurityAssessmentRecommendation/olvasás | Read data from the ADSecurityAssessmentRecommendation table |
> | Műveletek | Microsoft. bepillantások/naplók/riasztás/olvasás | Read data from the Alert table |
> | Műveletek | Microsoft. bepillantások/naplók/AlertHistory/olvasás | Read data from the AlertHistory table |
> | Műveletek | Microsoft. bepillantások/naplók/ApplicationInsights/olvasás | Read data from the ApplicationInsights table |
> | Műveletek | Microsoft. bepillantások/naplók/AzureActivity/olvasás | Read data from the AzureActivity table |
> | Műveletek | Microsoft. bepillantások/naplók/AzureMetrics/olvasás | Read data from the AzureMetrics table |
> | Műveletek | Microsoft. bepillantások/naplók/BoundPort/olvasás | Read data from the BoundPort table |
> | Műveletek | Microsoft. bepillantások/naplók/CommonSecurityLog/olvasás | Read data from the CommonSecurityLog table |
> | Műveletek | Microsoft. bepillantások/naplók/ComputerGroup/olvasás | Read data from the ComputerGroup table |
> | Műveletek | Microsoft. bepillantások/naplók/konfigurációváltozás/olvasás | Read data from the ConfigurationChange table |
> | Műveletek | Microsoft. bepillantások/naplók/ConfigurationData/olvasás | Read data from the ConfigurationData table |
> | Műveletek | Microsoft. bepillantások/naplók/ContainerImageInventory/olvasás | Read data from the ContainerImageInventory table |
> | Műveletek | Microsoft. bepillantások/naplók/ContainerInventory/olvasás | Read data from the ContainerInventory table |
> | Műveletek | Microsoft. bepillantások/naplók/ContainerLog/olvasás | Read data from the ContainerLog table |
> | Műveletek | Microsoft. bepillantások/naplók/ContainerServiceLog/olvasás | Read data from the ContainerServiceLog table |
> | Műveletek | Microsoft. bepillantások/naplók/DeviceAppCrash/olvasás | Read data from the DeviceAppCrash table |
> | Műveletek | Microsoft. bepillantások/naplók/DeviceAppLaunch/olvasás | Read data from the DeviceAppLaunch table |
> | Műveletek | Microsoft. bepillantások/naplók/DeviceCalendar/olvasás | Read data from the DeviceCalendar table |
> | Műveletek | Microsoft. bepillantások/naplók/DeviceCleanup/olvasás | Read data from the DeviceCleanup table |
> | Műveletek | Microsoft. bepillantások/naplók/DeviceConnectSession/olvasás | Read data from the DeviceConnectSession table |
> | Műveletek | Microsoft. bepillantások/naplók/DeviceEtw/olvasás | Read data from the DeviceEtw table |
> | Műveletek | Microsoft. bepillantások/naplók/DeviceHardwareHealth/olvasás | Read data from the DeviceHardwareHealth table |
> | Műveletek | Microsoft. bepillantások/naplók/DeviceHealth/olvasás | Read data from the DeviceHealth table |
> | Műveletek | Microsoft. bepillantások/naplók/DeviceHeartbeat/olvasás | Read data from the DeviceHeartbeat table |
> | Műveletek | Microsoft. bepillantások/naplók/DeviceSkypeHeartbeat/olvasás | Read data from the DeviceSkypeHeartbeat table |
> | Műveletek | Microsoft. bepillantások/naplók/DeviceSkypeSignIn/olvasás | Read data from the DeviceSkypeSignIn table |
> | Műveletek | Microsoft. bepillantások/naplók/DeviceSleepState/olvasás | Read data from the DeviceSleepState table |
> | Műveletek | Microsoft. bepillantások/naplók/DHAppFailure/olvasás | Read data from the DHAppFailure table |
> | Műveletek | Microsoft. bepillantások/naplók/DHAppReliability/olvasás | Read data from the DHAppReliability table |
> | Műveletek | Microsoft. bepillantások/naplók/DHDriverReliability/olvasás | Read data from the DHDriverReliability table |
> | Műveletek | Microsoft. bepillantások/naplók/DHLogonFailures/olvasás | Read data from the DHLogonFailures table |
> | Műveletek | Microsoft. bepillantások/naplók/DHLogonMetrics/olvasás | Read data from the DHLogonMetrics table |
> | Műveletek | Microsoft. bepillantások/naplók/DHOSCrashData/olvasás | Read data from the DHOSCrashData table |
> | Műveletek | Microsoft. bepillantások/naplók/DHOSReliability/olvasás | Read data from the DHOSReliability table |
> | Műveletek | Microsoft. bepillantások/naplók/DHWipAppLearning/olvasás | Read data from the DHWipAppLearning table |
> | Műveletek | Microsoft. bepillantások/naplók/DnsEvents/olvasás | Read data from the DnsEvents table |
> | Műveletek | Microsoft. bepillantások/naplók/DnsInventory/olvasás | Read data from the DnsInventory table |
> | Műveletek | Microsoft. bepillantások/naplók/ETWEvent/olvasás | Read data from the ETWEvent table |
> | Műveletek | Microsoft. elemzések/naplók/esemény/olvasás | Read data from the Event table |
> | Műveletek | Microsoft. bepillantások/naplók/ExchangeAssessmentRecommendation/olvasás | Read data from the ExchangeAssessmentRecommendation table |
> | Műveletek | Microsoft. bepillantások/naplók/ExchangeOnlineAssessmentRecommendation/olvasás | Read data from the ExchangeOnlineAssessmentRecommendation table |
> | Műveletek | Microsoft. bepillantások/naplók/szívverés/olvasás | Read data from the Heartbeat table |
> | Műveletek | Microsoft. bepillantások/naplók/IISAssessmentRecommendation/olvasás | Read data from the IISAssessmentRecommendation table |
> | Műveletek | Microsoft. bepillantások/naplók/InboundConnection/olvasás | Read data from the InboundConnection table |
> | Műveletek | Microsoft. bepillantások/naplók/KubeNodeInventory/olvasás | Read data from the KubeNodeInventory table |
> | Műveletek | Microsoft. bepillantások/naplók/KubePodInventory/olvasás | Read data from the KubePodInventory table |
> | Műveletek | Microsoft. bepillantások/naplók/LinuxAuditLog/olvasás | Read data from the LinuxAuditLog table |
> | Műveletek | Microsoft. bepillantások/naplók/MAApplication/olvasás | Read data from the MAApplication table |
> | Műveletek | Microsoft. bepillantások/naplók/MAApplicationHealth/olvasás | Read data from the MAApplicationHealth table |
> | Műveletek | Microsoft. bepillantások/naplók/MAApplicationHealthAlternativeVersions/olvasás | Read data from the MAApplicationHealthAlternativeVersions table |
> | Műveletek | Microsoft. bepillantások/naplók/MAApplicationHealthIssues/olvasás | Read data from the MAApplicationHealthIssues table |
> | Műveletek | Microsoft. bepillantások/naplók/MAApplicationInstance/olvasás | Read data from the MAApplicationInstance table |
> | Műveletek | Microsoft. bepillantások/naplók/MAApplicationInstanceReadiness/olvasás | Read data from the MAApplicationInstanceReadiness table |
> | Műveletek | Microsoft. bepillantások/naplók/MAApplicationReadiness/olvasás | Read data from the MAApplicationReadiness table |
> | Műveletek | Microsoft. bepillantások/naplók/MADeploymentPlan/olvasás | Read data from the MADeploymentPlan table |
> | Műveletek | Microsoft. bepillantások/naplók/MADevice/olvasás | Read data from the MADevice table |
> | Műveletek | Microsoft. bepillantások/naplók/MADevicePnPHealth/olvasás | Read data from the MADevicePnPHealth table |
> | Műveletek | Microsoft. bepillantások/naplók/MADevicePnPHealthAlternativeVersions/olvasás | Read data from the MADevicePnPHealthAlternativeVersions table |
> | Műveletek | Microsoft. bepillantások/naplók/MADevicePnPHealthIssues/olvasás | Read data from the MADevicePnPHealthIssues table |
> | Műveletek | Microsoft. bepillantások/naplók/MADeviceReadiness/olvasás | Read data from the MADeviceReadiness table |
> | Műveletek | Microsoft. bepillantások/naplók/MADriverInstanceReadiness/olvasás | Read data from the MADriverInstanceReadiness table |
> | Műveletek | Microsoft. bepillantások/naplók/MADriverReadiness/olvasás | Read data from the MADriverReadiness table |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeAddin/olvasás | Read data from the MAOfficeAddin table |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeAddinHealth/olvasás | Read data from the MAOfficeAddinHealth table |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeAddinHealthIssues/olvasás | Read data from the MAOfficeAddinHealthIssues table |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeAddinInstance/olvasás | Read data from the MAOfficeAddinInstance table |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeAddinInstanceReadiness/olvasás | Read data from the MAOfficeAddinInstanceReadiness table |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeAddinReadiness/olvasás | Read data from the MAOfficeAddinReadiness table |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeApp/olvasás | Read data from the MAOfficeApp table |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeAppHealth/olvasás | Read data from the MAOfficeAppHealth table |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeAppInstance/olvasás | Adatok beolvasása a MAOfficeAppInstance táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeAppReadiness/olvasás | Adatok beolvasása a MAOfficeAppReadiness táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeBuildInfo/olvasás | Adatok beolvasása a MAOfficeBuildInfo táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeCurrencyAssessment/olvasás | Adatok beolvasása a MAOfficeCurrencyAssessment táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeCurrencyAssessmentDailyCounts/olvasás | Adatok beolvasása a MAOfficeCurrencyAssessmentDailyCounts táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeDeploymentStatus/olvasás | Adatok beolvasása a MAOfficeDeploymentStatus táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeMacroHealth/olvasás | Adatok beolvasása a MAOfficeMacroHealth táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeMacroHealthIssues/olvasás | Adatok beolvasása a MAOfficeMacroHealthIssues táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeMacroIssueInstanceReadiness/olvasás | Adatok beolvasása a MAOfficeMacroIssueInstanceReadiness táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeMacroIssueReadiness/olvasás | Adatok beolvasása a MAOfficeMacroIssueReadiness táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeMacroSummary/olvasás | Adatok beolvasása a MAOfficeMacroSummary táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeSuite/olvasás | Adatok beolvasása a MAOfficeSuite táblából |
> | Műveletek | Microsoft.Insights/Logs/MAOfficeSuiteInstance/Read | Adatok beolvasása a MAOfficeSuiteInstance táblából |
> | Műveletek | Microsoft.Insights/Logs/MAProposedPilotDevices/Read | Adatok beolvasása a MAProposedPilotDevices táblából |
> | Műveletek | Microsoft.Insights/Logs/MAWindowsBuildInfo/Read | Adatok beolvasása a MAWindowsBuildInfo táblából |
> | Műveletek | Microsoft.Insights/Logs/MAWindowsCurrencyAssessment/Read | Adatok beolvasása a MAWindowsCurrencyAssessment táblából |
> | Műveletek | Microsoft.Insights/Logs/MAWindowsCurrencyAssessmentDailyCounts/Read | Adatok beolvasása a MAWindowsCurrencyAssessmentDailyCounts táblából |
> | Műveletek | Microsoft.Insights/Logs/MAWindowsDeploymentStatus/Read | Adatok beolvasása a MAWindowsDeploymentStatus táblából |
> | Műveletek | Microsoft.Insights/Logs/MAWindowsSysReqInstanceReadiness/Read | Adatok beolvasása a MAWindowsSysReqInstanceReadiness táblából |
> | Műveletek | Microsoft.Insights/Logs/NetworkMonitoring/Read | Adatok beolvasása a NetworkMonitoring táblából |
> | Műveletek | Microsoft.Insights/Logs/OfficeActivity/Read | Adatok beolvasása a OfficeActivity táblából |
> | Műveletek | Microsoft.Insights/Logs/Operation/Read | Adatok beolvasása a műveleti táblából |
> | Műveletek | Microsoft.Insights/Logs/OutboundConnection/Read | Adatok beolvasása a OutboundConnection táblából |
> | Műveletek | Microsoft.Insights/Logs/Perf/Read | Adatok beolvasása a Perf táblából |
> | Műveletek | Microsoft.Insights/Logs/ProtectionStatus/Read | Adatok beolvasása a ProtectionStatus táblából |
> | Műveletek | Microsoft.Insights/Logs/Read | Reading data from all your logs |
> | Műveletek | Microsoft.Insights/Logs/ReservedAzureCommonFields/Read | Read data from the ReservedAzureCommonFields table |
> | Műveletek | Microsoft.Insights/Logs/ReservedCommonFields/Read | Read data from the ReservedCommonFields table |
> | Műveletek | Microsoft.Insights/Logs/SCCMAssessmentRecommendation/Read | Read data from the SCCMAssessmentRecommendation table |
> | Műveletek | Microsoft.Insights/Logs/SCOMAssessmentRecommendation/Read | Read data from the SCOMAssessmentRecommendation table |
> | Műveletek | Microsoft.Insights/Logs/SecurityAlert/Read | Read data from the SecurityAlert table |
> | Műveletek | Microsoft.Insights/Logs/SecurityBaseline/Read | Read data from the SecurityBaseline table |
> | Műveletek | Microsoft.Insights/Logs/SecurityBaselineSummary/Read | Read data from the SecurityBaselineSummary table |
> | Műveletek | Microsoft.Insights/Logs/SecurityDetection/Read | Read data from the SecurityDetection table |
> | Műveletek | Microsoft.Insights/Logs/SecurityEvent/Read | Read data from the SecurityEvent table |
> | Műveletek | Microsoft.Insights/Logs/ServiceFabricOperationalEvent/Read | Read data from the ServiceFabricOperationalEvent table |
> | Műveletek | Microsoft.Insights/Logs/ServiceFabricReliableActorEvent/Read | Read data from the ServiceFabricReliableActorEvent table |
> | Műveletek | Microsoft.Insights/Logs/ServiceFabricReliableServiceEvent/Read | Read data from the ServiceFabricReliableServiceEvent table |
> | Műveletek | Microsoft.Insights/Logs/SfBAssessmentRecommendation/Read | Read data from the SfBAssessmentRecommendation table |
> | Műveletek | Microsoft.Insights/Logs/SfBOnlineAssessmentRecommendation/Read | Read data from the SfBOnlineAssessmentRecommendation table |
> | Műveletek | Microsoft.Insights/Logs/SharePointOnlineAssessmentRecommendation/Read | Read data from the SharePointOnlineAssessmentRecommendation table |
> | Műveletek | Microsoft.Insights/Logs/SPAssessmentRecommendation/Read | Read data from the SPAssessmentRecommendation table |
> | Műveletek | Microsoft.Insights/Logs/SQLAssessmentRecommendation/Read | Read data from the SQLAssessmentRecommendation table |
> | Műveletek | Microsoft.Insights/Logs/SQLQueryPerformance/Read | Read data from the SQLQueryPerformance table |
> | Műveletek | Microsoft.Insights/Logs/Syslog/Read | Read data from the Syslog table |
> | Műveletek | Microsoft.Insights/Logs/SysmonEvent/Read | Read data from the SysmonEvent table |
> | Műveletek | Microsoft.Insights/Logs/Tables.Custom/Read | Reading data from any custom log |
> | Műveletek | Microsoft.Insights/Logs/UAApp/Read | Read data from the UAApp table |
> | Műveletek | Microsoft.Insights/Logs/UAComputer/Read | Read data from the UAComputer table |
> | Műveletek | Microsoft.Insights/Logs/UAComputerRank/Read | Read data from the UAComputerRank table |
> | Műveletek | Microsoft.Insights/Logs/UADriver/Read | Read data from the UADriver table |
> | Műveletek | Microsoft.Insights/Logs/UADriverProblemCodes/Read | Read data from the UADriverProblemCodes table |
> | Műveletek | Microsoft.Insights/Logs/UAFeedback/Read | Read data from the UAFeedback table |
> | Műveletek | Microsoft.Insights/Logs/UAHardwareSecurity/Read | Read data from the UAHardwareSecurity table |
> | Műveletek | Microsoft.Insights/Logs/UAIESiteDiscovery/Read | Read data from the UAIESiteDiscovery table |
> | Műveletek | Microsoft.Insights/Logs/UAOfficeAddIn/Read | Read data from the UAOfficeAddIn table |
> | Műveletek | Microsoft.Insights/Logs/UAProposedActionPlan/Read | Read data from the UAProposedActionPlan table |
> | Műveletek | Microsoft.Insights/Logs/UASysReqIssue/Read | Read data from the UASysReqIssue table |
> | Műveletek | Microsoft.Insights/Logs/UAUpgradedComputer/Read | Read data from the UAUpgradedComputer table |
> | Műveletek | Microsoft.Insights/Logs/Update/Read | Read data from the Update table |
> | Műveletek | Microsoft.Insights/Logs/UpdateRunProgress/Read | Read data from the UpdateRunProgress table |
> | Műveletek | Microsoft.Insights/Logs/UpdateSummary/Read | Read data from the UpdateSummary table |
> | Műveletek | Microsoft.Insights/Logs/Usage/Read | Read data from the Usage table |
> | Műveletek | Microsoft.Insights/Logs/W3CIISLog/Read | Read data from the W3CIISLog table |
> | Műveletek | Microsoft.Insights/Logs/WaaSDeploymentStatus/Read | Read data from the WaaSDeploymentStatus table |
> | Műveletek | Microsoft.Insights/Logs/WaaSInsiderStatus/Read | Read data from the WaaSInsiderStatus table |
> | Műveletek | Microsoft.Insights/Logs/WaaSUpdateStatus/Read | Read data from the WaaSUpdateStatus table |
> | Műveletek | Microsoft.Insights/Logs/WDAVStatus/Read | Read data from the WDAVStatus table |
> | Műveletek | Microsoft.Insights/Logs/WDAVThreat/Read | Read data from the WDAVThreat table |
> | Műveletek | Microsoft.Insights/Logs/WindowsClientAssessmentRecommendation/Read | Read data from the WindowsClientAssessmentRecommendation table |
> | Műveletek | Microsoft.Insights/Logs/WindowsFirewall/Read | Read data from the WindowsFirewall table |
> | Műveletek | Microsoft.Insights/Logs/WindowsServerAssessmentRecommendation/Read | Read data from the WindowsServerAssessmentRecommendation table |
> | Műveletek | Microsoft.Insights/Logs/WireData/Read | Read data from the WireData table |
> | Műveletek | Microsoft.Insights/Logs/WUDOAggregatedStatus/Read | Read data from the WUDOAggregatedStatus table |
> | Műveletek | Microsoft.Insights/Logs/WUDOStatus/Read | Read data from the WUDOStatus table |
> | Műveletek | Microsoft.Insights/MetricAlerts/Delete | Delete a metric alert |
> | Műveletek | Microsoft.Insights/MetricAlerts/Read | Read a metric alert |
> | Műveletek | Microsoft.Insights/MetricAlerts/Status/Read | Read metric alert status |
> | Műveletek | Microsoft.Insights/MetricAlerts/Write | Create or update a metric alert |
> | Műveletek | Microsoft.Insights/MetricBaselines/Read | Read metric baselines |
> | Műveletek | Microsoft.Insights/MetricDefinitions/Microsoft.Insights/Read | Read metric definitions |
> | Műveletek | Microsoft.Insights/MetricDefinitions/providers/Microsoft.Insights/Read | Read metric definitions |
> | Műveletek | Microsoft.Insights/MetricDefinitions/Read | Read metric definitions |
> | Műveletek | Microsoft.Insights/Metricnamespaces/Read | Read metric namespaces |
> | Műveletek | Microsoft.Insights/Metrics/Action | Metric Action |
> | Műveletek | Microsoft.Insights/Metrics/Microsoft.Insights/Read | Read metrics |
> | Műveletek | Microsoft.Insights/Metrics/providers/Metrics/Read | Read metrics |
> | Műveletek | Microsoft.Insights/Metrics/Read | Read metrics |
> | DataAction | Microsoft.Insights/Metrics/Write | Write metrics |
> | Műveletek | Microsoft.Insights/MigrateToNewpricingModel/Action | Migrate subscription to new pricing model |
> | Műveletek | Microsoft.Insights/Operations/Read | Olvasási műveletek |
> | Műveletek | Microsoft.Insights/Register/Action | Register the Microsoft Insights provider |
> | Műveletek | Microsoft.Insights/RollbackToLegacyPricingModel/Action | Rollback subscription to legacy pricing model |
> | Műveletek | Microsoft.Insights/ScheduledQueryRules/Delete | Deleting a scheduled query rule |
> | Műveletek | Microsoft.Insights/ScheduledQueryRules/Read | Reading a scheduled query rule |
> | Műveletek | Microsoft.Insights/ScheduledQueryRules/Write | Writing a scheduled query rule |
> | Műveletek | Microsoft.Insights/Tenants/Register/Action | Initializes the Microsoft Insights provider |
> | Műveletek | Microsoft.Insights/Unregister/Action | Register the Microsoft Insights provider |
> | Műveletek | Microsoft.Insights/Webtests/Delete | Deleting a webtest configuration |
> | Műveletek | Microsoft.Insights/Webtests/GetToken/Read | Reading a webtest token |
> | Műveletek | Microsoft.Insights/Webtests/MetricDefinitions/Read | Reading a webtest metric definitions |
> | Műveletek | Microsoft.Insights/Webtests/Metrics/Read | Reading a webtest metrics |
> | Műveletek | Microsoft.Insights/Webtests/Read | Reading a webtest configuration |
> | Műveletek | Microsoft.Insights/Webtests/Write | Writing to a webtest configuration |
> | Műveletek | Microsoft.Insights/Workbooks/Delete | Munkafüzet törlése |
> | Műveletek | Microsoft.Insights/Workbooks/Read | Read a workbook |
> | Műveletek | Microsoft.Insights/Workbooks/Write | Create or update a workbook |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Intune/diagnosticsettings/delete | Deleting a diagnostic setting |
> | Műveletek | Microsoft.Intune/diagnosticsettings/read | Reading a diagnostic setting |
> | Műveletek | Microsoft.Intune/diagnosticsettings/write | Writing a diagnostic setting |
> | Műveletek | Microsoft.Intune/diagnosticsettingscategories/read | Reading a diagnostic setting categories |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.IoTCentral/appTemplates/action | Gets all the available application templates on Azure IoT Central |
> | Műveletek | Microsoft.IoTCentral/checkNameAvailability/action | Checks if an IoT Central Application name is available |
> | Műveletek | Microsoft.IoTCentral/checkSubdomainAvailability/action | Checks if an IoT Central Application subdomain is available |
> | Műveletek | Microsoft.IoTCentral/IoTApps/delete | Deletes an IoT Central Applications |
> | Műveletek | Microsoft.IoTCentral/IoTApps/read | Gets a single IoT Central Application |
> | Műveletek | Microsoft.IoTCentral/IoTApps/write | Creates or Updates an IoT Central Applications |
> | Műveletek | Microsoft.IoTCentral/operations/read | Gets all the available operations on IoT Central Applications |
> | Műveletek | Microsoft.IoTCentral/register/action | Register the subscription for Azure IoT Central resource provider |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.IoTSpaces/Graph/delete | Deletes Microsoft.IoTSpaces Graph resource |
> | Műveletek | Microsoft.IoTSpaces/Graph/read | Gets the Microsoft.IoTSpaces Graph resource(s) |
> | Műveletek | Microsoft.IoTSpaces/Graph/write | Create Microsoft.IoTSpaces Graph resource |
> | Műveletek | Microsoft.IoTSpaces/register/action | Register subscription for Microsoft.IoTSpaces Graph resource provider to enable creating of resources |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.KeyVault/checkNameAvailability/read | Checks that a key vault name is valid and is not in use |
> | Műveletek | Microsoft.KeyVault/deletedVaults/read | View the properties of soft deleted key vaults |
> | Műveletek | Microsoft.KeyVault/hsmPools/delete | Delete an HSM pool |
> | Műveletek | Microsoft.KeyVault/hsmPools/joinVault/action | Join a key vault to an HSM pool |
> | Műveletek | Microsoft.KeyVault/hsmPools/read | View the properties of an HSM pool |
> | Műveletek | Microsoft.KeyVault/hsmPools/write | Create a new HSM pool of update the properties of an existing HSM pool |
> | Műveletek | Microsoft.KeyVault/locations/deletedVaults/purge/action | Purge a soft deleted key vault |
> | Műveletek | Microsoft.KeyVault/locations/deletedVaults/read | View the properties of a soft deleted key vault |
> | Műveletek | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | Notifies Microsoft.KeyVault that a virtual network or subnet is being deleted |
> | Műveletek | Microsoft.KeyVault/locations/operationResults/read | Check the result of a long run operation |
> | Műveletek | Microsoft.KeyVault/operations/read | Lists operations available on Microsoft.KeyVault resource provider |
> | Műveletek | Microsoft.KeyVault/register/action | Registers a subscription |
> | Műveletek | Microsoft.KeyVault/unregister/action | Unregisters a subscription |
> | Műveletek | Microsoft.KeyVault/vaults/accessPolicies/write | Update an existing access policy by merging or replacing, or add a new access policy to a vault. |
> | Műveletek | Microsoft.KeyVault/vaults/delete | Kulcstároló törlése |
> | Műveletek | Microsoft.KeyVault/vaults/deploy/action | Enables access to secrets in a key vault when deploying Azure resources |
> | Műveletek | Microsoft.KeyVault/vaults/eventGridFilters/delete | Notifies Microsoft.KeyVault that an EventGrid Subscription for Key Vault is being deleted |
> | Műveletek | Microsoft.KeyVault/vaults/eventGridFilters/read | Notifies Microsoft.KeyVault that an EventGrid Subscription for Key Vault is being viewed |
> | Műveletek | Microsoft.KeyVault/vaults/eventGridFilters/write | Notifies Microsoft.KeyVault that a new EventGrid Subscription for Key Vault is being created |
> | Műveletek | Microsoft.KeyVault/vaults/read | View the properties of a key vault |
> | Műveletek | Microsoft.KeyVault/vaults/secrets/read | View the properties of a secret, but not its value. |
> | Műveletek | Microsoft.KeyVault/vaults/secrets/write | Create a new secret or update the value of an existing secret. |
> | Műveletek | Microsoft.KeyVault/vaults/write | Create a new key vault or update the properties of an existing key vault |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Kusto/Clusters/Activate/action | Starts the cluster. |
> | Műveletek | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/delete | Deletes an attached database configuration resourceCopy. |
> | Műveletek | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/read | Reads an attached database configuration resourceCopy. |
> | Műveletek | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/write | Writes an attached database configuration resourceCopy. |
> | Műveletek | Microsoft.Kusto/Clusters/CheckNameAvailability/action | Checks the cluster name availability. |
> | Műveletek | Microsoft.Kusto/Clusters/Databases/AddPrincipals/action | Adds database principals. |
> | Műveletek | Microsoft.Kusto/Clusters/Databases/CheckNameAvailability/action | Checks name availability for a given type. |
> | Műveletek | Microsoft.Kusto/Clusters/Databases/DataConnections/delete | Deletes a data connections resourceCopy. |
> | Műveletek | Microsoft.Kusto/Clusters/Databases/DataConnections/read | Reads a data connections resourceCopy. |
> | Műveletek | Microsoft.Kusto/Clusters/Databases/DataConnections/write | Writes a data connections resourceCopy. |
> | Műveletek | Microsoft.Kusto/Clusters/Databases/DataConnectionValidation/action | Validates database data connection. |
> | Műveletek | Microsoft.Kusto/Clusters/Databases/delete | Deletes a database resourceCopy. |
> | Műveletek | Microsoft.Kusto/Clusters/Databases/EventHubConnections/delete | Deletes an Event Hub connections resourceCopy. |
> | Műveletek | Microsoft.Kusto/Clusters/Databases/EventHubConnections/read | Reads an Event Hub connections resourceCopy. |
> | Műveletek | Microsoft.Kusto/Clusters/Databases/EventHubConnections/write | Writes an Event Hub connections resourceCopy. |
> | Műveletek | Microsoft.Kusto/Clusters/Databases/EventHubConnectionValidation/action | Validates database Event Hub connection. |
> | Műveletek | Microsoft.Kusto/Clusters/Databases/ListPrincipals/action | Lists database principals. |
> | Műveletek | Microsoft.Kusto/Clusters/Databases/PrincipalAssignments/delete | Deletes a database principal assignments resourceCopy. |
> | Műveletek | Microsoft.Kusto/Clusters/Databases/PrincipalAssignments/read | Reads a database principal assignments resourceCopy. |
> | Műveletek | Microsoft.Kusto/Clusters/Databases/PrincipalAssignments/write | Writes a database principal assignments resourceCopy. |
> | Műveletek | Microsoft.Kusto/Clusters/Databases/read | Reads a database resourceCopy. |
> | Műveletek | Microsoft.Kusto/Clusters/Databases/RemovePrincipals/action | Removes database principals. |
> | Műveletek | Microsoft.Kusto/Clusters/Databases/write | Writes a database resourceCopy. |
> | Műveletek | Microsoft.Kusto/Clusters/Deactivate/action | Stops the cluster. |
> | Műveletek | Microsoft.Kusto/Clusters/delete | Deletes a cluster resourceCopy. |
> | Műveletek | Microsoft.Kusto/Clusters/DetachFollowerDatabases/action | Detaches follower's databases. |
> | Műveletek | Microsoft.Kusto/Clusters/DiagnoseVirtualNetwork/action | Diagnoses network connectivity status for external resources on which the service is depedent on. |
> | Műveletek | Microsoft.Kusto/Clusters/ListFollowerDatabases/action | Lists the follower's databases. |
> | Műveletek | Microsoft.Kusto/Clusters/read | Reads a cluster resourceCopy. |
> | Műveletek | Microsoft.Kusto/Clusters/SKUs/read | Reads a cluster SKU resourceCopy. |
> | Műveletek | Microsoft.Kusto/Clusters/Start/action | Starts the cluster. |
> | Műveletek | Microsoft.Kusto/Clusters/Stop/action | Stops the cluster. |
> | Műveletek | Microsoft.Kusto/Clusters/write | Writes a cluster resourceCopy. |
> | Műveletek | Microsoft.Kusto/Locations/CheckNameAvailability/action | Checks resourceCopy name availability. |
> | Műveletek | Microsoft.Kusto/Locations/GetNetworkPolicies/action | Gets Network Intent Policies |
> | Műveletek | Microsoft.Kusto/locations/operationresults/read | Reads operations resourceCopys |
> | Műveletek | Microsoft.Kusto/Operations/read | Reads operations resourceCopys |
> | Műveletek | Microsoft.Kusto/register/action | Subscription Registration Action |
> | Műveletek | Microsoft.Kusto/Register/action | Registers the subscription to the Kusto Resource Provider. |
> | Műveletek | Microsoft.Kusto/SKUs/read | Reads a SKU resourceCopy. |
> | Műveletek | Microsoft.Kusto/Unregister/action | Unregisters the subscription to the Kusto Resource Provider. |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.LabServices/labAccounts/CreateLab/action | Create a lab in a lab account. |
> | Műveletek | Microsoft.LabServices/labAccounts/delete | Delete lab accounts. |
> | Műveletek | Microsoft.LabServices/labAccounts/galleryImages/delete | Delete gallery images. |
> | Műveletek | Microsoft.LabServices/labAccounts/galleryImages/read | Read gallery images. |
> | Műveletek | Microsoft.LabServices/labAccounts/galleryImages/write | Add or modify gallery images. |
> | Műveletek | Microsoft.LabServices/labAccounts/GetRegionalAvailability/action | Get regional availability information for each size category configured under a lab account |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/AddUsers/action | Add users to a lab |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/delete | Delete labs. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | Delete environment setting. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | Delete environments. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | Read environments. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/ResetPassword/action | Resets the user password on an environment |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | Starts an environment by starting all resources inside the environment. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | Stops an environment by stopping all resources inside the environment |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/write | Add or modify environments. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | Provisions/deprovisions required resources for an environment setting based on current state of the lab/environment setting. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | Read environment setting. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/ResetPassword/action | Resets password on the template virtual machine. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/SaveImage/action | Saves current template image to the shared gallery in the lab account |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/delete | Delete schedules. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/read | Read schedules. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/write | Add or modify schedules. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | Starts a template by starting all resources inside the template. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | Stops a template by stopping all resources inside the template. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | Add or modify environment setting. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/read | Read labs. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/SendEmail/action | Send email with registration link to the lab |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/users/delete | Delete users. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/users/read | Read users. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/users/write | Add or modify users. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/write | Add or modify labs. |
> | Műveletek | Microsoft.LabServices/labAccounts/read | Read lab accounts. |
> | Műveletek | Microsoft.LabServices/labAccounts/sharedGalleries/delete | Delete sharedgalleries. |
> | Műveletek | Microsoft.LabServices/labAccounts/sharedGalleries/read | Read sharedgalleries. |
> | Műveletek | Microsoft.LabServices/labAccounts/sharedGalleries/write | Add or modify sharedgalleries. |
> | Műveletek | Microsoft.LabServices/labAccounts/sharedImages/delete | Delete sharedimages. |
> | Műveletek | Microsoft.LabServices/labAccounts/sharedImages/read | Read sharedimages. |
> | Műveletek | Microsoft.LabServices/labAccounts/sharedImages/write | Add or modify sharedimages. |
> | Műveletek | Microsoft.LabServices/labAccounts/write | Add or modify lab accounts. |
> | Műveletek | Microsoft.LabServices/locations/operations/read | Read operations. |
> | Műveletek | Microsoft.LabServices/register/action | Registers the subscription |
> | Műveletek | Microsoft.LabServices/users/ListAllEnvironments/action | List all Environments for the user |
> | Műveletek | Microsoft.LabServices/users/Register/action | Register a user to a managed lab |
> | Műveletek | Microsoft.LabServices/users/ResetPassword/action | Resets the user password on an environment |
> | Műveletek | Microsoft.LabServices/users/StartEnvironment/action | Starts an environment by starting all resources inside the environment. |
> | Műveletek | Microsoft.LabServices/users/StopEnvironment/action | Stops an environment by stopping all resources inside the environment |
> | Műveletek | Microsoft.LabServices/users/UserSettings/action | Updates and returns personal user settings. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Logic/integrationAccounts/agreements/delete | Deletes the agreement in integration account. |
> | Műveletek | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | Gets the callback URL for agreement content in integration account. |
> | Műveletek | Microsoft.Logic/integrationAccounts/agreements/read | Reads the agreement in integration account. |
> | Műveletek | Microsoft.Logic/integrationAccounts/agreements/write | Creates or updates the agreement in integration account. |
> | Műveletek | Microsoft. Logic/integrationAccounts/szerelvények/törlés | A szerelvény törlése az integrációs fiókban. |
> | Műveletek | Microsoft. Logic/integrationAccounts/szerelvények/listContentCallbackUrl/művelet | Beolvassa a szerelvény tartalmának visszahívási URL-címét az integrációs fiókban. |
> | Műveletek | Microsoft. Logic/integrationAccounts/szerelvények/olvasás | Beolvassa a szerelvényt az integrációs fiókban. |
> | Műveletek | Microsoft. Logic/integrationAccounts/szerelvények/írás | Létrehozza vagy frissíti a szerelvényt az integrációs fiókban. |
> | Műveletek | Microsoft. Logic/integrationAccounts/batchConfigurations/delete | Törli a Batch-konfigurációt az integrációs fiókban. |
> | Műveletek | Microsoft. Logic/integrationAccounts/batchConfigurations/READ | Beolvassa a Batch-konfigurációt az integrációs fiókban. |
> | Műveletek | Microsoft. Logic/integrationAccounts/batchConfigurations/írás | Létrehozza vagy frissíti a Batch-konfigurációt az integrációs fiókban. |
> | Műveletek | Microsoft. Logic/integrationAccounts/tanúsítványok/törlés | A tanúsítvány törlése az integrációs fiókban. |
> | Műveletek | Microsoft. Logic/integrationAccounts/tanúsítványok/olvasás | Beolvassa a tanúsítványt az integrációs fiókban. |
> | Műveletek | Microsoft. Logic/integrationAccounts/tanúsítványok/írás | A tanúsítvány létrehozása vagy frissítése az integrációs fiókban. |
> | Műveletek | Microsoft. Logic/integrationAccounts/delete | Az integrációs fiók törlése. |
> | Műveletek | Microsoft. Logic/integrationAccounts/csatlakozás/művelet | Csatlakozik az integrációs fiókhoz. |
> | Műveletek | Microsoft. Logic/integrationAccounts/listCallbackUrl/művelet | Lekéri az integrációs fiók visszahívási URL-címét. |
> | Műveletek | Microsoft. Logic/integrationAccounts/listKeyVaultKeys/művelet | A Key vaultban lévő kulcsok beolvasása. |
> | Műveletek | Microsoft. Logic/integrationAccounts/logTrackingEvents/művelet | Naplózza az integrációs fiók követési eseményeit. |
> | Műveletek | Microsoft. Logic/integrationAccounts/Maps/delete | Törli a leképezést az integrációs fiókban. |
> | Műveletek | Microsoft. Logic/integrationAccounts/Maps/listContentCallbackUrl/művelet | Lekéri a visszahívási URL-címet a leképezési tartalomhoz az integrációs fiókban. |
> | Műveletek | Microsoft. Logic/integrationAccounts/Maps/READ | Beolvassa a térképet az integrációs fiókban. |
> | Műveletek | Microsoft. Logic/integrationAccounts/Maps/Write | Létrehozza vagy frissíti a leképezést az integrációs fiókban. |
> | Műveletek | Microsoft. Logic/integrationAccounts/partnerek/törlés | Törli a partnert az integrációs fiókban. |
> | Műveletek | Microsoft. Logic/integrationAccounts/partnerek/listContentCallbackUrl/művelet | Beolvassa a partneri tartalmak visszahívási URL-címét az integrációs fiókban. |
> | Műveletek | Microsoft. Logic/integrationAccounts/partnerek/olvasás | Beolvassa a partnert az integrációs fiókban. |
> | Műveletek | Microsoft. Logic/integrationAccounts/partnerek/írás | Létrehozza vagy frissíti a partnert az integrációs fiókban. |
> | Műveletek | Microsoft. Logic/integrationAccounts/Providers/Microsoft. bepillantást/logDefinitions/READ | Beolvassa az integrációs fiók naplójának definícióit. |
> | Műveletek | Microsoft. Logic/integrationAccounts/READ | Beolvassa az integrációs fiókot. |
> | Műveletek | Microsoft. Logic/integrationAccounts/regenerateAccessKey/művelet | Regenerates the access key secrets. |
> | Műveletek | Microsoft. Logic/integrationAccounts/rosettaNetProcessConfigurations/delete | A RosettaNet folyamat konfigurációjának törlése az integrációs fiókban. |
> | Műveletek | Microsoft. Logic/integrationAccounts/rosettaNetProcessConfigurations/READ | A RosettaNet folyamat konfigurációjának beolvasása az integrációs fiókban. |
> | Műveletek | Microsoft. Logic/integrationAccounts/rosettaNetProcessConfigurations/írás | A RosettaNet folyamat konfigurációjának létrehozása vagy frissítése az integrációs fiókban. |
> | Műveletek | Microsoft. Logic/integrationAccounts/sémák/törlés | Törli a sémát az integrációs fiókban. |
> | Műveletek | Microsoft. Logic/integrationAccounts/sémák/listContentCallbackUrl/művelet | Lekéri a séma tartalmának visszahívási URL-címét az integrációs fiókban. |
> | Műveletek | Microsoft. Logic/integrationAccounts/sémák/olvasás | Beolvassa a sémát az integrációs fiókban. |
> | Műveletek | Microsoft. Logic/integrationAccounts/sémák/írás | Létrehozza vagy frissíti a sémát az integrációs fiókban. |
> | Műveletek | Microsoft. Logic/integrationAccounts/Sessions/delete | A munkamenet törlése az integrációs fiókban. |
> | Műveletek | Microsoft. Logic/integrationAccounts/Sessions/READ | Beolvassa a Batch-konfigurációt az integrációs fiókban. |
> | Műveletek | Microsoft. Logic/integrationAccounts/Sessions/Write | A munkamenet létrehozása vagy frissítése az integrációs fiókban. |
> | Műveletek | Microsoft. Logic/integrationAccounts/Write | Létrehozza vagy frissíti az integrációs fiókot. |
> | Műveletek | Microsoft. Logic/integrationServiceEnvironments/availableManagedApis/READ | Az integrációs szolgáltatási környezet elérhető felügyelt API-k beolvasása. |
> | Műveletek | Microsoft. Logic/integrationServiceEnvironments/delete | Törli az integrációs szolgáltatási környezetet. |
> | Műveletek | Microsoft. Logic/integrationServiceEnvironments/csatlakozás/művelet | Csatlakozik a integrációs szolgáltatási környezethoz. |
> | Műveletek | Microsoft. Logic/integrationServiceEnvironments/król/apiOperations/READ | Az integrációs szolgáltatás környezetének felügyelt API-műveletének beolvasása. |
> | Műveletek | Microsoft. Logic/integrationServiceEnvironments/król/csatlakozás/művelet | Csatlakozik a integrációs szolgáltatási környezet felügyelt API-hoz. |
> | Műveletek | Microsoft. Logic/integrationServiceEnvironments/król/operationStatuses/READ | Az integrációs szolgáltatás környezetének felügyelt API műveleti állapotának beolvasása. |
> | Műveletek | Microsoft. Logic/integrationServiceEnvironments/król/READ | Beolvassa az integrációs szolgáltatás környezetének felügyelt API-ját. |
> | Műveletek | Microsoft. Logic/integrationServiceEnvironments/król/írás | Az integrációs szolgáltatás környezetének felügyelt API-ját hozza létre vagy frissíti. |
> | Műveletek | Microsoft. Logic/integrationServiceEnvironments/operationStatuses/READ | Reads the integration service environment operation statuses. |
> | Műveletek | Microsoft.Logic/integrationServiceEnvironments/providers/Microsoft.Insights/metricDefinitions/read | Reads the integration service environment metric definitions. |
> | Műveletek | Microsoft.Logic/integrationServiceEnvironments/read | Reads the integration service environment. |
> | Műveletek | Microsoft.Logic/integrationServiceEnvironments/write | Creates or updates the integration service environment. |
> | Műveletek | Microsoft.Logic/locations/workflows/recommendOperationGroups/action | Gets the workflow recommend operation groups. |
> | Műveletek | Microsoft.Logic/locations/workflows/validate/action | Validates the workflow. |
> | Műveletek | Microsoft.Logic/operations/read | Gets the operation. |
> | Műveletek | Microsoft.Logic/register/action | Registers the Microsoft.Logic resource provider for a given subscription. |
> | Műveletek | Microsoft.Logic/workflows/accessKeys/delete | Deletes the access key. |
> | Műveletek | Microsoft.Logic/workflows/accessKeys/list/action | Lists the access key secrets. |
> | Műveletek | Microsoft.Logic/workflows/accessKeys/read | Reads the access key. |
> | Műveletek | Microsoft.Logic/workflows/accessKeys/regenerate/action | Regenerates the access key secrets. |
> | Műveletek | Microsoft.Logic/workflows/accessKeys/write | Creates or updates the access key. |
> | Műveletek | Microsoft.Logic/workflows/delete | Deletes the workflow. |
> | Műveletek | Microsoft.Logic/workflows/detectors/read | Reads the workflow detector. |
> | Műveletek | Microsoft.Logic/workflows/disable/action | Disables the workflow. |
> | Műveletek | Microsoft.Logic/workflows/enable/action | A munkafolyamat engedélyezése. |
> | Műveletek | Microsoft.Logic/workflows/listCallbackUrl/action | Gets the callback URL for workflow. |
> | Műveletek | Microsoft.Logic/workflows/listSwagger/action | Gets the workflow swagger definitions. |
> | Műveletek | Microsoft.Logic/workflows/move/action | Moves Workflow from its existing subscription id, resource group, and/or name to a different subscription id, resource group, and/or name. |
> | Műveletek | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/read | Reads the workflow diagnostic settings. |
> | Műveletek | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/write | Creates or updates the workflow diagnostic setting. |
> | Műveletek | Microsoft.Logic/workflows/providers/Microsoft.Insights/logDefinitions/read | Reads the workflow log definitions. |
> | Műveletek | Microsoft.Logic/workflows/providers/Microsoft.Insights/metricDefinitions/read | Reads the workflow metric definitions. |
> | Műveletek | Microsoft.Logic/workflows/read | Reads the workflow. |
> | Műveletek | Microsoft.Logic/workflows/regenerateAccessKey/action | Regenerates the access key secrets. |
> | Műveletek | Microsoft.Logic/workflows/run/action | Starts a run of the workflow. |
> | Műveletek | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | Gets the workflow run action expression traces. |
> | Műveletek | Microsoft.Logic/workflows/runs/actions/read | Reads the workflow run action. |
> | Műveletek | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | Gets the workflow run action repetition expression traces. |
> | Műveletek | Microsoft.Logic/workflows/runs/actions/repetitions/read | Reads the workflow run action repetition. |
> | Műveletek | Microsoft.Logic/workflows/runs/actions/repetitions/requestHistories/read | Reads the workflow run repetition action request history. |
> | Műveletek | Microsoft.Logic/workflows/runs/actions/requestHistories/read | Reads the workflow run action request history. |
> | Műveletek | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | Reads the workflow run action scope repetition. |
> | Műveletek | Microsoft.Logic/workflows/runs/cancel/action | Cancels the run of a workflow. |
> | Műveletek | Microsoft.Logic/workflows/runs/delete | Deletes a run of a workflow. |
> | Műveletek | Microsoft.Logic/workflows/runs/operations/read | Reads the workflow run operation status. |
> | Műveletek | Microsoft.Logic/workflows/runs/read | Reads the workflow run. |
> | Műveletek | Microsoft.Logic/workflows/suspend/action | Suspends the workflow. |
> | Műveletek | Microsoft.Logic/workflows/triggers/histories/read | Reads the trigger histories. |
> | Műveletek | Microsoft.Logic/workflows/triggers/histories/resubmit/action | Resubmits the workflow trigger. |
> | Műveletek | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | Gets the callback URL for trigger. |
> | Műveletek | Microsoft.Logic/workflows/triggers/read | Reads the trigger. |
> | Műveletek | Microsoft.Logic/workflows/triggers/reset/action | Resets the trigger. |
> | Műveletek | Microsoft.Logic/workflows/triggers/run/action | Executes the trigger. |
> | Műveletek | Microsoft.Logic/workflows/triggers/setState/action | Sets the trigger state. |
> | Műveletek | Microsoft.Logic/workflows/validate/action | Validates the workflow. |
> | Műveletek | Microsoft.Logic/workflows/versions/read | Reads the workflow version. |
> | Műveletek | Microsoft.Logic/workflows/versions/triggers/listCallbackUrl/action | Gets the callback URL for trigger. |
> | Műveletek | Microsoft.Logic/workflows/write | Creates or updates the workflow. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | Move any Machine Learning Commitment Plan Association |
> | Műveletek | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | Read any Machine Learning Commitment Plan Association |
> | Műveletek | Microsoft.MachineLearning/commitmentPlans/delete | Delete any Machine Learning Commitment Plan |
> | Műveletek | Microsoft.MachineLearning/commitmentPlans/join/action | Join any Machine Learning Commitment Plan |
> | Műveletek | Microsoft.MachineLearning/commitmentPlans/read | Read any Machine Learning Commitment Plan |
> | Műveletek | Microsoft.MachineLearning/commitmentPlans/write | Create or Update any Machine Learning Commitment Plan |
> | Műveletek | Microsoft.MachineLearning/locations/operationresults/read | Machine Learning művelet eredményének beolvasása |
> | Műveletek | Microsoft. MachineLearning/Locations/operationsstatus/READ | Folyamatban lévő Machine Learning művelet állapotának beolvasása |
> | Műveletek | Microsoft. MachineLearning/Operations/READ | Machine Learning műveletek beolvasása |
> | Műveletek | Microsoft. MachineLearning/regisztráció/művelet | Regisztrálja az előfizetést a Machine learning webszolgáltatás erőforrás-szolgáltatója számára, és lehetővé teszi a webszolgáltatások létrehozását. |
> | Műveletek | Microsoft. MachineLearning/SKU/READ | Machine Learning kötelezettségvállalási terv beszerzése |
> | Műveletek | Microsoft. MachineLearning/webszolgáltatások/művelet | Regionális webszolgáltatás tulajdonságainak létrehozása a támogatott régiók számára |
> | Műveletek | Microsoft. MachineLearning/webszolgáltatások/törlés | Machine Learning webszolgáltatás törlése |
> | Műveletek | Microsoft. MachineLearning/webszolgáltatás/listkeys műveletének beolvasása/READ | Kulcsok beolvasása egy Machine Learning webszolgáltatáshoz |
> | Műveletek | Microsoft. MachineLearning/webszolgáltatások/olvasás | Bármely Machine Learning webszolgáltatás beolvasása |
> | Műveletek | Microsoft. MachineLearning/webszolgáltatások/írás | Machine Learning webszolgáltatás létrehozása vagy frissítése |
> | Műveletek | Microsoft. MachineLearning/munkaterületek/törlés | Tetszőleges Machine Learning-munkaterület törlése |
> | Műveletek | Microsoft. MachineLearning/munkaterületek/listworkspacekeys/művelet | Machine Learning-munkaterület kulcsainak listázása |
> | Műveletek | Microsoft. MachineLearning/munkaterületek/olvasás | Bármilyen Machine Learning-munkaterület beolvasása |
> | Műveletek | Microsoft. MachineLearning/munkaterületek/resyncstoragekeys/művelet | Machine Learning-munkaterület konfigurált Storage-fiók kulcsainak újraszinkronizálása |
> | Műveletek | Microsoft. MachineLearning/munkaterületek/írás | Tetszőleges Machine Learning-munkaterület létrehozása vagy frissítése |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. MachineLearningServices/Locations/computeoperationsstatus/READ | Egy adott számítási művelet állapotának beolvasása |
> | Műveletek | Microsoft. MachineLearningServices/Locations/kvóták/olvasás | Lekéri a jelenleg hozzárendelt munkaterület-kvótákat a VMFamily alapján. |
> | Műveletek | Microsoft. MachineLearningServices/Locations/updateQuotas/Action | Frissítse a kvótát minden virtuálisgép-családhoz a munkaterületen. |
> | Műveletek | Microsoft. MachineLearningServices/Locations/használati adatok/olvasás | Használati jelentés az előfizetéshez tartozó pénzmosás-számítási erőforrásokhoz |
> | Műveletek | Microsoft. MachineLearningServices/Locations/méreteinek listáján/READ | Támogatott virtuális gépek méretének beolvasása |
> | Műveletek | Microsoft. MachineLearningServices/Locations/workspaceOperationsStatus/READ | Egy adott munkaterület-művelet állapotának beolvasása |
> | Műveletek | Microsoft. MachineLearningServices/regisztráció/művelet | Regisztrálja az előfizetést a Machine Learning Services erőforrás-szolgáltatóhoz |
> | Műveletek | Microsoft. MachineLearningServices/munkaterületek/számítások/törlés | Machine Learning Services munkaterület (ek) ben lévő számítási erőforrások törlése |
> | Műveletek | Microsoft. MachineLearningServices/munkaterületek/számítások/Listkeys műveletének beolvasása/művelet | Machine Learning Services munkaterület számítási erőforrásaihoz tartozó titkok listázása |
> | Műveletek | Microsoft. MachineLearningServices/munkaterületek/számítások/listNodes/művelet | Számítási erőforrás csomópontjainak listázása Machine Learning Services munkaterületen |
> | Műveletek | Microsoft. MachineLearningServices/munkaterületek/számítások/olvasás | Beolvassa a Machine Learning Services munkaterület (ek) számítási erőforrásait |
> | Műveletek | Microsoft. MachineLearningServices/munkaterületek/számítások/írás | A számítási erőforrások létrehozása vagy frissítése Machine Learning Services munkaterületen |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/datadriftdetectors/olvasás | Adatdrift-érzékelők beolvasása Machine Learning Services munkaterületen |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/datadriftdetectors/írás | Adatdrift-érzékelők létrehozása vagy frissítése Machine Learning Services munkaterületen |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/adatkészletek/regisztrált/törlés | A regisztrált adatkészletek törlése Machine Learning Services munkaterületen (ek) |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/adatkészletek/regisztrált/előzetes/olvasási | Beolvassa a regisztrált adatkészletek adatkészlet-előnézetét Machine Learning Services munkaterület (ok) ban |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/adatkészletek/regisztrált/profil/olvasás | Beolvassa a Machine Learning Services munkaterület (ek) ben regisztrált adatkészletek adatkészlet-profiljait |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/adatkészletek/regisztrált/profil/írás | Adatkészlet-profilok létrehozása vagy frissítése Machine Learning Services munkaterületen |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/adatkészletek/regisztrált/olvasható | Beolvassa a regisztrált adatkészleteket Machine Learning Services munkaterületen (ek) |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/adatkészletek/regisztrált/írási | Regisztrált adatkészletek létrehozása vagy frissítése Machine Learning Services munkaterületen |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/adatkészletek/Regisztráció törlése/törlés | Törli a nem regisztrált adatkészleteket Machine Learning Services munkaterületen |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/adatkészletek/nem regisztrált/előzetes/olvasási | Beolvassa a nem regisztrált adatkészletek adatkészlet-előnézetét Machine Learning Services munkaterületen |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/adatkészletek/nem regisztrált/profil/olvasás | Beolvassa az Machine Learning Services munkaterületen található nem regisztrált adatkészletek adatkészlet-profiljait |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/adatkészletek/nem regisztrált/olvasott | Nem regisztrált adatkészletek beolvasása Machine Learning Services munkaterületen |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/adatkészletek/nem regisztrált/írási | Nem regisztrált adatkészletek létrehozása vagy frissítése Machine Learning Services munkaterületen |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/adattárolók/törlés | Machine Learning Services munkaterület (ek) ben lévő adattárolók törlése |
> | DataAction | Microsoft. MachineLearningServices/munkaterület/adattár/olvasás | Adattárolók beolvasása Machine Learning Services munkaterületen (ek) |
> | DataAction | Microsoft. MachineLearningServices/munkaterület/adattár/írás | Adattárolók létrehozása vagy frissítése Machine Learning Services munkaterületen |
> | Műveletek | Microsoft. MachineLearningServices/munkaterületek/törlés | Törli a Machine Learning Services munkaterületet (ka) t |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/végpontok/folyamatok/olvasás | Beolvassa a Machine Learning Services munkaterület (ok) ban közzétett folyamatokat és folyamat-végpontokat |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/végpontok/folyamatok/írás | Közzétett folyamatok és folyamat-végpontok létrehozása vagy frissítése Machine Learning Services munkaterület (ok) ban |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/környezetek/létrehozás/művelet | Környezetek létrehozása Machine Learning Services munkaterület (ek) ben |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/környezetek/olvasás | Környezetek beolvasása Machine Learning Services munkaterületen |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/környezetek/readSecrets/művelet | Környezetek beolvasása a Machine Learning Services munkaterület (ek) titkos kulcsaival |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/környezetek/írás | Környezetek létrehozása vagy frissítése Machine Learning Services munkaterület (ek) ben |
> | Műveletek | Microsoft. MachineLearningServices/munkaterületek/eventGridFilters/olvasás | Event Grid szűrő beszerzése egy adott munkaterülethez |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/kísérletek/törlés | Kísérleteket töröl Machine Learning Services munkaterületen (k) |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/kísérletek/olvasás | Kísérletek beolvasása Machine Learning Services munkaterületen |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/kísérletek/futtatások/olvasás | Machine Learning Services munkaterületen fut |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/kísérletek/futtatások/scriptRun/küldés/művelet | Parancsfájl-futtatások létrehozása vagy frissítése Machine Learning Services munkaterületen |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/kísérletek/Futtatás/írás | A (z) Machine Learning Services munkaterületen lévő futtatások létrehozása vagy frissítése |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/kísérletek/írás | Kísérletek létrehozása vagy frissítése Machine Learning Services munkaterületen |
> | Műveletek | Microsoft. MachineLearningServices/munkaterületek/Listkeys műveletének beolvasása/művelet | Machine Learning Services munkaterület titkainak listázása |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/metaadatok/összetevők/törlés | Összetevők törlése Machine Learning Services munkaterületen |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/metaadatok/összetevők/olvasás | Összetevők beolvasása Machine Learning Services munkaterületen |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/metaadatok/összetevők/írás | Összetevők létrehozása vagy frissítése Machine Learning Services munkaterületen |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/metaadatok/Pillanatképek/törlés | Pillanatképek törlése Machine Learning Services munkaterületen (ek) |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/metaadatok/Pillanatképek/olvasás | Pillanatképek beolvasása Machine Learning Services munkaterületen (ek) |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/metaadatok/Pillanatképek/írás | Pillanatképek létrehozása vagy frissítése Machine Learning Services munkaterületen |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/modellek/törlés | Machine Learning Services munkaterület (ek) modelljeinek törlése |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/modellek/letöltés/művelet | Machine Learning Services munkaterület (ek) modelljeinek letöltése |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/modellek/csomag/művelet | Csomagok modelljei Machine Learning Services munkaterületen |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/modellek/olvasás | Machine Learning Services-munkaterület (ek) modelljeinek beolvasása |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/modellek/írás | Modellek létrehozása vagy frissítése Machine Learning Services munkaterületen |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/modulok/olvasás | Modulok beolvasása Machine Learning Services munkaterület (ek) ben |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/modulok/írás | Modul létrehozása vagy frissítése Machine Learning Services munkaterület (ek) ben |
> | DataAction | Microsoft. MachineLearningServices/munkaterület/pipelinedrafts/delete | Machine Learning Services munkaterület (ek) ben lévő folyamat-Piszkozatok törlése |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/pipelinedrafts/olvasás | Machine Learning Services munkaterület (ek) ben beolvassa a folyamat piszkozatait |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/pipelinedrafts/írás | Folyamat-Piszkozatok létrehozása vagy frissítése Machine Learning Services munkaterületen |
> | Műveletek | Microsoft. MachineLearningServices/munkaterületek/olvasás | A Machine Learning Services munkaterület (ok) beolvasása |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/szolgáltatások/ACI/delete | ACI-szolgáltatások törlése Machine Learning Services munkaterületen |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/szolgáltatások/ACI/listkeys műveletének beolvasása/művelet | Lists keys for ACI services in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/services/aci/write | Creates or updates ACI services in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/services/aks/devtest/delete | Deletes devtest AKS services in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/services/aks/devtest/listkeys/action | Lists keys for devtest AKS services in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/services/aks/devtest/score/action | Scores devtest AKS services in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/services/aks/devtest/write | Creates or updates devtest AKS services in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/services/aks/prod/delete | Deletes prod AKS services in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/services/aks/prod/listkeys/action | Lists keys for prod AKS services in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/services/aks/prod/score/action | Scores prod AKS services in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/services/aks/prod/write | Creates or updates prod AKS services in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/services/read | Gets services in Machine Learning Services Workspace(s) |
> | Műveletek | Microsoft.MachineLearningServices/workspaces/write | Creates or updates a Machine Learning Services Workspace(s) |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ManagedIdentity/identities/read | Gets an existing system assigned identity |
> | Műveletek | Microsoft.ManagedIdentity/operations/read | Lists operations available on Microsoft.ManagedIdentity resource provider |
> | Műveletek | Microsoft.ManagedIdentity/register/action | Registers the subscription for the managed identity resource provider |
> | Műveletek | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | RBAC action for assigning an existing user assigned identity to a resource |
> | Műveletek | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Deletes an existing user assigned identity |
> | Műveletek | Microsoft.ManagedIdentity/userAssignedIdentities/read | Gets an existing user assigned identity |
> | Műveletek | Microsoft.ManagedIdentity/userAssignedIdentities/write | Creates a new user assigned identity or updates the tags associated with an existing user assigned identity |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ManagedServices/marketplaceRegistrationDefinitions/read | Retrieves a list of Managed Services registration definitions. |
> | Műveletek | Microsoft.ManagedServices/operations/read | Retrieves a list of Managed Services operations. |
> | Műveletek | Microsoft.ManagedServices/operationStatuses/read | Reads the operation status for the resource. |
> | Műveletek | Microsoft.ManagedServices/register/action | Register to Managed Services. |
> | Műveletek | Microsoft.ManagedServices/registrationAssignments/delete | Removes Managed Services registration assignment. |
> | Műveletek | Microsoft.ManagedServices/registrationAssignments/read | Retrieves a list of Managed Services registration assignments. |
> | Műveletek | Microsoft.ManagedServices/registrationAssignments/write | Add or modify Managed Services registration assignment. |
> | Műveletek | Microsoft.ManagedServices/registrationDefinitions/delete | Removes Managed Services registration definition. |
> | Műveletek | Microsoft.ManagedServices/registrationDefinitions/read | Retrieves a list of Managed Services registration definitions. |
> | Műveletek | Microsoft.ManagedServices/registrationDefinitions/write | Add or modify Managed Services registration definition. |
> | Műveletek | Microsoft.ManagedServices/unregister/action | Unregister from Managed Services. |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Management/checkNameAvailability/action | Checks if the specified management group name is valid and unique. |
> | Műveletek | Microsoft.Management/getEntities/action | List all entities (Management Groups, Subscriptions, etc.) for the authenticated user. |
> | Műveletek | Microsoft.Management/managementGroups/delete | Delete management group. |
> | Műveletek | Microsoft.Management/managementGroups/descendants/read | Gets all the descendants (Management Groups, Subscriptions) of a Management Group. |
> | Műveletek | Microsoft.Management/managementGroups/read | List management groups for the authenticated user. |
> | Műveletek | Microsoft.Management/managementGroups/subscriptions/delete | De-associates subscription from the management group. |
> | Műveletek | Microsoft.Management/managementGroups/subscriptions/write | Associates existing subscription with the management group. |
> | Műveletek | Microsoft.Management/managementGroups/write | Create or update a management group. |
> | Műveletek | Microsoft.Management/register/action | Register the specified subscription with Microsoft.Management |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | DataAction | Microsoft.Maps/accounts/data/read | Grants data read access to a maps account. |
> | Műveletek | Microsoft.Maps/accounts/delete | Delete a Maps Account. |
> | Műveletek | Microsoft.Maps/accounts/eventGridFilters/delete | Delete an Event Grid filter |
> | Műveletek | Microsoft.Maps/accounts/eventGridFilters/read | Get an Event Grid filter |
> | Műveletek | Microsoft.Maps/accounts/eventGridFilters/write | Create or update an Event Grid filter |
> | Műveletek | Microsoft.Maps/accounts/listKeys/action | List Maps Account keys |
> | Műveletek | Microsoft.Maps/accounts/read | Get a Maps Account. |
> | Műveletek | Microsoft.Maps/accounts/regenerateKey/action | Generate new Maps Account primary or secondary key |
> | Műveletek | Microsoft.Maps/accounts/write | Create or update a Maps Account. |
> | Műveletek | Microsoft.Maps/operations/read | Read the provider operations |
> | Műveletek | Microsoft.Maps/register/action | Register the provider |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | Returns an Agreement. |
> | Műveletek | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | Accepts a signed agreement. |
> | Műveletek | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | Imports an image to the end user's ACR. |
> | Műveletek | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | Returns a config. |
> | Műveletek | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | Saves a config. |
> | Műveletek | Microsoft.Marketplace/register/action | Registers Microsoft.Marketplace resource provider in the subscription. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.MarketplaceApps/ClassicDevServices/delete | Does a DELETE operation on a classic dev service resource. |
> | Műveletek | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | Gets a classic dev service resource management keys. |
> | Műveletek | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | Gets the Single Sign On URL for a classic dev service. |
> | Műveletek | Microsoft.MarketplaceApps/ClassicDevServices/read | Does a GET operation on a classic dev service. |
> | Műveletek | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | Generates a classic dev service resource management keys. |
> | Műveletek | Microsoft.MarketplaceApps/Operations/read | Read the operations for all resource types. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.MarketplaceOrdering/agreements/offers/plans/cancel/action | Cancel an agreement for a given marketplace item |
> | Műveletek | Microsoft.MarketplaceOrdering/agreements/offers/plans/read | Return an agreement for a given marketplace item |
> | Műveletek | Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action | Sign an agreement for a given marketplace item |
> | Műveletek | Microsoft.MarketplaceOrdering/agreements/read | Return all agreements under given subscription |
> | Műveletek | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | Get an agreement for a given marketplace virtual machine item |
> | Műveletek | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | Sign or Cancel an agreement for a given marketplace virtual machine item |
> | Műveletek | Microsoft.MarketplaceOrdering/operations/read | List all possible operations in the API |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Media/checknameavailability/action | Checks if a Media Services account name is available |
> | Műveletek | Microsoft.Media/mediaservices/accountfilters/delete | Delete any Account Filter |
> | Műveletek | Microsoft.Media/mediaservices/accountfilters/read | Read any Account Filter |
> | Műveletek | Microsoft.Media/mediaservices/accountfilters/write | Create or Update any Account Filter |
> | Műveletek | Microsoft.Media/mediaservices/assets/assetfilters/delete | Delete any Asset Filter |
> | Műveletek | Microsoft.Media/mediaservices/assets/assetfilters/read | Read any Asset Filter |
> | Műveletek | Microsoft.Media/mediaservices/assets/assetfilters/write | Create or Update any Asset Filter |
> | Műveletek | Microsoft.Media/mediaservices/assets/delete | Delete any Asset |
> | Műveletek | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | Get Asset Encryption Key |
> | Műveletek | Microsoft.Media/mediaservices/assets/listContainerSas/action | List Asset Container SAS URLs |
> | Műveletek | Microsoft.Media/mediaservices/assets/listStreamingLocators/action | List Streaming Locators for Asset |
> | Műveletek | Microsoft.Media/mediaservices/assets/read | Read any Asset |
> | Műveletek | Microsoft.Media/mediaservices/assets/write | Create or Update any Asset |
> | Műveletek | Microsoft.Media/mediaservices/contentKeyPolicies/delete | Delete any Content Key Policy |
> | Műveletek | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | Get Policy Properties With Secrets |
> | Műveletek | Microsoft.Media/mediaservices/contentKeyPolicies/read | Read any Content Key Policy |
> | Műveletek | Microsoft.Media/mediaservices/contentKeyPolicies/write | Create or Update any Content Key Policy |
> | Műveletek | Microsoft.Media/mediaservices/delete | Delete any Media Services Account |
> | Műveletek | Microsoft.Media/mediaservices/eventGridFilters/delete | Delete any Event Grid Filter |
> | Műveletek | Microsoft.Media/mediaservices/eventGridFilters/read | Read any Event Grid Filter |
> | Műveletek | Microsoft.Media/mediaservices/eventGridFilters/write | Create or Update any Event Grid Filter |
> | Műveletek | Microsoft.Media/mediaservices/liveEventOperations/read | Read any Live Event Operation |
> | Műveletek | Microsoft.Media/mediaservices/liveEvents/delete | Delete any Live Event |
> | Műveletek | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | Delete any Live Output |
> | Műveletek | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | Read any Live Output |
> | Műveletek | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | Create or Update any Live Output |
> | Műveletek | Microsoft.Media/mediaservices/liveEvents/read | Read any Live Event |
> | Műveletek | Microsoft.Media/mediaservices/liveEvents/reset/action | Reset any Live Event Operation |
> | Műveletek | Microsoft.Media/mediaservices/liveEvents/start/action | Start any Live Event Operation |
> | Műveletek | Microsoft.Media/mediaservices/liveEvents/stop/action | Stop any Live Event Operation |
> | Műveletek | Microsoft.Media/mediaservices/liveEvents/write | Create or Update any Live Event |
> | Műveletek | Microsoft.Media/mediaservices/liveOutputOperations/read | Read any Live Output Operation |
> | Műveletek | Microsoft.Media/mediaservices/read | Read any Media Services Account |
> | Műveletek | Microsoft.Media/mediaservices/streamingEndpointOperations/read | Read any Streaming Endpoint Operation |
> | Műveletek | Microsoft.Media/mediaservices/streamingEndpoints/delete | Delete any Streaming Endpoint |
> | Műveletek | Microsoft.Media/mediaservices/streamingEndpoints/read | Read any Streaming Endpoint |
> | Műveletek | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | Scale any Streaming Endpoint Operation |
> | Műveletek | Microsoft.Media/mediaservices/streamingEndpoints/start/action | Start any Streaming Endpoint Operation |
> | Műveletek | Microsoft.Media/mediaservices/streamingEndpoints/stop/action | Stop any Streaming Endpoint Operation |
> | Műveletek | Microsoft.Media/mediaservices/streamingEndpoints/write | Create or Update any Streaming Endpoint |
> | Műveletek | Microsoft.Media/mediaservices/streamingLocators/delete | Delete any Streaming Locator |
> | Műveletek | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | List Content Keys |
> | Műveletek | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | List Paths |
> | Műveletek | Microsoft.Media/mediaservices/streamingLocators/read | Read any Streaming Locator |
> | Műveletek | Microsoft.Media/mediaservices/streamingLocators/write | Create or Update any Streaming Locator |
> | Műveletek | Microsoft.Media/mediaservices/streamingPolicies/delete | Delete any Streaming Policy |
> | Műveletek | Microsoft.Media/mediaservices/streamingPolicies/read | Read any Streaming Policy |
> | Műveletek | Microsoft.Media/mediaservices/streamingPolicies/write | Create or Update any Streaming Policy |
> | Műveletek | Microsoft.Media/mediaservices/syncStorageKeys/action | Synchronize the Storage Keys for an attached Azure Storage account |
> | Műveletek | Microsoft.Media/mediaservices/transforms/delete | Delete any Transform |
> | Műveletek | Microsoft.Media/mediaservices/transforms/jobs/cancelJob/action | Cancel Job |
> | Műveletek | Microsoft.Media/mediaservices/transforms/jobs/delete | Delete any Job |
> | Műveletek | Microsoft.Media/mediaservices/transforms/jobs/read | Read any Job |
> | Műveletek | Microsoft.Media/mediaservices/transforms/jobs/write | Create or Update any Job |
> | Műveletek | Microsoft.Media/mediaservices/transforms/read | Read any Transform |
> | Műveletek | Microsoft.Media/mediaservices/transforms/write | Create or Update any Transform |
> | Műveletek | Microsoft.Media/mediaservices/write | Create or Update any Media Services Account |
> | Műveletek | Microsoft.Media/operations/read | Get Available Operations |
> | Műveletek | Microsoft.Media/register/action | Registers the subscription for the Media Services resource provider and enables the creation of Media Services accounts |
> | Műveletek | Microsoft.Media/unregister/action | Unregisters the subscription for the Media Services resource provider |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Migrate/assessmentprojects/assessmentOptions/read | Gets the assessment options which are available in the given location |
> | Műveletek | Microsoft.Migrate/assessmentprojects/assessments/read | Lists assessments within a project |
> | Műveletek | Microsoft.Migrate/assessmentprojects/delete | Deletes the assessment project |
> | Műveletek | Microsoft.Migrate/assessmentprojects/groups/assessments/assessedmachines/read | Get the properties of an assessed machine |
> | Műveletek | Microsoft.Migrate/assessmentprojects/groups/assessments/delete | Deletes an assessment |
> | Műveletek | Microsoft.Migrate/assessmentprojects/groups/assessments/downloadurl/action | Downloads an assessment report's URL |
> | Műveletek | Microsoft.Migrate/assessmentprojects/groups/assessments/read | Gets the properties of an assessment |
> | Műveletek | Microsoft.Migrate/assessmentprojects/groups/assessments/write | Creates a new assessment or updates an existing assessment |
> | Műveletek | Microsoft.Migrate/assessmentprojects/groups/delete | Deletes a group |
> | Műveletek | Microsoft.Migrate/assessmentprojects/groups/read | Get the properties of a group |
> | Műveletek | Microsoft.Migrate/assessmentprojects/groups/updateMachines/action | Update group by adding or removing machines |
> | Műveletek | Microsoft.Migrate/assessmentprojects/groups/write | Creates a new group or updates an existing group |
> | Műveletek | Microsoft.Migrate/assessmentprojects/hypervcollectors/delete | Deletes the HyperV collector |
> | Műveletek | Microsoft.Migrate/assessmentprojects/hypervcollectors/read | Gets the properties of HyperV collector |
> | Műveletek | Microsoft.Migrate/assessmentprojects/hypervcollectors/write | Creates a new HyperV collector or updates an existing HyperV collector |
> | Műveletek | Microsoft.Migrate/assessmentprojects/importcollectors/delete | Deletes the Import collector |
> | Műveletek | Microsoft.Migrate/assessmentprojects/importcollectors/read | Gets the properties of Import collector |
> | Műveletek | Microsoft.Migrate/assessmentprojects/importcollectors/write | Creates a new Import collector or updates an existing Import collector |
> | Műveletek | Microsoft.Migrate/assessmentprojects/machines/read | Gets the properties of a machine |
> | Műveletek | Microsoft.Migrate/assessmentprojects/read | Gets the properties of assessment project |
> | Műveletek | Microsoft.Migrate/assessmentprojects/servercollectors/read | Gets the properties of Server collector |
> | Műveletek | Microsoft.Migrate/assessmentprojects/servercollectors/write | Creates a new Server collector or updates an existing Server collector |
> | Műveletek | Microsoft.Migrate/assessmentprojects/vmwarecollectors/delete | Deletes the VMware collector |
> | Műveletek | Microsoft.Migrate/assessmentprojects/vmwarecollectors/read | Gets the properties of VMware collector |
> | Műveletek | Microsoft.Migrate/assessmentprojects/vmwarecollectors/write | Creates a new VMware collector or updates an existing VMware collector |
> | Műveletek | Microsoft.Migrate/assessmentprojects/write | Creates a new assessment project or updates an existing assessment project |
> | Műveletek | Microsoft.Migrate/locations/assessmentOptions/read | Gets the assessment options which are available in the given location |
> | Műveletek | Microsoft.Migrate/locations/checknameavailability/action | Checks availability of the resource name for the given subscription in the given location |
> | Műveletek | Microsoft.Migrate/migrateprojects/DatabaseInstances/read | Gets the properties of a database instance |
> | Műveletek | Microsoft.Migrate/migrateprojects/Databases/read | Gets the properties of a database |
> | Műveletek | Microsoft.Migrate/migrateprojects/delete | Deletes a migrate project |
> | Műveletek | Microsoft.Migrate/migrateprojects/machines/read | Gets the properties of a machine |
> | Műveletek | Microsoft.Migrate/migrateprojects/MigrateEvents/Delete | Deletes a migrate event |
> | Műveletek | Microsoft.Migrate/migrateprojects/MigrateEvents/read | Gets the properties of a migrate events. |
> | Műveletek | Microsoft.Migrate/migrateprojects/read | Gets the properties of migrate project |
> | Műveletek | Microsoft.Migrate/migrateprojects/RefreshSummary/action | Refreshes the migrate project summary |
> | Műveletek | Microsoft.Migrate/migrateprojects/registerTool/action | Registers tool to a migrate project |
> | Műveletek | Microsoft.Migrate/migrateprojects/solutions/cleanupData/action | Clean up the migrate project solution data |
> | Műveletek | Microsoft.Migrate/migrateprojects/solutions/Delete | Deletes a  migrate project solution |
> | Műveletek | Microsoft.Migrate/migrateprojects/solutions/getconfig/action | Gets the migrate project solution configuration |
> | Műveletek | Microsoft.Migrate/migrateprojects/solutions/read | Gets the properties of migrate project solution |
> | Műveletek | Microsoft.Migrate/migrateprojects/solutions/write | Creates a new migrate project solution or updates an existing migrate project solution |
> | Műveletek | Microsoft.Migrate/migrateprojects/write | Creates a new migrate project or updates an existing migrate project |
> | Műveletek | Microsoft.Migrate/Operations/read | Lists operations available on Microsoft.Migrate resource provider |
> | Műveletek | Microsoft.Migrate/projects/assessments/read | Lists assessments within a project |
> | Műveletek | Microsoft.Migrate/projects/delete | Deletes the project |
> | Műveletek | Microsoft.Migrate/projects/groups/assessments/assessedmachines/read | Get the properties of an assessed machine |
> | Műveletek | Microsoft.Migrate/projects/groups/assessments/delete | Deletes an assessment |
> | Műveletek | Microsoft.Migrate/projects/groups/assessments/downloadurl/action | Downloads an assessment report's URL |
> | Műveletek | Microsoft.Migrate/projects/groups/assessments/read | Gets the properties of an assessment |
> | Műveletek | Microsoft.Migrate/projects/groups/assessments/write | Creates a new assessment or updates an existing assessment |
> | Műveletek | Microsoft.Migrate/projects/groups/delete | Deletes a group |
> | Műveletek | Microsoft.Migrate/projects/groups/read | Get the properties of a group |
> | Műveletek | Microsoft.Migrate/projects/groups/write | Creates a new group or updates an existing group |
> | Műveletek | Microsoft.Migrate/projects/keys/action | Gets shared keys for the project |
> | Műveletek | Microsoft.Migrate/projects/machines/read | Gets the properties of a machine |
> | Műveletek | Microsoft.Migrate/projects/read | Gets the properties of a project |
> | Műveletek | Microsoft.Migrate/projects/write | Creates a new project or updates an existing project |
> | Műveletek | Microsoft.Migrate/register/action | Registers Subscription with Microsoft.Migrate resource provider |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.MixedReality/register/action | Registers a subscription for the Mixed Reality resource provider. |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Create spatial anchors |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | Delete spatial anchors |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Discover nearby spatial anchors |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Get properties of spatial anchors |
> | Műveletek | Microsoft.MixedReality/spatialAnchorsAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Gets the diagnostic setting for Microsoft.MixedReality/spatialAnchorsAccounts |
> | Műveletek | Microsoft.MixedReality/spatialAnchorsAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Creates or updates the diagnostic setting for Microsoft.MixedReality/spatialAnchorsAccounts |
> | Műveletek | Microsoft.MixedReality/spatialAnchorsAccounts/providers/Microsoft.Insights/metricDefinitions/read | Gets the available metrics for Microsoft.MixedReality/spatialAnchorsAccounts |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Locate spatial anchors |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Submit diagnostics data to help improve the quality of the Azure Spatial Anchors service |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Update spatial anchors properties |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.NetApp/locations/checkfilepathavailability/action | Check if file path is available |
> | Műveletek | Microsoft.NetApp/locations/checknameavailability/action | Check if resource name is available |
> | Műveletek | Microsoft.NetApp/locations/operationresults/read | Reads an operation result resource. |
> | Műveletek | Microsoft.NetApp/locations/read | Reads an availability check resource. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/backupPolicies/delete | Deletes a backup policy resource. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/backupPolicies/read | Reads a backup policy resource. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/backupPolicies/write | Writes a backup policy resource. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/delete | Deletes a pool resource. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/read | Reads a pool resource. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/backups/delete | Deletes a backup resource. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/backups/read | Reads a backup resource. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/backups/write | Writes a backup resource. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/break/action | Break volume replication relations |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/delete | Deletes a volume resource. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/mountTargets/read | Reads a mount target resource. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/read | Reads a volume resource. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/ReplicationStatus/action | Reads the statuses of the Volume Replication. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/revert/action | Revert volume to specific snapshot |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete | Deletes a snapshot resource. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/read | Reads a snapshot resource. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write | Writes a snapshot resource. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write | Writes a volume resource. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/capacityPools/write | Writes a pool resource. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/delete | Deletes an account resource. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/read | Reads an account resource. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/vaults/read | Reads a vault resource. |
> | Műveletek | Microsoft.NetApp/netAppAccounts/write | Writes an account resource. |
> | Műveletek | Microsoft.NetApp/Operations/read | Reads an operation resources. |
> | Műveletek | Microsoft.NetApp/register/action | Registers Subscription with Microsoft.NetApp resource provider |
> | Műveletek | Microsoft.NetApp/unregister/action | Unregisters Subscription with Microsoft.NetApp resource provider |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Network/applicationGatewayAvailableRequestHeaders/read | Get Application Gateway available Request Headers |
> | Műveletek | Microsoft.Network/applicationGatewayAvailableResponseHeaders/read | Get Application Gateway available Response Header |
> | Műveletek | Microsoft.Network/applicationGatewayAvailableServerVariables/read | Get Application Gateway available Server Variables |
> | Műveletek | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | Application Gateway Ssl Predefined Policy |
> | Műveletek | Microsoft.Network/applicationGatewayAvailableSslOptions/read | Application Gateway available Ssl Options |
> | Műveletek | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | Gets Application Gateway Available Waf Rule Sets |
> | Műveletek | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Joins an application gateway backend address pool. Not Alertable. |
> | Műveletek | Microsoft.Network/applicationGateways/backendhealth/action | Gets an application gateway backend health |
> | Műveletek | Microsoft.Network/applicationGateways/delete | Deletes an application gateway |
> | Műveletek | Microsoft.Network/applicationGateways/getBackendHealthOnDemand/action | Gets an application gateway backend health on demand for given http setting and backend pool |
> | Műveletek | Microsoft.Network/applicationGateways/read | Gets an application gateway |
> | Műveletek | Microsoft.Network/applicationGateways/start/action | Starts an application gateway |
> | Műveletek | Microsoft.Network/applicationGateways/stop/action | Stops an application gateway |
> | Műveletek | Microsoft.Network/applicationGateways/write | Creates an application gateway or updates an application gateway |
> | Műveletek | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/delete | Deletes an Application Gateway WAF policy |
> | Műveletek | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/read | Gets an Application Gateway WAF policy |
> | Műveletek | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/write | Creates an Application Gateway WAF policy or updates an Application Gateway WAF policy |
> | Műveletek | Microsoft.Network/applicationSecurityGroups/delete | Deletes an Application Security Group |
> | Műveletek | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | Joins an IP Configuration to Application Security Groups. Not alertable. |
> | Műveletek | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Joins a Security Rule to Application Security Groups. Not alertable. |
> | Műveletek | Microsoft.Network/applicationSecurityGroups/listIpConfigurations/action | Lists IP Configurations in the ApplicationSecurityGroup |
> | Műveletek | Microsoft.Network/applicationSecurityGroups/read | Gets an Application Security Group ID. |
> | Műveletek | Microsoft.Network/applicationSecurityGroups/write | Creates an Application Security Group, or updates an existing Application Security Group. |
> | Műveletek | Microsoft.Network/azureFirewallFqdnTags/read | Gets Azure Firewall FQDN Tags |
> | Műveletek | Microsoft.Network/azurefirewalls/delete | Delete Azure Firewall |
> | Műveletek | Microsoft.Network/azurefirewalls/read | Get Azure Firewall |
> | Műveletek | Microsoft.Network/azurefirewalls/write | Creates or updates an Azure Firewall |
> | Műveletek | Microsoft.Network/bastionHosts/createbsl/action | Creates shareable urls for the VMs under a bastion and returns the urls |
> | Műveletek | Microsoft.Network/bastionHosts/delete | Deletes a Bastion Host |
> | Műveletek | Microsoft.Network/bastionHosts/deletebsl/action | Deletes shareable urls for the provided VMs under a bastion |
> | Műveletek | Microsoft.Network/bastionHosts/disconnectactivesessions/action | Disconnect given Active Sessions in the Bastion Host |
> | Műveletek | Microsoft.Network/bastionHosts/getactivesessions/action | Get Active Sessions in the Bastion Host |
> | Műveletek | Microsoft.Network/bastionHosts/getbsl/action | Returns the shareable urls for the specified VMs in a Bastion subnet provided their urls are created |
> | Műveletek | Microsoft.Network/bastionHosts/read | Gets a Bastion Host |
> | Műveletek | Microsoft.Network/bastionHosts/write | Create or Update a Bastion Host |
> | Műveletek | Microsoft.Network/bgpServiceCommunities/read | Get Bgp Service Communities |
> | Műveletek | Microsoft.Network/checkFrontDoorNameAvailability/action | Checks whether a Front Door name is available |
> | Műveletek | Microsoft.Network/checkTrafficManagerNameAvailability/action | Checks the availability of a Traffic Manager Relative DNS name. |
> | Műveletek | Microsoft.Network/connections/delete | Deletes VirtualNetworkGatewayConnection |
> | Műveletek | Microsoft.Network/connections/read | Gets VirtualNetworkGatewayConnection |
> | Műveletek | Microsoft.Network/connections/revoke/action | Marks an Express Route Connection status as Revoked |
> | Műveletek | Microsoft.Network/connections/sharedkey/action | Get VirtualNetworkGatewayConnection SharedKey |
> | Műveletek | Microsoft.Network/connections/sharedKey/read | Gets VirtualNetworkGatewayConnection SharedKey |
> | Műveletek | Microsoft.Network/connections/sharedKey/write | Creates or updates an existing VirtualNetworkGatewayConnection SharedKey |
> | Műveletek | Microsoft.Network/connections/startpacketcapture/action | Starts a Virtual Network Gateway Connection Packet Capture. |
> | Műveletek | Microsoft.Network/connections/stoppacketcapture/action | Stops a Virtual Network Gateway Connection Packet Capture. |
> | Műveletek | Microsoft.Network/connections/vpndeviceconfigurationscript/action | Gets Vpn Device Configuration of VirtualNetworkGatewayConnection |
> | Műveletek | Microsoft.Network/connections/write | Creates or updates an existing VirtualNetworkGatewayConnection |
> | Műveletek | Microsoft.Network/ddosCustomPolicies/delete | Deletes a DDoS customized policy |
> | Műveletek | Microsoft.Network/ddosCustomPolicies/read | Gets a DDoS customized policy definition Definition |
> | Műveletek | Microsoft.Network/ddosCustomPolicies/write | Creates a DDoS customized policy or updates an existing DDoS customized policy |
> | Műveletek | Microsoft.Network/ddosProtectionPlans/delete | Deletes a DDoS Protection Plan |
> | Műveletek | Microsoft.Network/ddosProtectionPlans/join/action | Joins a DDoS Protection Plan. Not alertable. |
> | Műveletek | Microsoft.Network/ddosProtectionPlans/read | Gets a DDoS Protection Plan |
> | Műveletek | Microsoft.Network/ddosProtectionPlans/write | Creates a DDoS Protection Plan or updates a DDoS Protection Plan  |
> | Műveletek | Microsoft.Network/dnsoperationresults/read | Gets results of a DNS operation |
> | Műveletek | Microsoft.Network/dnsoperationstatuses/read | Gets status of a DNS operation  |
> | Műveletek | Microsoft.Network/dnszones/A/delete | Remove the record set of a given name and type ‘A’ from a DNS zone. |
> | Műveletek | Microsoft.Network/dnszones/A/read | Get the record set of type ‘A’, in JSON format. The record set contains a list of records as well as the TTL, tags, and etag. |
> | Műveletek | Microsoft.Network/dnszones/A/write | Create or update a record set of type ‘A’ within a DNS zone. The records specified will replace the current records in the record set. |
> | Műveletek | Microsoft.Network/dnszones/AAAA/delete | Remove the record set of a given name and type ‘AAAA’ from a DNS zone. |
> | Műveletek | Microsoft.Network/dnszones/AAAA/read | Get the record set of type ‘AAAA’, in JSON format. The record set contains a list of records as well as the TTL, tags, and etag. |
> | Műveletek | Microsoft.Network/dnszones/AAAA/write | Create or update a record set of type ‘AAAA’ within a DNS zone. The records specified will replace the current records in the record set. |
> | Műveletek | Microsoft.Network/dnszones/all/read | Gets DNS record sets across types |
> | Műveletek | Microsoft.Network/dnszones/CAA/delete | Remove the record set of a given name and type ‘CAA’ from a DNS zone. |
> | Műveletek | Microsoft.Network/dnszones/CAA/read | Get the record set of type ‘CAA’, in JSON format. The record set contains the TTL, tags, and etag. |
> | Műveletek | Microsoft.Network/dnszones/CAA/write | Create or update a record set of type ‘CAA’ within a DNS zone. The records specified will replace the current records in the record set. |
> | Műveletek | Microsoft.Network/dnszones/CNAME/delete | Remove the record set of a given name and type ‘CNAME’ from a DNS zone. |
> | Műveletek | Microsoft.Network/dnszones/CNAME/read | Get the record set of type ‘CNAME’, in JSON format. The record set contains the TTL, tags, and etag. |
> | Műveletek | Microsoft.Network/dnszones/CNAME/write | Create or update a record set of type ‘CNAME’ within a DNS zone. The records specified will replace the current records in the record set. |
> | Műveletek | Microsoft.Network/dnszones/delete | Delete the DNS zone, in JSON format. The zone properties include tags, etag, numberOfRecordSets, and maxNumberOfRecordSets. |
> | Műveletek | Microsoft.Network/dnszones/MX/delete | Remove the record set of a given name and type ‘MX’ from a DNS zone. |
> | Műveletek | Microsoft.Network/dnszones/MX/read | Get the record set of type ‘MX’, in JSON format. The record set contains a list of records as well as the TTL, tags, and etag. |
> | Műveletek | Microsoft.Network/dnszones/MX/write | Create or update a record set of type ‘MX’ within a DNS zone. The records specified will replace the current records in the record set. |
> | Műveletek | Microsoft.Network/dnszones/NS/delete | Deletes the DNS record set of type NS |
> | Műveletek | Microsoft.Network/dnszones/NS/read | Gets DNS record set of type NS |
> | Műveletek | Microsoft.Network/dnszones/NS/write | Creates or updates DNS record set of type NS |
> | Műveletek | Microsoft.Network/dnszones/PTR/delete | Remove the record set of a given name and type ‘PTR’ from a DNS zone. |
> | Műveletek | Microsoft.Network/dnszones/PTR/read | Get the record set of type ‘PTR’, in JSON format. The record set contains a list of records as well as the TTL, tags, and etag. |
> | Műveletek | Microsoft.Network/dnszones/PTR/write | Create or update a record set of type ‘PTR’ within a DNS zone. The records specified will replace the current records in the record set. |
> | Műveletek | Microsoft.Network/dnszones/read | Get the DNS zone, in JSON format. The zone properties include tags, etag, numberOfRecordSets, and maxNumberOfRecordSets. Note that this command does not retrieve the record sets contained within the zone. |
> | Műveletek | Microsoft.Network/dnszones/recordsets/read | Gets DNS record sets across types |
> | Műveletek | Microsoft.Network/dnszones/SOA/read | Gets DNS record set of type SOA |
> | Műveletek | Microsoft.Network/dnszones/SOA/write | Creates or updates DNS record set of type SOA |
> | Műveletek | Microsoft.Network/dnszones/SRV/delete | Remove the record set of a given name and type ‘SRV’ from a DNS zone. |
> | Műveletek | Microsoft.Network/dnszones/SRV/read | Get the record set of type ‘SRV’, in JSON format. The record set contains a list of records as well as the TTL, tags, and etag. |
> | Műveletek | Microsoft.Network/dnszones/SRV/write | Create or update record set of type SRV |
> | Műveletek | Microsoft.Network/dnszones/TXT/delete | Remove the record set of a given name and type ‘TXT’ from a DNS zone. |
> | Műveletek | Microsoft.Network/dnszones/TXT/read | Get the record set of type ‘TXT’, in JSON format. The record set contains a list of records as well as the TTL, tags, and etag. |
> | Műveletek | Microsoft.Network/dnszones/TXT/write | Create or update a record set of type ‘TXT’ within a DNS zone. The records specified will replace the current records in the record set. |
> | Műveletek | Microsoft.Network/dnszones/write | Create or update a DNS zone within a resource group.  Used to update the tags on a DNS zone resource. Note that this command can not be used to create or update record sets within the zone. |
> | Műveletek | Microsoft.Network/expressRouteCircuits/authorizations/delete | Deletes an ExpressRouteCircuit Authorization |
> | Műveletek | Microsoft.Network/expressRouteCircuits/authorizations/read | Gets an ExpressRouteCircuit Authorization |
> | Műveletek | Microsoft.Network/expressRouteCircuits/authorizations/write | Creates or updates an existing ExpressRouteCircuit Authorization |
> | Műveletek | Microsoft.Network/expressRouteCircuits/delete | Deletes an ExpressRouteCircuit |
> | Műveletek | Microsoft.Network/expressRouteCircuits/join/action | Joins an Express Route Circuit. Not alertable. |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/arpTables/read | Gets an ExpressRouteCircuit Peering ArpTable |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | Deletes an ExpressRouteCircuit Connection |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/connections/read | Gets an ExpressRouteCircuit Connection |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/connections/write | Creates or updates an existing ExpressRouteCircuit Connection Resource |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/delete | Deletes an ExpressRouteCircuit Peering |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/peerConnections/read | Gets Peer Express Route Circuit Connection |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/read | Gets an ExpressRouteCircuit Peering |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/routeTables/read | Gets an ExpressRouteCircuit Peering RouteTable |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/routeTablesSummary/read | Gets an ExpressRouteCircuit Peering RouteTable Summary |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/stats/read | Gets an ExpressRouteCircuit Peering Stat |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/write | Creates or updates an existing ExpressRouteCircuit Peering |
> | Műveletek | Microsoft.Network/expressRouteCircuits/read | Get an ExpressRouteCircuit |
> | Műveletek | Microsoft.Network/expressRouteCircuits/stats/read | Gets an ExpressRouteCircuit Stat |
> | Műveletek | Microsoft.Network/expressRouteCircuits/write | Creates or updates an existing ExpressRouteCircuit |
> | Műveletek | Microsoft.Network/expressRouteCrossConnections/join/action | Joins an Express Route Cross Connection. Not alertable. |
> | Műveletek | Microsoft.Network/expressRouteCrossConnections/peerings/arpTables/read | Gets an Express Route Cross Connection Peering Arp Table |
> | Műveletek | Microsoft.Network/expressRouteCrossConnections/peerings/delete | Deletes an Express Route Cross Connection Peering |
> | Műveletek | Microsoft.Network/expressRouteCrossConnections/peerings/read | Gets an Express Route Cross Connection Peering |
> | Műveletek | Microsoft.Network/expressRouteCrossConnections/peerings/routeTables/read | Gets an Express Route Cross Connection Peering Route Table |
> | Műveletek | Microsoft.Network/expressRouteCrossConnections/peerings/routeTableSummary/read | Gets an Express Route Cross Connection Peering Route Table Summary |
> | Műveletek | Microsoft.Network/expressRouteCrossConnections/peerings/write | Creates an Express Route Cross Connection Peering or Updates an existing Express Route Cross Connection Peering |
> | Műveletek | Microsoft.Network/expressRouteCrossConnections/read | Get Express Route Cross Connection |
> | Műveletek | Microsoft.Network/expressRouteGateways/expressRouteConnections/delete | Deletes an Express Route Connection |
> | Műveletek | Microsoft.Network/expressRouteGateways/expressRouteConnections/read | Gets an Express Route Connection |
> | Műveletek | Microsoft.Network/expressRouteGateways/expressRouteConnections/write | Creates an Express Route Connection or Updates an existing Express Route Connection |
> | Műveletek | Microsoft.Network/expressRouteGateways/join/action | Joins an Express Route Gateway. Not alertable. |
> | Műveletek | Microsoft.Network/expressRouteGateways/read | Get Express Route Gateway |
> | Műveletek | Microsoft.Network/expressRoutePorts/delete | Deletes ExpressRoutePorts |
> | Műveletek | Microsoft.Network/expressRoutePorts/join/action | Joins Express Route ports. Not alertable. |
> | Műveletek | Microsoft.Network/expressRoutePorts/links/read | Gets ExpressRouteLink |
> | Műveletek | Microsoft.Network/expressRoutePorts/read | Gets ExpressRoutePorts |
> | Műveletek | Microsoft.Network/expressRoutePorts/write | Creates or updates ExpressRoutePorts |
> | Műveletek | Microsoft.Network/expressRoutePortsLocations/read | Get Express Route Ports Locations |
> | Műveletek | Microsoft.Network/expressRouteServiceProviders/read | Gets Express Route Service Providers |
> | Műveletek | Microsoft.Network/firewallPolicies/delete | Deletes a Firewall Policy |
> | Műveletek | Microsoft.Network/firewallPolicies/join/action | Joins a Firewall Policy. Not alertable. |
> | Műveletek | Microsoft.Network/firewallPolicies/read | Gets a Firewall Policy |
> | Műveletek | Microsoft.Network/firewallPolicies/ruleGroups/delete | Deletes a Firewall Policy Rule Group |
> | Műveletek | Microsoft.Network/firewallPolicies/ruleGroups/read | Gets a Firewall Policy Rule Group |
> | Műveletek | Microsoft.Network/firewallPolicies/ruleGroups/write | Creates a Firewall Policy Rule Group or Updates an existing Firewall Policy Rule Group |
> | Műveletek | Microsoft.Network/firewallPolicies/write | Creates a Firewall Policy or Updates an existing Firewall Policy |
> | Műveletek | Microsoft.Network/frontDoors/backendPools/delete | Deletes a backend pool |
> | Műveletek | Microsoft.Network/frontDoors/backendPools/read | Gets a backend pool |
> | Műveletek | Microsoft.Network/frontDoors/backendPools/write | Creates or updates a backend pool |
> | Műveletek | Microsoft.Network/frontDoors/delete | Deletes a Front Door |
> | Műveletek | Microsoft.Network/frontDoors/frontendEndpoints/delete | Deletes a frontend endpoint |
> | Műveletek | Microsoft.Network/frontDoors/frontendEndpoints/disableHttps/action | Disables HTTPS on a Frontend Endpoint |
> | Műveletek | Microsoft.Network/frontDoors/frontendEndpoints/enableHttps/action | Enables HTTPS on a Frontend Endpoint |
> | Műveletek | Microsoft.Network/frontDoors/frontendEndpoints/read | Gets a frontend endpoint |
> | Műveletek | Microsoft.Network/frontDoors/frontendEndpoints/write | Creates or updates a frontend endpoint |
> | Műveletek | Microsoft.Network/frontDoors/healthProbeSettings/delete | Deletes health probe settings |
> | Műveletek | Microsoft.Network/frontDoors/healthProbeSettings/read | Gets health probe settings |
> | Műveletek | Microsoft.Network/frontDoors/healthProbeSettings/write | Creates or updates health probe settings |
> | Műveletek | Microsoft.Network/frontDoors/loadBalancingSettings/delete | Creates or updates load balancing settings |
> | Műveletek | Microsoft.Network/frontDoors/loadBalancingSettings/read | Gets load balancing settings |
> | Műveletek | Microsoft.Network/frontDoors/loadBalancingSettings/write | Creates or updates load balancing settings |
> | Műveletek | Microsoft.Network/frontDoors/purge/action | Purge cached content from a Front Door |
> | Műveletek | Microsoft.Network/frontDoors/read | Gets a Front Door |
> | Műveletek | Microsoft.Network/frontDoors/routingRules/delete | Deletes a routing rule |
> | Műveletek | Microsoft.Network/frontDoors/routingRules/read | Gets a routing rule |
> | Műveletek | Microsoft.Network/frontDoors/routingRules/write | Creates or updates a routing rule |
> | Műveletek | Microsoft.Network/frontDoors/validateCustomDomain/action | Validates a frontend endpoint for a Front Door |
> | Műveletek | Microsoft.Network/frontDoors/write | Creates or updates a Front Door |
> | Műveletek | Microsoft.Network/frontDoorWebApplicationFirewallManagedRuleSets/read | Gets Web Application Firewall Managed Rule Sets |
> | Műveletek | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/delete | Deletes a Web Application Firewall Policy |
> | Műveletek | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/join/action | Joins a Web Application Firewall Policy. Not Alertable. |
> | Műveletek | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/read | Gets a Web Application Firewall Policy |
> | Műveletek | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/write | Creates or updates a Web Application Firewall Policy |
> | Műveletek | Microsoft.Network/ipGroups/delete | Deletes an IpGroup |
> | Műveletek | Microsoft.Network/ipGroups/join/action | Joins an IpGroup. Not alertable. |
> | Műveletek | Microsoft.Network/ipGroups/read | Gets an IpGroup |
> | Műveletek | Microsoft.Network/ipGroups/write | Creates an IpGroup or Updates An Existing IpGroups |
> | Műveletek | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Joins a load balancer backend address pool. Not Alertable. |
> | Műveletek | Microsoft.Network/loadBalancers/backendAddressPools/read | Gets a load balancer backend address pool definition |
> | Műveletek | Microsoft.Network/loadBalancers/delete | Deletes a load balancer |
> | Műveletek | Microsoft.Network/loadBalancers/frontendIPConfigurations/join/action | Joins a Load Balancer Frontend IP Configuration. Not alertable. |
> | Műveletek | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | Gets a load balancer frontend IP configuration definition |
> | Műveletek | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Joins a load balancer inbound NAT pool. Not alertable. |
> | Műveletek | Microsoft.Network/loadBalancers/inboundNatPools/read | Gets a load balancer inbound nat pool definition |
> | Műveletek | Microsoft.Network/loadBalancers/inboundNatRules/delete | Deletes a load balancer inbound nat rule |
> | Műveletek | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Joins a load balancer inbound nat rule. Not Alertable. |
> | Műveletek | Microsoft.Network/loadBalancers/inboundNatRules/read | Gets a load balancer inbound nat rule definition |
> | Műveletek | Microsoft.Network/loadBalancers/inboundNatRules/write | Creates a load balancer inbound nat rule or updates an existing load balancer inbound nat rule |
> | Műveletek | Microsoft.Network/loadBalancers/loadBalancingRules/read | Gets a load balancer load balancing rule definition |
> | Műveletek | Microsoft.Network/loadBalancers/networkInterfaces/read | Gets references to all the network interfaces under a load balancer |
> | Műveletek | Microsoft.Network/loadBalancers/outboundRules/read | Gets a load balancer outbound rule definition |
> | Műveletek | Microsoft.Network/loadBalancers/probes/join/action | Allows using probes of a load balancer. For example, with this permission healthProbe property of VM scale set can reference the probe. Not alertable. |
> | Műveletek | Microsoft.Network/loadBalancers/probes/read | Gets a load balancer probe |
> | Műveletek | Microsoft.Network/loadBalancers/read | Gets a load balancer definition |
> | Műveletek | Microsoft.Network/loadBalancers/virtualMachines/read | Gets references to all the virtual machines under a load balancer |
> | Műveletek | Microsoft.Network/loadBalancers/write | Creates a load balancer or updates an existing load balancer |
> | Műveletek | Microsoft.Network/localnetworkgateways/delete | Deletes LocalNetworkGateway |
> | Műveletek | Microsoft.Network/localnetworkgateways/read | Gets LocalNetworkGateway |
> | Műveletek | Microsoft.Network/localnetworkgateways/write | Creates or updates an existing LocalNetworkGateway |
> | Műveletek | Microsoft.Network/locations/autoApprovedPrivateLinkServices/read | Gets Auto Approved Private Link Services |
> | Műveletek | Microsoft.Network/locations/availableDelegations/read | Gets Available Delegations |
> | Műveletek | Microsoft.Network/locations/availablePrivateEndpointTypes/read | Gets available Private Endpoint resources |
> | Műveletek | Microsoft.Network/locations/availableServiceAliases/read | Gets Available Service Aliases |
> | Műveletek | Microsoft.Network/locations/bareMetalTenants/action | Allocates or validates a Bare Metal Tenant |
> | Műveletek | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | Checks Accelerated Networking support |
> | Műveletek | Microsoft.Network/locations/checkDnsNameAvailability/read | Checks if dns label is available at the specified location |
> | Műveletek | Microsoft.Network/locations/checkPrivateLinkServiceVisibility/action | Checks Private Link Service Visibility |
> | Műveletek | Microsoft.Network/locations/operationResults/read | Gets operation result of an async POST or DELETE operation |
> | Műveletek | Microsoft.Network/locations/operations/read | Gets operation resource that represents status of an asynchronous operation |
> | Műveletek | Microsoft.Network/locations/serviceTags/read | Get Service Tags |
> | Műveletek | Microsoft.Network/locations/supportedVirtualMachineSizes/read | Gets supported virtual machines sizes |
> | Műveletek | Microsoft.Network/locations/usages/read | Gets the resources usage metrics |
> | Műveletek | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | Gets a list of available Virtual Network Endpoint Services |
> | Műveletek | Microsoft.Network/networkIntentPolicies/delete | Deletes an Network Intent Policy |
> | Műveletek | Microsoft.Network/networkIntentPolicies/read | Gets an Network Intent Policy Description |
> | Műveletek | Microsoft.Network/networkIntentPolicies/write | Creates an Network Intent Policy or updates an existing Network Intent Policy |
> | Műveletek | Microsoft.Network/networkInterfaces/delete | Deletes a network interface |
> | Műveletek | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | Get Network Security Groups configured On Network Interface Of The Vm |
> | Műveletek | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | Get Route Table configured On Network Interface Of The Vm |
> | Műveletek | Microsoft.Network/networkInterfaces/ipconfigurations/join/action | Joins a Network Interface IP Configuration. Not alertable. |
> | Műveletek | Microsoft.Network/networkInterfaces/ipconfigurations/read | Gets a network interface ip configuration definition.  |
> | Műveletek | Microsoft.Network/networkInterfaces/join/action | Joins a Virtual Machine to a network interface. Not Alertable. |
> | Műveletek | Microsoft.Network/networkInterfaces/loadBalancers/read | Gets all the load balancers that the network interface is part of |
> | Műveletek | Microsoft.Network/networkInterfaces/read | Gets a network interface definition.  |
> | Műveletek | Microsoft.Network/networkInterfaces/tapConfigurations/delete | Deletes a Network Interface Tap Configuration. |
> | Műveletek | Microsoft.Network/networkInterfaces/tapConfigurations/read | Gets a Network Interface Tap Configuration. |
> | Műveletek | Microsoft.Network/networkInterfaces/tapConfigurations/write | Creates a Network Interface Tap Configuration or updates an existing Network Interface Tap Configuration. |
> | Műveletek | Microsoft.Network/networkInterfaces/write | Creates a network interface or updates an existing network interface.  |
> | Műveletek | Microsoft.Network/networkProfiles/delete | Deletes a Network Profile |
> | Műveletek | Microsoft.Network/networkProfiles/read | Gets a Network Profile |
> | Műveletek | Microsoft.Network/networkProfiles/removeContainers/action | Removes Containers |
> | Műveletek | Microsoft.Network/networkProfiles/setContainers/action | Sets Containers |
> | Műveletek | Microsoft.Network/networkProfiles/setNetworkInterfaces/action | Sets Container Network Interfaces |
> | Műveletek | Microsoft.Network/networkProfiles/write | Creates or updates a Network Profile |
> | Műveletek | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | Gets a default security rule definition |
> | Műveletek | Microsoft.Network/networkSecurityGroups/delete | Deletes a network security group |
> | Műveletek | Microsoft.Network/networkSecurityGroups/join/action | Joins a network security group. Not Alertable. |
> | Műveletek | Microsoft.Network/networkSecurityGroups/read | Gets a network security group definition |
> | Műveletek | Microsoft.Network/networkSecurityGroups/securityRules/delete | Deletes a security rule |
> | Műveletek | Microsoft.Network/networkSecurityGroups/securityRules/read | Gets a security rule definition |
> | Műveletek | Microsoft.Network/networkSecurityGroups/securityRules/write | Creates a security rule or updates an existing security rule |
> | Műveletek | Microsoft.Network/networkSecurityGroups/write | Creates a network security group or updates an existing network security group |
> | Műveletek | Microsoft.Network/networkWatchers/availableProvidersList/action | Returns all available internet service providers for a specified Azure region. |
> | Műveletek | Microsoft.Network/networkWatchers/azureReachabilityReport/action | Returns the relative latency score for internet service providers from a specified location to Azure regions. |
> | Műveletek | Microsoft.Network/networkWatchers/configureFlowLog/action | Configures flow logging for a target resource. |
> | Műveletek | Microsoft.Network/networkWatchers/connectionMonitors/delete | Deletes a Connection Monitor |
> | Műveletek | Microsoft.Network/networkWatchers/connectionMonitors/query/action | Query monitoring connectivity between specified endpoints |
> | Műveletek | Microsoft.Network/networkWatchers/connectionMonitors/read | Get Connection Monitor details |
> | Műveletek | Microsoft.Network/networkWatchers/connectionMonitors/start/action | Start monitoring connectivity between specified endpoints |
> | Műveletek | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | Stop/pause monitoring connectivity between specified endpoints |
> | Műveletek | Microsoft.Network/networkWatchers/connectionMonitors/write | Creates a Connection Monitor |
> | Műveletek | Microsoft.Network/networkWatchers/connectivityCheck/action | Verifies the possibility of establishing a direct TCP connection from a virtual machine to a given endpoint including another VM or an arbitrary remote server. |
> | Műveletek | Microsoft.Network/networkWatchers/delete | Deletes a network watcher |
> | Műveletek | Microsoft.Network/networkWatchers/flowLogs/delete | Deletes a Flow Log |
> | Műveletek | Microsoft.Network/networkWatchers/flowLogs/read | Get Flow Log details |
> | Műveletek | Microsoft.Network/networkWatchers/flowLogs/write | Creates a Flow Log |
> | Műveletek | Microsoft.Network/networkWatchers/ipFlowVerify/action | Returns whether the packet is allowed or denied to or from a particular destination. |
> | Műveletek | Microsoft.Network/networkWatchers/lenses/delete | Deletes a Lens |
> | Műveletek | Microsoft.Network/networkWatchers/lenses/query/action | Query monitoring network traffic on a specified endpoint |
> | Műveletek | Microsoft.Network/networkWatchers/lenses/read | Get Lens details |
> | Műveletek | Microsoft.Network/networkWatchers/lenses/start/action | Start monitoring network traffic on a specified endpoint |
> | Műveletek | Microsoft.Network/networkWatchers/lenses/stop/action | Stop/pause monitoring network traffic on a specified endpoint |
> | Műveletek | Microsoft.Network/networkWatchers/lenses/write | Creates a Lens |
> | Műveletek | Microsoft.Network/networkWatchers/networkConfigurationDiagnostic/action | Diagnostic of network configuration. |
> | Műveletek | Microsoft.Network/networkWatchers/nextHop/action | For a specified target and destination IP address, return the next hop type and next hope IP address. |
> | Műveletek | Microsoft.Network/networkWatchers/packetCaptures/delete | Deletes a packet capture |
> | Műveletek | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Gets information about properties and status of a packet capture resource. |
> | Műveletek | Microsoft.Network/networkWatchers/packetCaptures/read | Get the packet capture definition |
> | Műveletek | Microsoft.Network/networkWatchers/packetCaptures/stop/action | Stop the running packet capture session. |
> | Műveletek | Microsoft.Network/networkWatchers/packetCaptures/write | Creates a packet capture |
> | Műveletek | Microsoft.Network/networkWatchers/pingMeshes/delete | Deletes a PingMesh |
> | Műveletek | Microsoft.Network/networkWatchers/pingMeshes/read | Get PingMesh details |
> | Műveletek | Microsoft.Network/networkWatchers/pingMeshes/start/action | Start PingMesh between specified VMs |
> | Műveletek | Microsoft.Network/networkWatchers/pingMeshes/stop/action | Stop PingMesh between specified VMs |
> | Műveletek | Microsoft.Network/networkWatchers/pingMeshes/write | Creates a PingMesh |
> | Műveletek | Microsoft.Network/networkWatchers/queryConnectionMonitors/action | Batch query monitoring connectivity between specified endpoints |
> | Műveletek | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | Gets the status of flow logging on a resource. |
> | Műveletek | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | Gets the troubleshooting result from the previously run or currently running troubleshooting operation. |
> | Műveletek | Microsoft.Network/networkWatchers/read | Get the network watcher definition |
> | Műveletek | Microsoft.Network/networkWatchers/securityGroupView/action | View the configured and effective network security group rules applied on a VM. |
> | Műveletek | Microsoft.Network/networkWatchers/topology/action | Gets a network level view of resources and their relationships in a resource group. |
> | Műveletek | Microsoft.Network/networkWatchers/troubleshoot/action | Starts troubleshooting on a Networking resource in Azure. |
> | Műveletek | Microsoft.Network/networkWatchers/write | Creates a network watcher or updates an existing network watcher |
> | Műveletek | Microsoft.Network/operations/read | Get Available Operations |
> | Műveletek | Microsoft.Network/p2sVpnGateways/delete | Deletes a P2SVpnGateway. |
> | Műveletek | Microsoft.Network/p2sVpnGateways/disconnectp2svpnconnections/action | Disconnect p2s vpn connections |
> | Műveletek | Microsoft.Network/p2sVpnGateways/generatevpnprofile/action | Generate Vpn Profile for P2SVpnGateway |
> | Műveletek | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealth/action | Gets a P2S Vpn Connection health for P2SVpnGateway |
> | Műveletek | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealthdetailed/action | Gets a P2S Vpn Connection health detailed for P2SVpnGateway |
> | Műveletek | Microsoft.Network/p2sVpnGateways/read | Gets a P2SVpnGateway. |
> | Műveletek | Microsoft.Network/p2sVpnGateways/write | Puts a P2SVpnGateway. |
> | Műveletek | Microsoft.Network/privateDnsOperationResults/read | Gets results of a Private DNS operation |
> | Műveletek | Microsoft.Network/privateDnsOperationStatuses/read | Gets status of a Private DNS operation |
> | Műveletek | Microsoft.Network/privateDnsZones/A/delete | Remove the record set of a given name and type ‘A’ from a Private DNS zone. |
> | Műveletek | Microsoft.Network/privateDnsZones/A/read | Get the record set of type ‘A’ within a Private DNS zone, in JSON format. The record set contains a list of records as well as the TTL, tags, and etag. |
> | Műveletek | Microsoft.Network/privateDnsZones/A/write | Create or update a record set of type ‘A’ within a Private DNS zone. The records specified will replace the current records in the record set. |
> | Műveletek | Microsoft.Network/privateDnsZones/AAAA/delete | Remove the record set of a given name and type ‘AAAA’ from a Private DNS zone. |
> | Műveletek | Microsoft.Network/privateDnsZones/AAAA/read | Get the record set of type ‘AAAA’ within a Private DNS zone, in JSON format. The record set contains a list of records as well as the TTL, tags, and etag. |
> | Műveletek | Microsoft.Network/privateDnsZones/AAAA/write | Create or update a record set of type ‘AAAA’ within a Private DNS zone. The records specified will replace the current records in the record set. |
> | Műveletek | Microsoft.Network/privateDnsZones/ALL/read | Gets Private DNS record sets across types |
> | Műveletek | Microsoft.Network/privateDnsZones/CNAME/delete | Remove the record set of a given name and type ‘CNAME’ from a Private DNS zone. |
> | Műveletek | Microsoft.Network/privateDnsZones/CNAME/read | Get the record set of type ‘CNAME’ within a Private DNS zone, in JSON format. |
> | Műveletek | Microsoft.Network/privateDnsZones/CNAME/write | Create or update a record set of type ‘CNAME’ within a Private DNS zone. |
> | Műveletek | Microsoft.Network/privateDnsZones/delete | Delete a Private DNS zone. |
> | Műveletek | Microsoft.Network/privateDnsZones/MX/delete | Remove the record set of a given name and type ‘MX’ from a Private DNS zone. |
> | Műveletek | Microsoft.Network/privateDnsZones/MX/read | Get the record set of type ‘MX’ within a Private DNS zone, in JSON format. The record set contains a list of records as well as the TTL, tags, and etag. |
> | Műveletek | Microsoft.Network/privateDnsZones/MX/write | Create or update a record set of type ‘MX’ within a Private DNS zone. The records specified will replace the current records in the record set. |
> | Műveletek | Microsoft.Network/privateDnsZones/PTR/delete | Remove the record set of a given name and type ‘PTR’ from a Private DNS zone. |
> | Műveletek | Microsoft.Network/privateDnsZones/PTR/read | Get the record set of type ‘PTR’ within a Private DNS zone, in JSON format. The record set contains a list of records as well as the TTL, tags, and etag. |
> | Műveletek | Microsoft.Network/privateDnsZones/PTR/write | Create or update a record set of type ‘PTR’ within a Private DNS zone. The records specified will replace the current records in the record set. |
> | Műveletek | Microsoft.Network/privateDnsZones/read | Get the Private DNS zone properties, in JSON format. Note that this command does not retrieve the virtual networks to which the Private DNS zone is linked or the record sets contained within the zone. |
> | Műveletek | Microsoft.Network/privateDnsZones/recordsets/read | Gets Private DNS record sets across types |
> | Műveletek | Microsoft.Network/privateDnsZones/SOA/read | Get the record set of type ‘SOA’ within a Private DNS zone, in JSON format. |
> | Műveletek | Microsoft.Network/privateDnsZones/SOA/write | Update a record set of type ‘SOA’ within a Private DNS zone. |
> | Műveletek | Microsoft.Network/privateDnsZones/SRV/delete | Remove the record set of a given name and type ‘SRV’ from a Private DNS zone. |
> | Műveletek | Microsoft.Network/privateDnsZones/SRV/read | Get the record set of type ‘SRV’ within a Private DNS zone, in JSON format. The record set contains a list of records as well as the TTL, tags, and etag. |
> | Műveletek | Microsoft.Network/privateDnsZones/SRV/write | Create or update a record set of type ‘SRV’ within a Private DNS zone. The records specified will replace the current records in the record set. |
> | Műveletek | Microsoft.Network/privateDnsZones/TXT/delete | Remove the record set of a given name and type ‘TXT’ from a Private DNS zone. |
> | Műveletek | Microsoft.Network/privateDnsZones/TXT/read | Get the record set of type ‘TXT’ within a Private DNS zone, in JSON format. The record set contains a list of records as well as the TTL, tags, and etag. |
> | Műveletek | Microsoft.Network/privateDnsZones/TXT/write | Create or update a record set of type ‘TXT’ within a Private DNS zone. The records specified will replace the current records in the record set. |
> | Műveletek | Microsoft.Network/privateDnsZones/virtualNetworkLinks/delete | Delete a Private DNS zone link to virtual network. |
> | Műveletek | Microsoft.Network/privateDnsZones/virtualNetworkLinks/read | Get the Private DNS zone link to virtual network properties, in JSON format. |
> | Műveletek | Microsoft.Network/privateDnsZones/virtualNetworkLinks/write | Create or update a Private DNS zone link to virtual network. |
> | Műveletek | Microsoft.Network/privateDnsZones/write | Create or update a Private DNS zone within a resource group. Note that this command cannot be used to create or update virtual network links or record sets within the zone. |
> | Műveletek | Microsoft.Network/privateEndpointRedirectMaps/read | Gets a Private Endpoint RedirectMap |
> | Műveletek | Microsoft.Network/privateEndpointRedirectMaps/write | Creates Private Endpoint RedirectMap Or Updates An Existing Private Endpoint RedirectMap |
> | Műveletek | Microsoft.Network/privateEndpoints/delete | Deletes an private endpoint resource. |
> | Műveletek | Microsoft.Network/privateEndpoints/read | Gets an private endpoint resource. |
> | Műveletek | Microsoft.Network/privateEndpoints/write | Creates a new private endpoint, or updates an existing private endpoint. |
> | Műveletek | Microsoft.Network/privateLinkServices/delete | Deletes an private link service resource. |
> | Műveletek | Microsoft.Network/privateLinkServices/privateEndpointConnections/delete | Deletes an private endpoint connection. |
> | Műveletek | Microsoft.Network/privateLinkServices/privateEndpointConnections/read | Gets an private endpoint connection definition. |
> | Műveletek | Microsoft.Network/privateLinkServices/privateEndpointConnections/write | Creates a new private endpoint connection, or updates an existing private endpoint connection. |
> | Műveletek | Microsoft.Network/privateLinkServices/read | Gets an private link service resource. |
> | Műveletek | Microsoft.Network/privateLinkServices/write | Creates a new private link service, or updates an existing private link service. |
> | Műveletek | Microsoft.Network/publicIPAddresses/delete | Deletes a public Ip address. |
> | Műveletek | Microsoft.Network/publicIPAddresses/join/action | Joins a public ip address. Not Alertable. |
> | Műveletek | Microsoft.Network/publicIPAddresses/read | Gets a public ip address definition. |
> | Műveletek | Microsoft.Network/publicIPAddresses/write | Creates a public Ip address or updates an existing public Ip address.  |
> | Műveletek | Microsoft.Network/publicIPPrefixes/delete | Deletes A Public Ip Prefix |
> | Műveletek | Microsoft.Network/publicIPPrefixes/join/action | Joins a PublicIPPrefix. Not alertable. |
> | Műveletek | Microsoft.Network/publicIPPrefixes/read | Gets a Public Ip Prefix Definition |
> | Műveletek | Microsoft.Network/publicIPPrefixes/write | Creates A Public Ip Prefix Or Updates An Existing Public Ip Prefix |
> | Műveletek | Microsoft.Network/register/action | Registers the subscription |
> | Műveletek | Microsoft.Network/routeFilters/delete | Deletes a route filter definition |
> | Műveletek | Microsoft.Network/routeFilters/join/action | Joins a route filter. Not Alertable. |
> | Műveletek | Microsoft.Network/routeFilters/read | Gets a route filter definition |
> | Műveletek | Microsoft.Network/routeFilters/routeFilterRules/delete | Deletes a route filter rule definition |
> | Műveletek | Microsoft.Network/routeFilters/routeFilterRules/read | Gets a route filter rule definition |
> | Műveletek | Microsoft.Network/routeFilters/routeFilterRules/write | Creates a route filter rule or Updates an existing route filter rule |
> | Műveletek | Microsoft.Network/routeFilters/write | Creates a route filter or Updates an existing route filter |
> | Műveletek | Microsoft.Network/routeTables/delete | Deletes a route table definition |
> | Műveletek | Microsoft.Network/routeTables/join/action | Joins a route table. Not Alertable. |
> | Műveletek | Microsoft.Network/routeTables/read | Gets a route table definition |
> | Műveletek | Microsoft.Network/routeTables/routes/delete | Deletes a route definition |
> | Műveletek | Microsoft.Network/routeTables/routes/read | Gets a route definition |
> | Műveletek | Microsoft.Network/routeTables/routes/write | Creates a route or Updates an existing route |
> | Műveletek | Microsoft.Network/routeTables/write | Creates a route table or Updates an existing route table |
> | Műveletek | Microsoft.Network/serviceEndpointPolicies/delete | Deletes a Service Endpoint Policy |
> | Műveletek | Microsoft.Network/serviceEndpointPolicies/join/action | Joins a Service Endpoint Policy. Not alertable. |
> | Műveletek | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | Joins a Subnet To Service Endpoint Policies. Not alertable. |
> | Műveletek | Microsoft.Network/serviceEndpointPolicies/read | Gets a Service Endpoint Policy Description |
> | Műveletek | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | Deletes a Service Endpoint Policy Definition |
> | Műveletek | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | Gets a Service Endpoint Policy Definition Description |
> | Műveletek | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | Creates a Service Endpoint Policy Definition or updates an existing Service Endpoint Policy Definition |
> | Műveletek | Microsoft.Network/serviceEndpointPolicies/write | Creates a Service Endpoint Policy or updates an existing Service Endpoint Policy |
> | Műveletek | Microsoft.Network/trafficManagerGeographicHierarchies/read | Gets the Traffic Manager Geographic Hierarchy containing regions which can be used with the Geographic traffic routing method |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | Deletes an Azure Endpoint from an existing Traffic Manager Profile. Traffic Manager will stop routing traffic to the deleted Azure Endpoint. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | Gets an Azure Endpoint which belongs to a Traffic Manager Profile, including all the properties of that Azure Endpoint. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | Add a new Azure Endpoint in an existing Traffic Manager Profile or update the properties of an existing Azure Endpoint in that Traffic Manager Profile. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/delete | Delete the Traffic Manager profile. All settings associated with the Traffic Manager profile will be lost, and the profile can no longer be used to route traffic. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | Deletes an External Endpoint from an existing Traffic Manager Profile. Traffic Manager will stop routing traffic to the deleted External Endpoint. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | Gets an External Endpoint which belongs to a Traffic Manager Profile, including all the properties of that External Endpoint. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | Add a new External Endpoint in an existing Traffic Manager Profile or update the properties of an existing External Endpoint in that Traffic Manager Profile. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/heatMaps/read | Gets the Traffic Manager Heat Map for the given Traffic Manager profile which contains query counts and latency data by location and source IP. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | Deletes an Nested Endpoint from an existing Traffic Manager Profile. Traffic Manager will stop routing traffic to the deleted Nested Endpoint. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | Gets an Nested Endpoint which belongs to a Traffic Manager Profile, including all the properties of that Nested Endpoint. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | Add a new Nested Endpoint in an existing Traffic Manager Profile or update the properties of an existing Nested Endpoint in that Traffic Manager Profile. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/read | Get the Traffic Manager profile configuration. This includes DNS settings, traffic routing settings, endpoint monitoring settings, and the list of endpoints routed by this Traffic Manager profile. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/write | Create a Traffic Manager profile, or modify the configuration of an existing Traffic Manager profile.<br>This includes enabling or disabling a profile and modifying DNS settings, traffic routing settings, or endpoint monitoring settings.<br>Endpoints routed by the Traffic Manager profile can be added, removed, enabled or disabled. |
> | Műveletek | Microsoft.Network/trafficManagerUserMetricsKeys/delete | Deletes the subscription-level key used for Realtime User Metrics collection. |
> | Műveletek | Microsoft.Network/trafficManagerUserMetricsKeys/read | Gets the subscription-level key used for Realtime User Metrics collection. |
> | Műveletek | Microsoft.Network/trafficManagerUserMetricsKeys/write | Creates a new subscription-level key to be used for Realtime User Metrics collection. |
> | Műveletek | Microsoft.Network/unregister/action | Unregisters the subscription |
> | Műveletek | Microsoft.Network/virtualHubs/delete | Deletes a Virtual Hub |
> | Műveletek | Microsoft.Network/virtualHubs/effectiveRoutes/action | Gets effective route configured on Virtual Hub |
> | Műveletek | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | Deletes a HubVirtualNetworkConnection |
> | Műveletek | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | Get a HubVirtualNetworkConnection |
> | Műveletek | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | Create or update a HubVirtualNetworkConnection |
> | Műveletek | Microsoft.Network/virtualHubs/read | Get a Virtual Hub |
> | Műveletek | Microsoft.Network/virtualHubs/routeTables/delete | Delete a VirtualHubRouteTableV2 |
> | Műveletek | Microsoft.Network/virtualHubs/routeTables/read | Get a VirtualHubRouteTableV2 |
> | Műveletek | Microsoft.Network/virtualHubs/routeTables/write | Create or Update a VirtualHubRouteTableV2 |
> | Műveletek | Microsoft.Network/virtualHubs/write | Create or update a Virtual Hub |
> | Műveletek | microsoft.network/virtualnetworkgateways/connections/read | Get VirtualNetworkGatewayConnection |
> | Műveletek | Microsoft.Network/virtualNetworkGateways/delete | Deletes a virtualNetworkGateway |
> | Műveletek | microsoft.network/virtualnetworkgateways/disconnectvirtualnetworkgatewayvpnconnections/action | Disconnect virtual network gateway vpn connections |
> | Műveletek | microsoft.network/virtualnetworkgateways/generatevpnclientpackage/action | Generate VpnClient package for virtualNetworkGateway |
> | Műveletek | microsoft.network/virtualnetworkgateways/generatevpnprofile/action | Generate VpnProfile package for VirtualNetworkGateway |
> | Műveletek | microsoft.network/virtualnetworkgateways/getadvertisedroutes/action | Gets virtualNetworkGateway advertised routes |
> | Műveletek | microsoft.network/virtualnetworkgateways/getbgppeerstatus/action | Gets virtualNetworkGateway bgp peer status |
> | Műveletek | microsoft.network/virtualnetworkgateways/getlearnedroutes/action | Gets virtualnetworkgateway learned routes |
> | Műveletek | microsoft.network/virtualnetworkgateways/getvpnclientconnectionhealth/action | Get Per Vpn Client Connection Health for VirtualNetworkGateway |
> | Műveletek | microsoft.network/virtualnetworkgateways/getvpnclientipsecparameters/action | Get Vpnclient Ipsec parameters for VirtualNetworkGateway P2S client. |
> | Műveletek | microsoft.network/virtualnetworkgateways/getvpnprofilepackageurl/action | Gets the URL of a pre-generated vpn client profile package |
> | Műveletek | Microsoft.Network/virtualNetworkGateways/read | Gets a VirtualNetworkGateway |
> | Műveletek | microsoft.network/virtualnetworkgateways/reset/action | Resets a virtualNetworkGateway |
> | Műveletek | microsoft.network/virtualnetworkgateways/resetvpnclientsharedkey/action | Reset Vpnclient shared key for VirtualNetworkGateway P2S client. |
> | Műveletek | microsoft.network/virtualnetworkgateways/setvpnclientipsecparameters/action | Set Vpnclient Ipsec parameters for VirtualNetworkGateway P2S client. |
> | Műveletek | microsoft.network/virtualnetworkgateways/startpacketcapture/action | Starts a Virtual Network Gateway Packet Capture. |
> | Műveletek | microsoft.network/virtualnetworkgateways/stoppacketcapture/action | Stops a Virtual Network Gateway Packet Capture. |
> | Műveletek | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | Lists Supported Vpn Devices |
> | Műveletek | Microsoft.Network/virtualNetworkGateways/write | Creates or updates a VirtualNetworkGateway |
> | Műveletek | Microsoft.Network/virtualNetworks/BastionHosts/action | Gets Bastion Host references in a Virtual Network. |
> | Műveletek | Microsoft.Network/virtualNetworks/bastionHosts/default/action | Gets Bastion Host references in a Virtual Network. |
> | Műveletek | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | Check if Ip Address is available at the specified virtual network |
> | Műveletek | Microsoft.Network/virtualNetworks/delete | Deletes a virtual network |
> | Műveletek | Microsoft.Network/virtualNetworks/join/action | Joins a virtual network. Not Alertable. |
> | Műveletek | Microsoft.Network/virtualNetworks/peer/action | Peers a virtual network with another virtual network |
> | Műveletek | Microsoft.Network/virtualNetworks/read | Get the virtual network definition |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/contextualServiceEndpointPolicies/delete | Deletes A Contextual Service Endpoint Policy |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/contextualServiceEndpointPolicies/read | Gets Contextual Service Endpoint Policies |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/contextualServiceEndpointPolicies/write | Creates a Contextual Service Endpoint Policy or updates an existing Contextual Service Endpoint Policy |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/delete | Deletes a virtual network subnet |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/join/action | Joins a virtual network. Not Alertable. |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Joins resource such as storage account or SQL database to a subnet. Not alertable. |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/prepareNetworkPolicies/action | Prepares a subnet by applying necessary Network Policies |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/read | Gets a virtual network subnet definition |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/unprepareNetworkPolicies/action | Unprepare a subnet by removing the applied Network Policies |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | Gets references to all the virtual machines in a virtual network subnet |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/write | Creates a virtual network subnet or updates an existing virtual network subnet |
> | Műveletek | Microsoft.Network/virtualNetworks/usages/read | Get the IP usages for each subnet of the virtual network |
> | Műveletek | Microsoft.Network/virtualNetworks/virtualMachines/read | Gets references to all the virtual machines in a virtual network |
> | Műveletek | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Deletes a virtual network peering |
> | Műveletek | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | Gets a virtual network peering definition |
> | Műveletek | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Creates a virtual network peering or updates an existing virtual network peering |
> | Műveletek | Microsoft.Network/virtualNetworks/write | Creates a virtual network or updates an existing virtual network |
> | Műveletek | Microsoft.Network/virtualNetworkTaps/delete | Delete Virtual Network Tap |
> | Műveletek | Microsoft.Network/virtualNetworkTaps/join/action | Joins a virtual network tap. Not Alertable. |
> | Műveletek | Microsoft.Network/virtualNetworkTaps/read | Get Virtual Network Tap |
> | Műveletek | Microsoft.Network/virtualNetworkTaps/write | Create or Update Virtual Network Tap |
> | Műveletek | Microsoft.Network/virtualRouters/delete | Deletes A VirtualRouter |
> | Műveletek | Microsoft.Network/virtualRouters/join/action | Joins A VirtualRouter. Not alertable. |
> | Műveletek | Microsoft.Network/virtualRouters/peerings/delete | Deletes A VirtualRouterPeering |
> | Műveletek | Microsoft.Network/virtualRouters/peerings/read | Gets A VirtualRouterPeering |
> | Műveletek | Microsoft.Network/virtualRouters/peerings/write | Creates A VirtualRouterPeering or Updates An Existing VirtualRouterPeering |
> | Műveletek | Microsoft.Network/virtualRouters/read | Gets A VirtualRouter |
> | Műveletek | Microsoft.Network/virtualRouters/write | Creates A VirtualRouter or Updates An Existing VirtualRouter |
> | Műveletek | Microsoft.Network/virtualWans/delete | Deletes a Virtual Wan |
> | Műveletek | Microsoft.Network/virtualwans/generateVpnProfile/action | Generate VirtualWanVpnServerConfiguration VpnProfile |
> | Műveletek | Microsoft.network/virtualWans/p2sVpnServerConfigurations/delete | Deletes a virtual Wan P2SVpnServerConfiguration |
> | Műveletek | Microsoft.Network/virtualWans/p2sVpnServerConfigurations/read | Gets a virtual Wan P2SVpnServerConfiguration |
> | Műveletek | Microsoft.network/virtualWans/p2sVpnServerConfigurations/write | Creates a virtual Wan P2SVpnServerConfiguration or updates an existing virtual Wan P2SVpnServerConfiguration |
> | Műveletek | Microsoft.Network/virtualWans/read | Get a Virtual Wan |
> | Műveletek | Microsoft.Network/virtualwans/supportedSecurityProviders/read | Gets supported VirtualWan Security Providers. |
> | Műveletek | Microsoft.Network/virtualWans/virtualHubs/read | Gets all Virtual Hubs that reference a Virtual Wan. |
> | Műveletek | Microsoft.Network/virtualwans/vpnconfiguration/action | Gets a Vpn Configuration |
> | Műveletek | Microsoft.Network/virtualwans/vpnServerConfigurations/action | Get VirtualWanVpnServerConfigurations |
> | Műveletek | Microsoft.Network/virtualWans/vpnSites/read | Gets all VPN Sites that reference a Virtual Wan. |
> | Műveletek | Microsoft.Network/virtualWans/write | Create or update a Virtual Wan |
> | Műveletek | Microsoft.Network/vpnGateways/delete | Deletes a VpnGateway. |
> | Műveletek | microsoft.network/vpngateways/listvpnconnectionshealth/action | Gets connection health for all or a subset of connections on a VpnGateway |
> | Műveletek | Microsoft.Network/vpnGateways/read | Gets a VpnGateway. |
> | Műveletek | microsoft.network/vpngateways/reset/action | Resets a VpnGateway |
> | Műveletek | microsoft.network/vpnGateways/vpnConnections/delete | Deletes a VpnConnection. |
> | Műveletek | microsoft.network/vpnGateways/vpnConnections/read | Gets a VpnConnection. |
> | Műveletek | microsoft.network/vpnGateways/vpnConnections/vpnLinkConnections/read | Gets a Vpn Link Connection |
> | Műveletek | microsoft.network/vpnGateways/vpnConnections/write | Puts a VpnConnection. |
> | Műveletek | Microsoft.Network/vpnGateways/write | Puts a VpnGateway. |
> | Műveletek | Microsoft.Network/vpnServerConfigurations/delete | Delete VpnServerConfiguration |
> | Műveletek | Microsoft.Network/vpnServerConfigurations/read | Get VpnServerConfiguration |
> | Műveletek | Microsoft.Network/vpnServerConfigurations/write | Create or Update VpnServerConfiguration |
> | Műveletek | Microsoft.Network/vpnsites/delete | Deletes a Vpn Site resource. |
> | Műveletek | Microsoft.Network/vpnsites/read | Gets a Vpn Site resource. |
> | Műveletek | microsoft.network/vpnSites/vpnSiteLinks/read | Gets a Vpn Site Link |
> | Műveletek | Microsoft.Network/vpnsites/write | Creates or updates a Vpn Site resource. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | Checks whether or not a given Namespace resource name is available within the NotificationHub service. |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | Get the list of Namespaces Authorization Rules description. |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | Delete Namespace Authorization Rule. The Default Namespace Authorization Rule cannot be deleted.  |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | Get the Connection String to the Namespace |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | Get the list of Namespaces Authorization Rules description. |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | Namespace Authorization Rule Regenerate Primary/SecondaryKey, Specify the Key that needs to be regenerated |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | Create a Namespace level Authorization Rules and update its properties. The Authorization Rules Access Rights, the Primary and Secondary Keys can be updated. |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | Checks whether or not a given NotificationHub name is available inside a Namespace. |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/Delete | Delete Namespace Resource |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | Get the list of Notification Hub Authorization Rules |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Delete Notification Hub Authorization Rules |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | Get the Connection String to the Notification Hub |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | Get the list of Notification Hub Authorization Rules |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Notification Hub Authorization Rule Regenerate Primary/SecondaryKey, Specify the Key that needs to be regenerated |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | Create Notification Hub Authorization Rules and Update its properties. The Authorization Rules Access Rights, the Primary and Secondary Keys can be updated. |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | Send a test push notification. |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | Delete Notification Hub Resource |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | Get list of Namespace metrics Resource Descriptions |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Get All Notification Hub PNS Credentials. This includes, WNS, MPNS, APNS, GCM and Baidu credentials |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | Get list of Notification Hub Resource Descriptions |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Create a Notification Hub and Update its properties. Its properties mainly include PNS Credentials. Authorization Rules and TTL |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/read | Get the list of Namespace Resource Description |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/write | Create a Namespace Resource and Update its properties. Tags and Capacity of the Namespace are the properties which can be updated. |
> | Műveletek | Microsoft.NotificationHubs/operationResults/read | Returns operation results for Notification Hubs provider |
> | Műveletek | Microsoft.NotificationHubs/operations/read | Returns a list of supported operations for Notification Hubs provider |
> | Műveletek | Microsoft.NotificationHubs/register/action | Registers the subscription for the NotificationHubs resource provider and enables the creation of Namespaces and NotificationHubs |
> | Műveletek | Microsoft.NotificationHubs/unregister/action | Unregisters the subscription for the NotificationHubs resource provider and enables the creation of Namespaces and NotificationHubs |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.OffAzure/HyperVSites/clusters/read | Gets the properties of a Hyper-V cluster |
> | Műveletek | Microsoft.OffAzure/HyperVSites/clusters/write | Creates or updates the Hyper-V cluster |
> | Műveletek | Microsoft.OffAzure/HyperVSites/delete | Deletes the Hyper-V site |
> | Műveletek | Microsoft.OffAzure/HyperVSites/healthsummary/read | Gets the health summary for Hyper-V resource |
> | Műveletek | Microsoft.OffAzure/HyperVSites/hosts/read | Gets the properties of a Hyper-V host |
> | Műveletek | Microsoft.OffAzure/HyperVSites/hosts/write | Creates or updates the Hyper-V host |
> | Műveletek | Microsoft.OffAzure/HyperVSites/jobs/read | Gets the properties of a Hyper-V jobs |
> | Műveletek | Microsoft.OffAzure/HyperVSites/machines/read | Gets the properties of a Hyper-V machines |
> | Műveletek | Microsoft.OffAzure/HyperVSites/operationsstatus/read | Gets the properties of a Hyper-V operation status |
> | Műveletek | Microsoft.OffAzure/HyperVSites/read | Gets the properties of a Hyper-V site |
> | Műveletek | Microsoft.OffAzure/HyperVSites/refresh/action | Refreshes the objects within a Hyper-V site |
> | Műveletek | Microsoft.OffAzure/HyperVSites/runasaccounts/read | Gets the properties of a Hyper-V run as accounts |
> | Műveletek | Microsoft.OffAzure/HyperVSites/usage/read | Gets the usages of a Hyper-V site |
> | Műveletek | Microsoft.OffAzure/HyperVSites/write | Creates or updates the Hyper-V site |
> | Műveletek | Microsoft.OffAzure/Operations/read | Reads the exposed operations |
> | Műveletek | Microsoft.OffAzure/register/action | Registers Subscription with Microsoft.OffAzure resource provider |
> | Műveletek | Microsoft.OffAzure/ServerSites/jobs/read | Gets the properties of a Server jobs |
> | Műveletek | Microsoft.OffAzure/ServerSites/machines/read | Gets the properties of a Server machines |
> | Műveletek | Microsoft.OffAzure/ServerSites/machines/write | Write the properties of a Server machines |
> | Műveletek | Microsoft.OffAzure/ServerSites/operationsstatus/read | Gets the properties of a Server operation status |
> | Műveletek | Microsoft.OffAzure/ServerSites/read | Gets the properties of a Server site |
> | Műveletek | Microsoft.OffAzure/ServerSites/refresh/action | Refreshes the objects within a Server site |
> | Műveletek | Microsoft.OffAzure/ServerSites/runasaccounts/read | Gets the properties of a Server run as accounts |
> | Műveletek | Microsoft.OffAzure/ServerSites/usage/read | Gets the usages of a Server site |
> | Műveletek | Microsoft.OffAzure/ServerSites/write | Creates or updates the Server site |
> | Műveletek | Microsoft.OffAzure/VMwareSites/delete | Deletes the VMware site |
> | Műveletek | Microsoft.OffAzure/VMwareSites/healthsummary/read | Gets the health summary for VMware resource |
> | Műveletek | Microsoft.OffAzure/VMwareSites/jobs/read | Gets the properties of a VMware jobs |
> | Műveletek | Microsoft.OffAzure/VMwareSites/machines/read | Gets the properties of a VMware machines |
> | Műveletek | Microsoft.OffAzure/VMwareSites/machines/start/action | Start VMware machines |
> | Műveletek | Microsoft.OffAzure/VMwareSites/machines/stop/action | Stops the VMware machines |
> | Műveletek | Microsoft.OffAzure/VMwareSites/operationsstatus/read | Gets the properties of a VMware operation status |
> | Műveletek | Microsoft.OffAzure/VMwareSites/read | Gets the properties of a VMware site |
> | Műveletek | Microsoft.OffAzure/VMwareSites/refresh/action | Refreshes the objects within a VMware site |
> | Műveletek | Microsoft.OffAzure/VMwareSites/runasaccounts/read | Gets the properties of a VMware run as accounts |
> | Műveletek | Microsoft.OffAzure/VMwareSites/usage/read | Gets the usages of a VMware site |
> | Műveletek | Microsoft.OffAzure/VMwareSites/vcenters/read | Gets the properties of a VMware vCenter |
> | Műveletek | Microsoft.OffAzure/VMwareSites/vcenters/write | Creates or updates the VMware vCenter |
> | Műveletek | Microsoft.OffAzure/VMwareSites/write | Creates or updates the VMware site |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.OperationalInsights/linkTargets/read | Lists existing accounts that are not associated with an Azure subscription. To link this Azure subscription to a workspace, use a customer id returned by this operation in the customer id property of the Create Workspace operation. |
> | Műveletek | microsoft.operationalinsights/operations/read | Lists all of the available OperationalInsights Rest API operations. |
> | Műveletek | microsoft.operationalinsights/register/action | Rergisters the subscription. |
> | Műveletek | Microsoft.OperationalInsights/register/action | Register a subscription to a resource provider. |
> | Műveletek | microsoft.operationalinsights/unregister/action | Unregisters the subscription. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/analytics/query/action | Search using new engine. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | Get search schema V2. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/api/query/action | Search using new engine. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/api/query/schema/read | Get search schema V2. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | Delete Configuration Scope |
> | Műveletek | Microsoft.OperationalInsights/workspaces/configurationScopes/read | Get Configuration Scope |
> | Műveletek | Microsoft.OperationalInsights/workspaces/configurationScopes/write | Set Configuration Scope |
> | Műveletek | Microsoft.OperationalInsights/workspaces/datasources/delete | Delete datasources under a workspace. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/datasources/read | Get datasources under a workspace. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/datasources/write | Create/Update datasources under a workspace. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/delete | Deletes a workspace. If the workspace was linked to an existing workspace at creation time then the workspace it was linked to is not deleted. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/gateways/delete | Removes a gateway configured for the workspace. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/generateregistrationcertificate/action | Generates Registration Certificate for the workspace. This Certificate is used to connect Microsoft System Center Operation Manager to the workspace. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | Disables an intelligence pack for a given workspace. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | Enables an intelligence pack for a given workspace. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | Lists all intelligence packs that are visible for a given workspace and also lists whether the pack is enabled or disabled for that workspace. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/linkedServices/delete | Delete linked services under given workspace. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/linkedServices/read | Get linked services under given workspace. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/linkedServices/write | Create/Update linked services under given workspace. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/listKeys/action | Retrieves the list keys for the workspace. These keys are used to connect Microsoft Operational Insights agents to the workspace. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/listKeys/read | Retrieves the list keys for the workspace. These keys are used to connect Microsoft Operational Insights agents to the workspace. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/managementGroups/read | Gets the names and metadata for System Center Operations Manager management groups connected to this workspace. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | Get Metric Definitions under workspace |
> | Műveletek | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | Delete the user's notification settings for the workspace. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/notificationSettings/read | Get the user's notification settings for the workspace. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/notificationSettings/write | Set the user's notification settings for the workspace. |
> | Műveletek | microsoft.operationalinsights/workspaces/operations/read | Gets the status of an OperationalInsights workspace operation. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/purge/action | Delete specified data from workspace |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesAccountLogon/read | Read data from the AADDomainServicesAccountLogon table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesAccountManagement/read | Read data from the AADDomainServicesAccountManagement table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesDirectoryServiceAccess/read | Read data from the AADDomainServicesDirectoryServiceAccess table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesLogonLogoff/read | Read data from the AADDomainServicesLogonLogoff table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesPolicyChange/read | Read data from the AADDomainServicesPolicyChange table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesPrivilegeUse/read | Read data from the AADDomainServicesPrivilegeUse table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesSystemSecurity/read | Read data from the AADDomainServicesSystemSecurity table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ADAssessmentRecommendation/read | Read data from the ADAssessmentRecommendation table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AddonAzureBackupAlerts/read | Read data from the AddonAzureBackupAlerts table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AddonAzureBackupJobs/read | Read data from the AddonAzureBackupJobs table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AddonAzureBackupPolicy/read | Read data from the AddonAzureBackupPolicy table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AddonAzureBackupProtectedInstance/read | Read data from the AddonAzureBackupProtectedInstance table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AddonAzureBackupStorage/read | Read data from the AddonAzureBackupStorage table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ADFActivityRun/read | Read data from the ADFActivityRun table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ADFPipelineRun/read | Read data from the ADFPipelineRun table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ADFTriggerRun/read | Read data from the ADFTriggerRun table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ADReplicationResult/read | Read data from the ADReplicationResult table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ADSecurityAssessmentRecommendation/read | Read data from the ADSecurityAssessmentRecommendation table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AegDeliveryFailureLogs/read | Read data from the AegDeliveryFailureLogs table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AegPublishFailureLogs/read | Read data from the AegPublishFailureLogs table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/Alert/read | Read data from the Alert table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AlertHistory/read | Read data from the AlertHistory table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AmlComputeClusterEvent/read | Read data from the AmlComputeClusterEvent table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AmlComputeClusterNodeEvent/read | Read data from the AmlComputeClusterNodeEvent table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AmlComputeJobEvent/read | Read data from the AmlComputeJobEvent table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ApiManagementGatewayLogs/read | Read data from the ApiManagementGatewayLogs table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AppCenterError/read | Read data from the AppCenterError table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ApplicationInsights/read | Read data from the ApplicationInsights table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AppPlatformLogsforSpring/read | Read data from the AppPlatformLogsforSpring table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AppPlatformSystemLogs/read | Read data from the AppPlatformSystemLogs table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AppServiceAppLogs/read | Read data from the AppServiceAppLogs table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AppServiceAuditLogs/read | Read data from the AppServiceAuditLogs table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AppServiceConsoleLogs/read | Read data from the AppServiceConsoleLogs table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AppServiceEnvironmentPlatformLogs/read | Read data from the AppServiceEnvironmentPlatformLogs table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AppServiceHTTPLogs/read | Read data from the AppServiceHTTPLogs table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AuditLogs/read | Read data from the AuditLogs table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AutoscaleEvaluationsLog/read | Read data from the AutoscaleEvaluationsLog table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AutoscaleScaleActionsLog/read | Read data from the AutoscaleScaleActionsLog table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AWSCloudTrail/read | Read data from the AWSCloudTrail table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AzureActivity/read | Read data from the AzureActivity table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AzureAssessmentRecommendation/read | Read data from the AzureAssessmentRecommendation table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/AzureMetrics/read | Read data from the AzureMetrics table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/BaiClusterEvent/read | Read data from the BaiClusterEvent table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/BaiClusterNodeEvent/read | Read data from the BaiClusterNodeEvent table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/BaiJobEvent/read | Read data from the BaiJobEvent table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/BlockchainApplicationLog/read | Read data from the BlockchainApplicationLog table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/BlockchainProxyLog/read | Read data from the BlockchainProxyLog table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/BoundPort/read | Read data from the BoundPort table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/CommonSecurityLog/read | Read data from the CommonSecurityLog table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read | Read data from the ComputerGroup table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ConfigurationChange/read | Read data from the ConfigurationChange table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ConfigurationData/read | Read data from the ConfigurationData table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ContainerImageInventory/read | Read data from the ContainerImageInventory table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ContainerInventory/read | Read data from the ContainerInventory table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ContainerLog/read | Read data from the ContainerLog table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ContainerNodeInventory/read | Read data from the ContainerNodeInventory table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ContainerRegistryLoginEvents/read | Read data from the ContainerRegistryLoginEvents table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ContainerRegistryRepositoryEvents/read | Read data from the ContainerRegistryRepositoryEvents table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ContainerServiceLog/read | Read data from the ContainerServiceLog table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/CoreAzureBackup/read | Read data from the CoreAzureBackup table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DatabricksAccounts/read | Read data from the DatabricksAccounts table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DatabricksClusters/read | Read data from the DatabricksClusters table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DatabricksDBFS/read | Read data from the DatabricksDBFS table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DatabricksInstancePools/read | Read data from the DatabricksInstancePools table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DatabricksJobs/read | Read data from the DatabricksJobs table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DatabricksNotebook/read | Read data from the DatabricksNotebook table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DatabricksSecrets/read | Read data from the DatabricksSecrets table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DatabricksSQLPermissions/read | Read data from the DatabricksSQLPermissions table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DatabricksSSH/read | Read data from the DatabricksSSH table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DatabricksTables/read | Read data from the DatabricksTables table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DatabricksWorkspace/read | Read data from the DatabricksWorkspace table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DeviceAppCrash/read | Read data from the DeviceAppCrash table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DeviceAppLaunch/read | Read data from the DeviceAppLaunch table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DeviceCalendar/read | Read data from the DeviceCalendar table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DeviceCleanup/read | Read data from the DeviceCleanup table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DeviceConnectSession/read | Read data from the DeviceConnectSession table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DeviceEtw/read | Read data from the DeviceEtw table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DeviceHardwareHealth/read | Read data from the DeviceHardwareHealth table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DeviceHealth/read | Read data from the DeviceHealth table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DeviceHeartbeat/read | Read data from the DeviceHeartbeat table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeHeartbeat/read | Read data from the DeviceSkypeHeartbeat table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeSignIn/read | Read data from the DeviceSkypeSignIn table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DeviceSleepState/read | Read data from the DeviceSleepState table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DHAppFailure/read | Read data from the DHAppFailure table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DHAppReliability/read | Read data from the DHAppReliability table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DHCPActivity/read | Read data from the DHCPActivity table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DHDriverReliability/read | Read data from the DHDriverReliability table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DHLogonFailures/read | Read data from the DHLogonFailures table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DHLogonMetrics/read | Read data from the DHLogonMetrics table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DHOSCrashData/read | Read data from the DHOSCrashData table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DHOSReliability/read | Read data from the DHOSReliability table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DHWipAppLearning/read | Read data from the DHWipAppLearning table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DnsEvents/read | Read data from the DnsEvents table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/DnsInventory/read | Read data from the DnsInventory table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ETWEvent/read | Read data from the ETWEvent table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/Event/read | Read data from the Event table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ExchangeAssessmentRecommendation/read | Read data from the ExchangeAssessmentRecommendation table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ExchangeOnlineAssessmentRecommendation/read | Read data from the ExchangeOnlineAssessmentRecommendation table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/FailedIngestion/read | Read data from the FailedIngestion table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/FunctionAppLogs/read | Read data from the FunctionAppLogs table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/Heartbeat/read | Read data from the Heartbeat table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/HuntingBookmark/read | Read data from the HuntingBookmark table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/IISAssessmentRecommendation/read | Read data from the IISAssessmentRecommendation table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/InboundConnection/read | Read data from the InboundConnection table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/InsightsMetrics/read | Read data from the InsightsMetrics table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/IntuneAuditLogs/read | Read data from the IntuneAuditLogs table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/IntuneDeviceComplianceOrg/read | Read data from the IntuneDeviceComplianceOrg table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/IntuneOperationalLogs/read | Read data from the IntuneOperationalLogs table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/KubeEvents/read | Read data from the KubeEvents table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/KubeHealth/read | Read data from the KubeHealth table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/KubeMonAgentEvents/read | Read data from the KubeMonAgentEvents table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/KubeNodeInventory/read | Read data from the KubeNodeInventory table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/KubePodInventory/read | Read data from the KubePodInventory table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/KubeServices/read | Read data from the KubeServices table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/LinuxAuditLog/read | Read data from the LinuxAuditLog table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAApplication/read | Read data from the MAApplication table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealth/read | Read data from the MAApplicationHealth table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthAlternativeVersions/read | Read data from the MAApplicationHealthAlternativeVersions table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthIssues/read | Read data from the MAApplicationHealthIssues table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstance/read | Read data from the MAApplicationInstance table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstanceReadiness/read | Read data from the MAApplicationInstanceReadiness table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAApplicationReadiness/read | Read data from the MAApplicationReadiness table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MADeploymentPlan/read | Read data from the MADeploymentPlan table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MADevice/read | Read data from the MADevice table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MADeviceNotEnrolled/read | Read data from the MADeviceNotEnrolled table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MADeviceNRT/read | Read data from the MADeviceNRT table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealth/read | Read data from the MADevicePnPHealth table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthAlternativeVersions/read | Read data from the MADevicePnPHealthAlternativeVersions table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthIssues/read | Read data from the MADevicePnPHealthIssues table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MADeviceReadiness/read | Read data from the MADeviceReadiness table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MADriverInstanceReadiness/read | Read data from the MADriverInstanceReadiness table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MADriverReadiness/read | Read data from the MADriverReadiness table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddin/read | Read data from the MAOfficeAddin table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinEntityHealth/read | Read data from the MAOfficeAddinEntityHealth table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealth/read | Read data from the MAOfficeAddinHealth table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthEventNRT/read | Read data from the MAOfficeAddinHealthEventNRT table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthIssues/read | Read data from the MAOfficeAddinHealthIssues table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstance/read | Read data from the MAOfficeAddinInstance table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstanceReadiness/read | Read data from the MAOfficeAddinInstanceReadiness table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinReadiness/read | Read data from the MAOfficeAddinReadiness table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAOfficeApp/read | Read data from the MAOfficeApp table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppCrashesNRT/read | Read data from the MAOfficeAppCrashesNRT table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppHealth/read | Read data from the MAOfficeAppHealth table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstance/read | Adatok beolvasása a MAOfficeAppInstance táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeAppInstanceHealth/olvasás | Adatok beolvasása a MAOfficeAppInstanceHealth táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeAppReadiness/olvasás | Adatok beolvasása a MAOfficeAppReadiness táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeAppSessionsNRT/olvasás | Adatok beolvasása a MAOfficeAppSessionsNRT táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeBuildInfo/olvasás | Adatok beolvasása a MAOfficeBuildInfo táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeCurrencyAssessment/olvasás | Adatok beolvasása a MAOfficeCurrencyAssessment táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeCurrencyAssessmentDailyCounts/olvasás | Adatok beolvasása a MAOfficeCurrencyAssessmentDailyCounts táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeDeploymentStatus/olvasás | Adatok beolvasása a MAOfficeDeploymentStatus táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeDeploymentStatusNRT/olvasás | Adatok beolvasása a MAOfficeDeploymentStatusNRT táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeMacroErrorNRT/olvasás | Adatok beolvasása a MAOfficeMacroErrorNRT táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeMacroGlobalHealth/olvasás | Adatok beolvasása a MAOfficeMacroGlobalHealth táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeMacroHealth/olvasás | Adatok beolvasása a MAOfficeMacroHealth táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeMacroHealthIssues/olvasás | Adatok beolvasása a MAOfficeMacroHealthIssues táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeMacroIssueInstanceReadiness/olvasás | Adatok beolvasása a MAOfficeMacroIssueInstanceReadiness táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeMacroIssueReadiness/olvasás | Adatok beolvasása a MAOfficeMacroIssueReadiness táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeMacroSummary/olvasás | Adatok beolvasása a MAOfficeMacroSummary táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeSuite/olvasás | Adatok beolvasása a MAOfficeSuite táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeSuiteInstance/olvasás | Adatok beolvasása a MAOfficeSuiteInstance táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAProposedPilotDevices/olvasás | Adatok beolvasása a MAProposedPilotDevices táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAWindowsBuildInfo/olvasás | Adatok beolvasása a MAWindowsBuildInfo táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAWindowsCurrencyAssessment/olvasás | Adatok beolvasása a MAWindowsCurrencyAssessment táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAWindowsCurrencyAssessmentDailyCounts/olvasás | Adatok beolvasása a MAWindowsCurrencyAssessmentDailyCounts táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAWindowsDeploymentStatus/olvasás | Adatok beolvasása a MAWindowsDeploymentStatus táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAWindowsDeploymentStatusNRT/olvasás | Adatok beolvasása a MAWindowsDeploymentStatusNRT táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAWindowsSysReqInstanceReadiness/olvasás | Adatok beolvasása a MAWindowsSysReqInstanceReadiness táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/McasShadowItReporting/olvasás | Adatok beolvasása a McasShadowItReporting táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MicrosoftAzureBastionAuditLogs/olvasás | Adatok beolvasása a MicrosoftAzureBastionAuditLogs táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MicrosoftDataShareReceivedSnapshotLog/olvasás | Adatok beolvasása a MicrosoftDataShareReceivedSnapshotLog táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MicrosoftDataShareSentSnapshotLog/olvasás | Adatok beolvasása a MicrosoftDataShareSentSnapshotLog táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MicrosoftDataShareShareLog/olvasás | Adatok beolvasása a MicrosoftDataShareShareLog táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MicrosoftDynamicsTelemetryPerformanceLogs/olvasás | Adatok beolvasása a MicrosoftDynamicsTelemetryPerformanceLogs táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MicrosoftDynamicsTelemetrySystemMetricsLogs/olvasás | Adatok beolvasása a MicrosoftDynamicsTelemetrySystemMetricsLogs táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MicrosoftHealthcareApisAuditLogs/olvasás | Adatok beolvasása a MicrosoftHealthcareApisAuditLogs táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MicrosoftInsightsAzureActivityLog/olvasás | Adatok beolvasása a MicrosoftInsightsAzureActivityLog táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/NetworkMonitoring/olvasás | Adatok beolvasása a NetworkMonitoring táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/OfficeActivity/olvasás | Adatok beolvasása a OfficeActivity táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/művelet/olvasás | Adatok beolvasása a műveleti táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/OutboundConnection/olvasás | Adatok beolvasása a OutboundConnection táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/Perf/olvasás | Adatok beolvasása a Perf táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/ProtectionStatus/olvasás | Adatok beolvasása a ProtectionStatus táblából |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/read | Run queries over the data in the workspace |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ReservedCommonFields/read | Read data from the ReservedCommonFields table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SCCMAssessmentRecommendation/read | Read data from the SCCMAssessmentRecommendation table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SCOMAssessmentRecommendation/read | Read data from the SCOMAssessmentRecommendation table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read | Read data from the SecurityAlert table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read | Read data from the SecurityBaseline table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SecurityBaselineSummary/read | Read data from the SecurityBaselineSummary table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SecurityDetection/read | Read data from the SecurityDetection table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SecurityEvent/read | Read data from the SecurityEvent table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SecurityIoTRawEvent/read | Read data from the SecurityIoTRawEvent table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SecurityRecommendation/read | Read data from the SecurityRecommendation table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ServiceFabricOperationalEvent/read | Read data from the ServiceFabricOperationalEvent table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableActorEvent/read | Read data from the ServiceFabricReliableActorEvent table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableServiceEvent/read | Read data from the ServiceFabricReliableServiceEvent table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SfBAssessmentRecommendation/read | Read data from the SfBAssessmentRecommendation table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SfBOnlineAssessmentRecommendation/read | Read data from the SfBOnlineAssessmentRecommendation table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SharePointOnlineAssessmentRecommendation/read | Read data from the SharePointOnlineAssessmentRecommendation table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SignalRServiceDiagnosticLogs/read | Read data from the SignalRServiceDiagnosticLogs table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SigninLogs/read | Read data from the SigninLogs table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SPAssessmentRecommendation/read | Read data from the SPAssessmentRecommendation table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SQLAssessmentRecommendation/read | Read data from the SQLAssessmentRecommendation table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SQLQueryPerformance/read | Read data from the SQLQueryPerformance table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SqlThreatProtectionLoginAudits/read | Read data from the SqlThreatProtectionLoginAudits table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SqlVulnerabilityAssessmentResult/read | Read data from the SqlVulnerabilityAssessmentResult table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/StorageBlobLogs/read | Read data from the StorageBlobLogs table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/StorageFileLogs/read | Read data from the StorageFileLogs table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/StorageQueueLogs/read | Read data from the StorageQueueLogs table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/StorageTableLogs/read | Read data from the StorageTableLogs table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SucceededIngestion/read | Read data from the SucceededIngestion table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/Syslog/read | Read data from the Syslog table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/SysmonEvent/read | Read data from the SysmonEvent table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read | Reading data from any custom log |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/ThreatIntelligenceIndicator/read | Read data from the ThreatIntelligenceIndicator table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/UAApp/read | Read data from the UAApp table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/UAComputer/read | Read data from the UAComputer table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/UAComputerRank/read | Read data from the UAComputerRank table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/UADriver/read | Read data from the UADriver table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/UADriverProblemCodes/read | Read data from the UADriverProblemCodes table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/UAFeedback/read | Read data from the UAFeedback table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/UAHardwareSecurity/read | Read data from the UAHardwareSecurity table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/UAIESiteDiscovery/read | Read data from the UAIESiteDiscovery table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/UAOfficeAddIn/read | Read data from the UAOfficeAddIn table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/UAProposedActionPlan/read | Read data from the UAProposedActionPlan table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/UASysReqIssue/read | Read data from the UASysReqIssue table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/UAUpgradedComputer/read | Read data from the UAUpgradedComputer table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/Update/read | Read data from the Update table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read | Read data from the UpdateRunProgress table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read | Read data from the UpdateSummary table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/Usage/read | Read data from the Usage table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/VMBoundPort/read | Read data from the VMBoundPort table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/VMComputer/read | Read data from the VMComputer table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/VMConnection/read | Read data from the VMConnection table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/VMProcess/read | Read data from the VMProcess table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/W3CIISLog/read | Read data from the W3CIISLog table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/WaaSDeploymentStatus/read | Read data from the WaaSDeploymentStatus table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/WaaSInsiderStatus/read | Read data from the WaaSInsiderStatus table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/WaaSUpdateStatus/read | Read data from the WaaSUpdateStatus table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/WDAVStatus/read | Read data from the WDAVStatus table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/WDAVThreat/read | Read data from the WDAVThreat table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/WindowsClientAssessmentRecommendation/read | Read data from the WindowsClientAssessmentRecommendation table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/WindowsEvent/read | Read data from the WindowsEvent table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/WindowsFirewall/read | Read data from the WindowsFirewall table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/WindowsServerAssessmentRecommendation/read | Read data from the WindowsServerAssessmentRecommendation table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/WireData/read | Read data from the WireData table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/WorkloadMonitoringPerf/read | Read data from the WorkloadMonitoringPerf table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/WUDOAggregatedStatus/read | Read data from the WUDOAggregatedStatus table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/query/WUDOStatus/read | Read data from the WUDOStatus table |
> | Műveletek | Microsoft.OperationalInsights/workspaces/read | Gets an existing workspace |
> | Műveletek | Microsoft.OperationalInsights/workspaces/regeneratesharedkey/action | Regenerates the specified workspace shared key |
> | Műveletek | microsoft.operationalinsights/workspaces/rules/read | Get all alert rules. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/savedSearches/delete | Deletes a saved search query |
> | Műveletek | Microsoft.OperationalInsights/workspaces/savedSearches/read | Gets a saved search query |
> | Műveletek | microsoft.operationalinsights/workspaces/savedsearches/results/read | Get saved searches results. Elavult |
> | Műveletek | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/delete | Delete scheduled search actions. |
> | Műveletek | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/read | Get scheduled search actions. |
> | Műveletek | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/write | Create or update scheduled search actions. |
> | Műveletek | microsoft.operationalinsights/workspaces/savedsearches/schedules/delete | Delete scheduled searches. |
> | Műveletek | microsoft.operationalinsights/workspaces/savedsearches/schedules/read | Get scheduled searches. |
> | Műveletek | microsoft.operationalinsights/workspaces/savedsearches/schedules/write | Create or update scheduled searches. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/savedSearches/write | Creates a saved search query |
> | Műveletek | Microsoft.OperationalInsights/workspaces/schema/read | Gets the search schema for the workspace.  Search schema includes the exposed fields and their types. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/search/action | Executes a search query |
> | Műveletek | microsoft.operationalinsights/workspaces/search/read | Get search results. Elavult. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Retrieves the shared keys for the workspace. These keys are used to connect Microsoft Operational Insights agents to the workspace. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Retrieves the shared keys for the workspace. These keys are used to connect Microsoft Operational Insights agents to the workspace. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | Deletes a storage configuration. This will stop Microsoft Operational Insights from reading data from the storage account. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/read | Gets a storage configuration. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/write | Creates a new storage configuration. These configurations are used to pull data from a location in an existing storage account. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/upgradetranslationfailures/read | Get Search Upgrade Translation Failure log for the workspace |
> | Műveletek | Microsoft.OperationalInsights/workspaces/usages/read | Gets usage data for a workspace including the amount of data read by the workspace. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/write | Creates a new workspace or links to an existing workspace by providing the customer id from the existing workspace. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.OperationsManagement/managementAssociations/delete | Delete existing Management Association |
> | Műveletek | Microsoft.OperationsManagement/managementAssociations/read | Get Existing Management Association |
> | Műveletek | Microsoft.OperationsManagement/managementAssociations/write | Create a new Management Association |
> | Műveletek | Microsoft.OperationsManagement/managementConfigurations/delete | Delete existing Management Configuration |
> | Műveletek | Microsoft.OperationsManagement/managementConfigurations/read | Get Existing Management Configuration |
> | Műveletek | Microsoft.OperationsManagement/managementConfigurations/write | Create a new Management Configuration |
> | Műveletek | Microsoft.OperationsManagement/register/action | Register a subscription to a resource provider. |
> | Műveletek | Microsoft.OperationsManagement/solutions/delete | Delete existing OMS solution |
> | Műveletek | Microsoft.OperationsManagement/solutions/read | Get exiting OMS solution |
> | Műveletek | Microsoft.OperationsManagement/solutions/write | Create new OMS solution |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.PolicyInsights/asyncOperationResults/read | Gets the async operation result. |
> | DataAction | Microsoft.PolicyInsights/checkDataPolicyCompliance/action | Check the compliance status of a given component against data policies. |
> | Műveletek | Microsoft.PolicyInsights/operations/read | Gets supported operations on Microsoft.PolicyInsights namespace |
> | DataAction | Microsoft.PolicyInsights/policyEvents/logDataEvents/action | Log the resource component policy events. |
> | Műveletek | Microsoft.PolicyInsights/policyEvents/queryResults/action | Query information about policy events. |
> | Műveletek | Microsoft.PolicyInsights/policyEvents/queryResults/read | Query information about policy events. |
> | Műveletek | Microsoft.PolicyInsights/policyMetadata/read | Get Policy Metadata resources. |
> | Műveletek | Microsoft.PolicyInsights/policyStates/queryResults/action | Query information about policy states. |
> | Műveletek | Microsoft.PolicyInsights/policyStates/queryResults/read | Query information about policy states. |
> | Műveletek | Microsoft.PolicyInsights/policyStates/summarize/action | Query summary information about policy latest states. |
> | Műveletek | Microsoft.PolicyInsights/policyStates/summarize/read | Query summary information about policy latest states. |
> | Műveletek | Microsoft.PolicyInsights/policyStates/triggerEvaluation/action | Triggers a new compliance evaluation for the selected scope. |
> | Műveletek | Microsoft.PolicyInsights/policyTrackedResources/queryResults/read | Query information about resources required by DeployIfNotExists policies. |
> | Műveletek | Microsoft.PolicyInsights/register/action | Registers the Microsoft Policy Insights resource provider and enables actions on it. |
> | Műveletek | Microsoft.PolicyInsights/remediations/cancel/action | Cancel in-progress Microsoft Policy remediations. |
> | Műveletek | Microsoft.PolicyInsights/remediations/delete | Delete policy remediations. |
> | Műveletek | Microsoft.PolicyInsights/remediations/listDeployments/read | Lists the deployments required by a policy remediation. |
> | Műveletek | Microsoft.PolicyInsights/remediations/read | Get policy remediations. |
> | Műveletek | Microsoft.PolicyInsights/remediations/write | Create or update Microsoft Policy remediations. |
> | Műveletek | Microsoft.PolicyInsights/unregister/action | Unregisters the Microsoft Policy Insights resource provider. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Portal/consoles/delete | Removes the Cloud Shell instance. |
> | Műveletek | Microsoft.Portal/consoles/read | Reads the Cloud Shell instance. |
> | Műveletek | Microsoft.Portal/consoles/write | Create or update a Cloud Shell instance. |
> | Műveletek | Microsoft.Portal/dashboards/delete | Removes the dashboard from the subscription. |
> | Műveletek | Microsoft.Portal/dashboards/read | Reads the dashboards for the subscription. |
> | Műveletek | Microsoft.Portal/dashboards/write | Add or modify dashboard to a subscription. |
> | Műveletek | Microsoft.Portal/register/action | Register to Portal |
> | Műveletek | Microsoft.Portal/usersettings/delete | Removes the Cloud Shell user settings. |
> | Műveletek | Microsoft.Portal/usersettings/read | Reads the Cloud Shell user settings. |
> | Műveletek | Microsoft.Portal/usersettings/write | Create or update Cloud Shell user setting. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.PowerBIDedicated/capacities/delete | Deletes the Power BI Dedicated Capacity. |
> | Műveletek | Microsoft.PowerBIDedicated/capacities/read | Retrieves the information of the specified Power BI Dedicated Capacity. |
> | Műveletek | Microsoft.PowerBIDedicated/capacities/resume/action | Resumes the Capacity. |
> | Műveletek | Microsoft.PowerBIDedicated/capacities/skus/read | Retrieve available SKU information for the capacity |
> | Műveletek | Microsoft.PowerBIDedicated/capacities/suspend/action | Suspends the Capacity. |
> | Műveletek | Microsoft.PowerBIDedicated/capacities/write | Creates or updates the specified Power BI Dedicated Capacity. |
> | Műveletek | Microsoft.PowerBIDedicated/locations/checkNameAvailability/action | Checks that given Power BI Dedicated Capacity name is valid and not in use. |
> | Műveletek | Microsoft.PowerBIDedicated/locations/operationresults/read | Retrieves the information of the specified operation result. |
> | Műveletek | Microsoft.PowerBIDedicated/locations/operationstatuses/read | Retrieves the information of the specified operation status. |
> | Műveletek | Microsoft.PowerBIDedicated/operations/read | Retrieves the information of operations |
> | Műveletek | Microsoft.PowerBIDedicated/register/action | Registers Power BI Dedicated resource provider. |
> | Műveletek | Microsoft.PowerBIDedicated/skus/read | Retrieves the information of Skus |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp is internal operation used by service |
> | Műveletek | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp is internal operation used by service |
> | Műveletek | Microsoft.RecoveryServices/Locations/backupPreValidateProtection/action |  |
> | Műveletek | Microsoft.RecoveryServices/Locations/backupProtectedItem/write | Create a backup Protected Item |
> | Műveletek | Microsoft.RecoveryServices/Locations/backupProtectedItems/read | Returns the list of all Protected Items. |
> | Műveletek | Microsoft.RecoveryServices/Locations/backupStatus/action | Check Backup Status for Recovery Services Vaults |
> | Műveletek | Microsoft.RecoveryServices/Locations/backupValidateFeatures/action | Validate Features |
> | Műveletek | Microsoft.RecoveryServices/locations/checkNameAvailability/action | Check Resource Name Availability is an API to check if resource name is available |
> | Műveletek | Microsoft.RecoveryServices/locations/operationStatus/read | Gets Operation Status for a given Operation |
> | Műveletek | Microsoft.RecoveryServices/operations/read | Operation returns the list of Operations for a Resource Provider |
> | Műveletek | Microsoft.RecoveryServices/register/action | Registers subscription for given Resource Provider |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupconfig/read | Returns Configuration for Recovery Services Vault. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupconfig/write | Updates Configuration for Recovery Services Vault. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupEncryptionConfigs/read | Gets Backup Resource Encryption Configuration. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupEncryptionConfigs/write | Updates Backup Resource Encryption Configuration |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupEngines/read | Returns all the backup management servers registered with vault. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/delete | Delete a backup Protection Intent |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Get a backup Protection Intent |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Create a backup Protection Intent |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Returns status of the operation |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/operationsStatus/read | Returns status of the operation |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Get all protectable containers |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/delete | Deletes the registered Container |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Do inquiry for workloads within a container |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Get all items in a container |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Gets result of Operation performed on Protection Container. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationsStatus/read | Gets status of Operation performed on Protection Container. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Performs Backup for Protected Item. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | Deletes Protected Item |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Gets Result of Operation Performed on Protected Items. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Returns the status of Operation performed on Protected Items. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Returns object details of the Protected Item |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Provision Instant Item Recovery for Protected Item |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Get Recovery Points for Protected Items. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Restore Recovery Points for Protected Items. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Revoke Instant Item Recovery for Protected Item |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Create a backup Protected Item |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Returns all registered containers |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Creates a registered container |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Refreshes the container list |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | Cancel the Job |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Returns the Result of Job Operation. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupJobs/operationsStatus/read | Returns the status of Job Operation. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupJobs/read | Returns all Job Objects |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Export Jobs |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Returns Backup Operation Result for Recovery Services Vault. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupOperations/read | Returns Backup Operation Status for Recovery Services Vault. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupPolicies/delete | Delete a Protection Policy |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Get Results of Policy Operation. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Get Status of Policy Operation. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Returns all Protection Policies |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Creates Protection Policy |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | Returns list of all Protectable Items. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Returns the list of all Protected Items. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Returns all containers belonging to the subscription |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | List all backup Protection Intents |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/action | Returns Security PIN Information for Recovery Services Vault. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Returns Storage Configuration for Recovery Services Vault. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupstorageconfig/write | Updates Storage Configuration for Recovery Services Vault. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Returns summaries for Protected Items and Protected Servers for a Recovery Services . |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Validate Operation on Protected Item |
> | Műveletek | Microsoft.RecoveryServices/Vaults/certificates/write | The Update Resource Certificate operation updates the resource/vault credential certificate. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/delete | The Delete Vault operation deletes the specified Azure resource of type 'vault' |
> | Műveletek | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | The Get Extended Info operation gets an object's Extended Info representing the Azure resource of type ?vault? |
> | Műveletek | Microsoft.RecoveryServices/Vaults/extendedInformation/read | The Get Extended Info operation gets an object's Extended Info representing the Azure resource of type ?vault? |
> | Műveletek | Microsoft.RecoveryServices/Vaults/extendedInformation/write | The Get Extended Info operation gets an object's Extended Info representing the Azure resource of type ?vault? |
> | Műveletek | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Gets the alerts for the Recovery services vault. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Resolves the alert. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | Gets the Recovery services vault notification configuration. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | Configures e-mail notifications to Recovery services vault. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/read | The Get Vault operation gets an object representing the Azure resource of type 'vault' |
> | Műveletek | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | The UnRegister Container operation can be used to unregister a container. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | The Get Operation Results operation can be used get the operation status and result for the asynchronously submitted operation |
> | Műveletek | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | The Get Containers operation can be used get the containers registered for a resource. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | The Register Service Container operation can be used to register a container with Recovery Service. |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Read any Alerts Settings |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | Create or Update any Alerts Settings |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationEvents/read | Read any Events |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Checks Consistency of the Fabric |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | Delete any Fabrics |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | Deploy Process Server Image |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/migratetoaad/action | Migrate Fabric To AAD |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/operationresults/read | Track the results of an asynchronous operation on the resource Fabrics |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Read any Fabrics |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Reassociate Gateway |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | Remove Fabric |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Renew Certificate for Fabric |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationLogicalNetworks/read | Read any Logical Networks |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Read any Networks |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | Delete any Network Mappings |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Read any Network Mappings |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | Create or Update any Network Mappings |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | Discover Protectable Item |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/operationresults/read | Track the results of an asynchronous operation on the resource Protection Containers |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Read any Protection Containers |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | Remove Protection Container |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/delete | Delete any Migration Items |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrate/action | Migrate Item |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints/read | Read any Migration Recovery Points |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/operationresults/read | Track the results of an asynchronous operation on the resource Migration Items |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/read | Read any Migration Items |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrate/action | Test Migrate |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrateCleanup/action | Test Migrate Cleanup |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/write | Create or Update any Migration Items |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Read any Protectable Items |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/addDisks/action | Lemezek hozzáadása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Apply Recovery Point |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | Delete any Protected Items |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Failover Commit |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/operationresults/read | Track the results of an asynchronous operation on the resource Protected Items |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Tervezett feladatátvétel |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Read any Protected Items |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Read any Replication Recovery Points |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | Remove Protected Item |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/removeDisks/action | Remove disks |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Repair replication |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | ReProtect Protected Item |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/ResolveHealthErrors/action |  |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/action | Visszajelzés elküldése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/read | Read any Target Compute Sizes |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Feladatátvételi teszt |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Test Failover Cleanup |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Feladatátvétel |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Update Mobility Service |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | Create or Update any Protected Items |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | Delete any Protection Container Mappings |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/operationresults/read | Track the results of an asynchronous operation on the resource Protection Container Mappings |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Read any Protection Container Mappings |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | Remove Protection Container Mapping |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | Create or Update any Protection Container Mappings |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Switch Protection Container |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | Create or Update any Protection Containers |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | Delete any Recovery Services Providers |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/operationresults/read | Track the results of an asynchronous operation on the resource Recovery Services Providers |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Read any Recovery Services Providers |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Refresh Provider |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | Remove Recovery Services Provider |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | Create or Update any Recovery Services Providers |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Read any Storage Classifications |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | Delete any Storage Classification Mappings |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/operationresults/read | Track the results of an asynchronous operation on the resource Storage Classification Mappings |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Read any Storage Classification Mappings |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | Create or Update any Storage Classification Mappings |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | Delete any vCenters |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/operationresults/read | Track the results of an asynchronous operation on the resource vCenters |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Read any vCenters |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | Create or Update any vCenters |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/write | Create or Update any Fabrics |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | Cancel Job |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationJobs/operationresults/read | Track the results of an asynchronous operation on the resource Jobs |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationJobs/read | Read any Jobs |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | Restart job |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | Resume Job |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationMigrationItems/read | Read any Migration Items |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationNetworkMappings/read | Read any Network Mappings |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationNetworks/read | Read any Networks |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationOperationStatus/read | Read any Vault Replication Operation Status |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | Delete any Policies |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationPolicies/operationresults/read | Track the results of an asynchronous operation on the resource Policies |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Read any Policies |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationPolicies/write | Create or Update any Policies |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationProtectedItems/read | Read any Protected Items |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationProtectionContainerMappings/read | Read any Protection Container Mappings |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationProtectionContainers/read | Read any Protection Containers |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | Delete any Recovery Plans |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Failover Commit Recovery Plan |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/operationresults/read | Track the results of an asynchronous operation on the resource Recovery Plans |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Planned Failover Recovery Plan |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Read any Recovery Plans |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | ReProtect Recovery Plan |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Test Failover Recovery Plan |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Test Failover Cleanup Recovery Plan |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Failover Recovery Plan |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | Create or Update any Recovery Plans |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryServicesProviders/read | Read any Recovery Services Providers |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationStorageClassificationMappings/read | Read any Storage Classification Mappings |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationStorageClassifications/read | Read any Storage Classifications |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationSupportedOperatingSystems/read | Read any  |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationUsages/read | Read any Vault Replication Usages |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationVaultHealth/operationresults/read | Track the results of an asynchronous operation on the resource Vault Replication Health |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationVaultHealth/read | Read any Vault Replication Health |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationVaultHealth/refresh/action | Refresh Vault Health |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationVaultSettings/read | Read any  |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationVaultSettings/write | Create or Update any  |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationvCenters/read | Read any vCenters |
> | Műveletek | Microsoft.RecoveryServices/vaults/usages/read | Read any Vault Usages |
> | Műveletek | Microsoft.RecoveryServices/Vaults/usages/read | Returns usage details for a Recovery Services Vault. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/vaultTokens/read | The Vault Token operation can be used to get Vault Token for vault level backend operations. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/write | Create Vault operation creates an Azure resource of type 'vault' |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Relay/checkNameAvailability/action | Checks availability of namespace under given subscription. |
> | Műveletek | Microsoft.Relay/checkNamespaceAvailability/action | Checks availability of namespace under given subscription. This API is deprecated please use CheckNameAvailability instead. |
> | Műveletek | Microsoft.Relay/namespaces/authorizationRules/action | Updates Namespace Authorization Rule. This API is deprecated. Please use a PUT call to update the Namespace Authorization Rule instead.. This operation is not supported on API version 2017-04-01. |
> | Műveletek | Microsoft.Relay/namespaces/authorizationRules/delete | Delete Namespace Authorization Rule. The Default Namespace Authorization Rule cannot be deleted.  |
> | Műveletek | Microsoft.Relay/namespaces/authorizationRules/listkeys/action | Get the Connection String to the Namespace |
> | Műveletek | Microsoft.Relay/namespaces/authorizationRules/read | Get the list of Namespaces Authorization Rules description. |
> | Műveletek | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | Regenerate the Primary or Secondary key to the Resource |
> | Műveletek | Microsoft.Relay/namespaces/authorizationRules/write | Create a Namespace level Authorization Rules and update its properties. The Authorization Rules Access Rights, the Primary and Secondary Keys can be updated. |
> | Műveletek | Microsoft.Relay/namespaces/Delete | Delete Namespace Resource |
> | Műveletek | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Gets the authorization rules keys for the Disaster Recovery primary namespace |
> | Műveletek | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | Get Disaster Recovery Primary Namespace's Authorization Rules |
> | Műveletek | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | Disables Disaster Recovery and stops replicating changes from primary to secondary namespaces. |
> | Műveletek | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Checks availability of namespace alias under given subscription. |
> | Műveletek | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | Deletes the Disaster Recovery configuration associated with the namespace. This operation can only be invoked via the primary namespace. |
> | Műveletek | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | Invokes a GEO DR failover and reconfigures the namespace alias to point to the secondary namespace. |
> | Műveletek | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | Gets the Disaster Recovery configuration associated with the namespace. |
> | Műveletek | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | Creates or Updates the Disaster Recovery configuration associated with the namespace. |
> | Műveletek | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/action | Operation to update HybridConnection. This operation is not supported on API version 2017-04-01. Authorization Rules. Please use a PUT call to update Authorization Rule. |
> | Műveletek | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/delete | Operation to delete HybridConnection Authorization Rules |
> | Műveletek | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/listkeys/action | Get the Connection String to HybridConnection |
> | Műveletek | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/read |  Get the list of HybridConnection Authorization Rules |
> | Műveletek | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | Regenerate the Primary or Secondary key to the Resource |
> | Műveletek | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/write | Create HybridConnection Authorization Rules and Update its properties. The Authorization Rules Access Rights can be updated. |
> | Műveletek | Microsoft.Relay/namespaces/HybridConnections/Delete | Operation to delete HybridConnection Resource |
> | Műveletek | Microsoft.Relay/namespaces/HybridConnections/read | Get list of HybridConnection Resource Descriptions |
> | Műveletek | Microsoft.Relay/namespaces/HybridConnections/write | Create or Update HybridConnection properties. |
> | Műveletek | Microsoft.Relay/namespaces/messagingPlan/read | Gets the Messaging Plan for a namespace.<br>This API is deprecated.<br>Properties exposed via the MessagingPlan resource are moved to the (parent) Namespace resource in later API versions..<br>This operation is not supported on API version 2017-04-01. |
> | Műveletek | Microsoft.Relay/namespaces/messagingPlan/write | Updates the Messaging Plan for a namespace.<br>This API is deprecated.<br>Properties exposed via the MessagingPlan resource are moved to the (parent) Namespace resource in later API versions..<br>This operation is not supported on API version 2017-04-01. |
> | Műveletek | Microsoft.Relay/namespaces/operationresults/read | Get the status of Namespace operation |
> | Műveletek | Microsoft.Relay/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Get list of Namespace diagnostic settings Resource Descriptions |
> | Műveletek | Microsoft.Relay/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Get list of Namespace diagnostic settings Resource Descriptions |
> | Műveletek | Microsoft.Relay/namespaces/providers/Microsoft.Insights/logDefinitions/read | Get list of Namespace logs Resource Descriptions |
> | Műveletek | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Get list of Namespace metrics Resource Descriptions |
> | Műveletek | Microsoft.Relay/namespaces/read | Get the list of Namespace Resource Description |
> | Műveletek | Microsoft.Relay/namespaces/removeAcsNamepsace/action | Remove ACS namespace |
> | Műveletek | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/action | Operation to update WcfRelay. This operation is not supported on API version 2017-04-01. Authorization Rules. Please use a PUT call to update Authorization Rule. |
> | Műveletek | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/delete | Operation to delete WcfRelay Authorization Rules |
> | Műveletek | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/listkeys/action | Get the Connection String to WcfRelay |
> | Műveletek | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/read |  Get the list of WcfRelay Authorization Rules |
> | Műveletek | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/action | Regenerate the Primary or Secondary key to the Resource |
> | Műveletek | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/write | Create WcfRelay Authorization Rules and Update its properties. The Authorization Rules Access Rights can be updated. |
> | Műveletek | Microsoft.Relay/namespaces/WcfRelays/Delete | Operation to delete WcfRelay Resource |
> | Műveletek | Microsoft.Relay/namespaces/WcfRelays/read | Get list of WcfRelay Resource Descriptions |
> | Műveletek | Microsoft.Relay/namespaces/WcfRelays/write | Create or Update WcfRelay properties. |
> | Műveletek | Microsoft.Relay/namespaces/write | Create a Namespace Resource and Update its properties. Tags and Capacity of the Namespace are the properties which can be updated. |
> | Műveletek | Microsoft.Relay/operations/read | Műveletek beolvasása |
> | Műveletek | Microsoft.Relay/register/action | Registers the subscription for the Relay resource provider and enables the creation of Relay resources |
> | Műveletek | Microsoft.Relay/unregister/action | Registers the subscription for the Relay resource provider and enables the creation of Relay resources |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | Gets the availability status for the specified resource |
> | Műveletek | Microsoft.ResourceHealth/AvailabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Műveletek | Microsoft.ResourceHealth/events/read | Get Service Health Events for given subscription |
> | Műveletek | Microsoft.Resourcehealth/healthevent/action | Denotes the change in health state for the specified resource |
> | Műveletek | Microsoft.Resourcehealth/healthevent/Activated/action | Denotes the change in health state for the specified resource |
> | Műveletek | Microsoft.Resourcehealth/healthevent/InProgress/action | Denotes the change in health state for the specified resource |
> | Műveletek | Microsoft.Resourcehealth/healthevent/Pending/action | Denotes the change in health state for the specified resource |
> | Műveletek | Microsoft.Resourcehealth/healthevent/Resolved/action | Denotes the change in health state for the specified resource |
> | Műveletek | Microsoft.Resourcehealth/healthevent/Updated/action | Denotes the change in health state for the specified resource |
> | Műveletek | Microsoft.ResourceHealth/impactedResources/read | Get Impacted Resources for given subscription |
> | Műveletek | Microsoft.ResourceHealth/metadata/read | Gets Metadata |
> | Műveletek | Microsoft.ResourceHealth/Notifications/read | Receives Azure Resource Manager notifications |
> | Műveletek | Microsoft.ResourceHealth/Operations/read | Get the operations available for the Microsoft ResourceHealth |
> | Műveletek | Microsoft.ResourceHealth/register/action | Registers the subscription for the Microsoft ResourceHealth |
> | Műveletek | Microsoft.ResourceHealth/unregister/action | Unregisters the subscription for the Microsoft ResourceHealth |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Resources/calculateTemplateHash/action | Calculate the hash of provided template. |
> | Műveletek | Microsoft.Resources/checkPolicyCompliance/read | Check the compliance status of a given resource against resource policies. |
> | Műveletek | Microsoft.Resources/checkResourceName/action | Check the resource name for validity. |
> | Műveletek | Microsoft.Resources/deployments/cancel/action | Cancels a deployment. |
> | Műveletek | Microsoft.Resources/deployments/delete | Deletes a deployment. |
> | Műveletek | Microsoft.Resources/deployments/exportTemplate/action | Export template for a deployment |
> | Műveletek | Microsoft.Resources/deployments/operations/read | Gets or lists deployment operations. |
> | Műveletek | Microsoft.Resources/deployments/operationstatuses/read | Gets or lists deployment operation statuses. |
> | Műveletek | Microsoft.Resources/deployments/read | Gets or lists deployments. |
> | Műveletek | Microsoft.Resources/deployments/validate/action | Validates an deployment. |
> | Műveletek | Microsoft.Resources/deployments/whatIf/action | Predicts template deployment changes. |
> | Műveletek | Microsoft.Resources/deployments/write | Creates or updates an deployment. |
> | Műveletek | Microsoft.Resources/deploymentScripts/delete | Deletes a deployment script |
> | Műveletek | Microsoft.Resources/deploymentScripts/logs/read | Gets or lists deployment script logs |
> | Műveletek | Microsoft.Resources/deploymentScripts/read | Gets or lists deployment scripts |
> | Műveletek | Microsoft.Resources/deploymentScripts/write | Creates or updates a deployment script |
> | Műveletek | Microsoft.Resources/links/delete | Deletes a resource link. |
> | Műveletek | Microsoft.Resources/links/read | Gets or lists resource links. |
> | Műveletek | Microsoft.Resources/links/write | Creates or updates a resource link. |
> | Műveletek | Microsoft.Resources/marketplace/purchase/action | Purchases a resource from the marketplace. |
> | Műveletek | Microsoft.Resources/providers/read | Get the list of providers. |
> | Műveletek | Microsoft.Resources/resources/read | Get the list of resources based upon filters. |
> | Műveletek | Microsoft.Resources/subscriptions/locations/read | Gets the list of locations supported. |
> | Műveletek | Microsoft.Resources/subscriptions/operationresults/read | Get the subscription operation results. |
> | Műveletek | Microsoft.Resources/subscriptions/providers/read | Gets or lists resource providers. |
> | Műveletek | Microsoft.Resources/subscriptions/read | Gets the list of subscriptions. |
> | Műveletek | Microsoft.Resources/subscriptions/resourceGroups/delete | Deletes a resource group and all its resources. |
> | Műveletek | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | Gets or lists deployment operations. |
> | Műveletek | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | Gets or lists deployment operation statuses. |
> | Műveletek | Microsoft.Resources/subscriptions/resourcegroups/deployments/read | Gets or lists deployments. |
> | Műveletek | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | Creates or updates an deployment. |
> | Műveletek | Microsoft.Resources/subscriptions/resourceGroups/moveResources/action | Moves resources from one resource group to another. |
> | Műveletek | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Műveletek | Microsoft.Resources/subscriptions/resourcegroups/resources/read | Gets the resources for the resource group. |
> | Műveletek | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | Validate move of resources from one resource group to another. |
> | Műveletek | Microsoft.Resources/subscriptions/resourceGroups/write | Creates or updates a resource group. |
> | Műveletek | Microsoft.Resources/subscriptions/resources/read | Gets resources of a subscription. |
> | Műveletek | Microsoft.Resources/subscriptions/tagNames/delete | Deletes a subscription tag. |
> | Műveletek | Microsoft.Resources/subscriptions/tagNames/read | Gets or lists subscription tags. |
> | Műveletek | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | Deletes a subscription tag value. |
> | Műveletek | Microsoft.Resources/subscriptions/tagNames/tagValues/read | Gets or lists subscription tag values. |
> | Műveletek | Microsoft.Resources/subscriptions/tagNames/tagValues/write | Adds a subscription tag value. |
> | Műveletek | Microsoft.Resources/subscriptions/tagNames/write | Adds a subscription tag. |
> | Műveletek | Microsoft.Resources/tags/delete | Removes all the tags on a resource. |
> | Műveletek | Microsoft.Resources/tags/read | Gets all the tags on a resource. |
> | Műveletek | Microsoft.Resources/tags/write | Updates the tags on a resource by replacing or merging existing tags with a new set of tags, or removing existing tags. |
> | Műveletek | Microsoft.Resources/tenants/read | Gets the list of tenants. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Scheduler/jobcollections/delete | Deletes job collection. |
> | Műveletek | Microsoft.Scheduler/jobcollections/disable/action | Disables job collection. |
> | Műveletek | Microsoft.Scheduler/jobcollections/enable/action | Enables job collection. |
> | Műveletek | Microsoft.Scheduler/jobcollections/jobs/delete | Deletes job. |
> | Műveletek | Microsoft.Scheduler/jobcollections/jobs/generateLogicAppDefinition/action | Generates Logic App definition based on a Scheduler Job. |
> | Műveletek | Microsoft.Scheduler/jobcollections/jobs/jobhistories/read | Gets job history. |
> | Műveletek | Microsoft.Scheduler/jobcollections/jobs/read | Gets job. |
> | Műveletek | Microsoft.Scheduler/jobcollections/jobs/run/action | Runs job. |
> | Műveletek | Microsoft.Scheduler/jobcollections/jobs/write | Creates or updates job. |
> | Műveletek | Microsoft.Scheduler/jobcollections/read | Get Job Collection |
> | Műveletek | Microsoft.Scheduler/jobcollections/write | Creates or updates job collection. |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Search/checkNameAvailability/action | Checks availability of the service name. |
> | Műveletek | Microsoft.Search/operations/read | Lists all of the available operations of the Microsoft.Search provider. |
> | Műveletek | Microsoft.Search/register/action | Registers the subscription for the search resource provider and enables the creation of search services. |
> | Műveletek | Microsoft.Search/searchServices/createQueryKey/action | Creates the query key. |
> | Műveletek | Microsoft.Search/searchServices/delete | Deletes the search service. |
> | Műveletek | Microsoft.Search/searchServices/deleteQueryKey/delete | Deletes the query key. |
> | Műveletek | Microsoft.Search/searchServices/listAdminKeys/action | Reads the admin keys. |
> | Műveletek | Microsoft.Search/searchServices/listQueryKeys/action | Returns the list of query API keys for the given Azure Search service. |
> | Műveletek | Microsoft.Search/searchServices/listQueryKeys/read | Returns the list of query API keys for the given Azure Search service. |
> | Műveletek | Microsoft.Search/searchServices/privateEndpointConnectionProxies/delete | Deletes an existing private endpoint connection proxy |
> | Műveletek | Microsoft.Search/searchServices/privateEndpointConnectionProxies/read | Returns the list of private endpoint connection proxies or gets the properties for the specified private endpoint connection proxy |
> | Műveletek | Microsoft.Search/searchServices/privateEndpointConnectionProxies/validate/action | Validates a private endpoint connection create call from NRP side |
> | Műveletek | Microsoft.Search/searchServices/privateEndpointConnectionProxies/write | Creates a private endpoint connection proxy with the specified parameters or updates the properties or tags for the specified private endpoint connection proxy |
> | Műveletek | Microsoft.Search/searchServices/read | Reads the search service. |
> | Műveletek | Microsoft.Search/searchServices/regenerateAdminKey/action | Regenerates the admin key. |
> | Műveletek | Microsoft.Search/searchServices/start/action | Starts the search service. |
> | Műveletek | Microsoft.Search/searchServices/stop/action | Stops the search service. |
> | Műveletek | Microsoft.Search/searchServices/write | Creates or updates the search service. |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Security/adaptiveNetworkHardenings/enforce/action | Enforces the given traffic hardening rules by creating matching security rules on the given Network Security Group(s) |
> | Műveletek | Microsoft.Security/adaptiveNetworkHardenings/read | Gets Adaptive Network Hardening recommendations of an Azure protected resource |
> | Műveletek | Microsoft.Security/advancedThreatProtectionSettings/read | Gets the Advanced Threat Protection Settings for the resource |
> | Műveletek | Microsoft.Security/advancedThreatProtectionSettings/write | Updates the Advanced Threat Protection Settings for the resource |
> | Műveletek | Microsoft.Security/alerts/read | Gets all available security alerts |
> | Műveletek | Microsoft.Security/applicationWhitelistings/read | Gets the application whitelistings |
> | Műveletek | Microsoft.Security/applicationWhitelistings/write | Creates a new application whitelisting or updates an existing one |
> | Műveletek | Microsoft.Security/assessmentMetadata/read | Get available security assessment metadata on your subscription |
> | Műveletek | Microsoft.Security/assessmentMetadata/write | Create or update a security assessment metadata |
> | Műveletek | Microsoft.Security/assessments/read | Get security assessments on your subscription |
> | Műveletek | Microsoft.Security/assessments/write | Create or update security assessments on your subscription |
> | Műveletek | Microsoft.Security/complianceResults/read | Gets the compliance results for the resource |
> | Műveletek | Microsoft.Security/informationProtectionPolicies/read | Gets the information protection policies for the resource |
> | Műveletek | Microsoft.Security/informationProtectionPolicies/write | Updates the information protection policies for the resource |
> | Műveletek | Microsoft.Security/locations/alerts/activate/action | Activate a security alert |
> | Műveletek | Microsoft.Security/locations/alerts/dismiss/action | Dismiss a security alert |
> | Műveletek | Microsoft.Security/locations/alerts/read | Gets all available security alerts |
> | Műveletek | Microsoft.Security/locations/jitNetworkAccessPolicies/delete | Deletes the just-in-time network access policy |
> | Műveletek | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | Initiates a just-in-time network access policy request |
> | Műveletek | Microsoft.Security/locations/jitNetworkAccessPolicies/read | Gets the just-in-time network access policies |
> | Műveletek | Microsoft.Security/locations/jitNetworkAccessPolicies/write | Creates a new just-in-time network access policy or updates an existing one |
> | Műveletek | Microsoft.Security/locations/read | Gets the security data location |
> | Műveletek | Microsoft.Security/locations/tasks/activate/action | Activate a security recommendation |
> | Műveletek | Microsoft.Security/locations/tasks/dismiss/action | Dismiss a security recommendation |
> | Műveletek | Microsoft.Security/locations/tasks/read | Gets all available security recommendations |
> | Műveletek | Microsoft.Security/locations/tasks/resolve/action | Resolve a security recommendation |
> | Műveletek | Microsoft.Security/locations/tasks/start/action | Start a security recommendation |
> | Műveletek | Microsoft.Security/policies/read | Gets the security policy |
> | Műveletek | Microsoft.Security/policies/write | Updates the security policy |
> | Műveletek | Microsoft.Security/pricings/delete | Deletes the pricing settings for the scope |
> | Műveletek | Microsoft.Security/pricings/read | Gets the pricing settings for the scope |
> | Műveletek | Microsoft.Security/pricings/write | Updates the pricing settings for the scope |
> | Műveletek | Microsoft.Security/register/action | Registers the subscription for Azure Security Center |
> | Műveletek | Microsoft.Security/securityContacts/delete | Deletes the security contact |
> | Műveletek | Microsoft.Security/securityContacts/read | Gets the security contact |
> | Műveletek | Microsoft.Security/securityContacts/write | Updates the security contact |
> | Műveletek | Microsoft.Security/securitySolutions/delete | Deletes a security solution |
> | Műveletek | Microsoft.Security/securitySolutions/read | Gets the security solutions |
> | Műveletek | Microsoft.Security/securitySolutions/write | Creates a new security solution or updates an existing one |
> | Műveletek | Microsoft.Security/securitySolutionsReferenceData/read | Gets the security solutions reference data |
> | Műveletek | Microsoft.Security/securityStatuses/read | Gets the security health statuses for Azure resources |
> | Műveletek | Microsoft.Security/securityStatusesSummaries/read | Gets the security statuses summaries for the scope |
> | Műveletek | Microsoft.Security/settings/read | Gets the settings for the scope |
> | Műveletek | Microsoft.Security/settings/write | Updates the settings for the scope |
> | Műveletek | Microsoft.Security/tasks/read | Gets all available security recommendations |
> | Műveletek | Microsoft.Security/unregister/action | Unregisters the subscription from Azure Security Center |
> | Műveletek | Microsoft.Security/webApplicationFirewalls/delete | Deletes a web application firewall |
> | Műveletek | Microsoft.Security/webApplicationFirewalls/read | Gets the web application firewalls |
> | Műveletek | Microsoft.Security/webApplicationFirewalls/write | Creates a new web application firewall or updates an existing one |
> | Műveletek | Microsoft.Security/workspaceSettings/connect/action | Change workspace settings reconnection settings |
> | Műveletek | Microsoft.Security/workspaceSettings/delete | Deletes the workspace settings |
> | Műveletek | Microsoft.Security/workspaceSettings/read | Gets the workspace settings |
> | Műveletek | Microsoft.Security/workspaceSettings/write | Updates the workspace settings |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.SecurityGraph/diagnosticsettings/delete | Deleting a diagnostic setting |
> | Műveletek | Microsoft.SecurityGraph/diagnosticsettings/read | Reading a diagnostic setting |
> | Műveletek | Microsoft.SecurityGraph/diagnosticsettings/write | Writing a diagnostic setting |
> | Műveletek | Microsoft.SecurityGraph/diagnosticsettingscategories/read | Reading a diagnostic setting categories |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.SecurityInsights/Aggregations/read | Gets aggregated information |
> | Műveletek | Microsoft.SecurityInsights/alertRules/actions/delete | Deletes the response actions of an alert rule |
> | Műveletek | Microsoft.SecurityInsights/alertRules/actions/read | Gets the response actions of an alert rule |
> | Műveletek | Microsoft.SecurityInsights/alertRules/actions/write | Updates the response actions of an alert rule |
> | Műveletek | Microsoft.SecurityInsights/alertRules/delete | Deletes alert rules |
> | Műveletek | Microsoft.SecurityInsights/alertRules/read | Gets the alert rules |
> | Műveletek | Microsoft.SecurityInsights/alertRules/write | Updates alert rules |
> | Műveletek | Microsoft.SecurityInsights/Bookmarks/delete | Deletes bookmarks |
> | Műveletek | Microsoft.SecurityInsights/Bookmarks/expand/action | Gets related entities of an entity by a specific expansion |
> | Műveletek | Microsoft.SecurityInsights/Bookmarks/read | Gets bookmarks |
> | Műveletek | Microsoft.SecurityInsights/Bookmarks/write | Updates bookmarks |
> | Műveletek | Microsoft.SecurityInsights/cases/comments/read | Gets the case comments |
> | Műveletek | Microsoft.SecurityInsights/cases/comments/write | Creates the case comments |
> | Műveletek | Microsoft.SecurityInsights/cases/delete | Deletes a case |
> | Műveletek | Microsoft.SecurityInsights/cases/investigations/read | Gets the case investigations |
> | Műveletek | Microsoft.SecurityInsights/cases/investigations/write | Updates the metadata of a case |
> | Műveletek | Microsoft.SecurityInsights/cases/read | Gets a case |
> | Műveletek | Microsoft.SecurityInsights/cases/write | Updates a case |
> | Műveletek | Microsoft.SecurityInsights/dataConnectors/delete | Deletes a data connector |
> | Műveletek | Microsoft.SecurityInsights/dataConnectors/read | Gets the data connectors |
> | Műveletek | Microsoft.SecurityInsights/dataConnectors/write | Updates a data connector |
> | Műveletek | Microsoft.SecurityInsights/register/action | Registers the subscription to Azure Sentinel |
> | Műveletek | Microsoft.SecurityInsights/settings/read | Gets settings |
> | Műveletek | Microsoft.SecurityInsights/settings/write | Frissítések beállításai |
> | Műveletek | Microsoft.SecurityInsights/unregister/action | Unregisters the subscription from Azure Sentinel |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ServiceBus/checkNameAvailability/action | Checks availability of namespace under given subscription. |
> | Műveletek | Microsoft.ServiceBus/checkNamespaceAvailability/action | Checks availability of namespace under given subscription. This API is deprecated please use CheckNameAvailability instead. |
> | Műveletek | Microsoft.ServiceBus/locations/deleteVirtualNetworkOrSubnets/action | Deletes the VNet rules in ServiceBus Resource Provider for the specified VNet |
> | Műveletek | Microsoft.ServiceBus/namespaces/authorizationRules/action | Updates Namespace Authorization Rule. This API is deprecated. Please use a PUT call to update the Namespace Authorization Rule instead.. This operation is not supported on API version 2017-04-01. |
> | Műveletek | Microsoft.ServiceBus/namespaces/authorizationRules/delete | Delete Namespace Authorization Rule. The Default Namespace Authorization Rule cannot be deleted.  |
> | Műveletek | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | Get the Connection String to the Namespace |
> | Műveletek | Microsoft.ServiceBus/namespaces/authorizationRules/read | Get the list of Namespaces Authorization Rules description. |
> | Műveletek | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | Regenerate the Primary or Secondary key to the Resource |
> | Műveletek | Microsoft.ServiceBus/namespaces/authorizationRules/write | Create a Namespace level Authorization Rules and update its properties. The Authorization Rules Access Rights, the Primary and Secondary Keys can be updated. |
> | Műveletek | Microsoft.ServiceBus/namespaces/Delete | Delete Namespace Resource |
> | Műveletek | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Gets the authorization rules keys for the Disaster Recovery primary namespace |
> | Műveletek | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | Get Disaster Recovery Primary Namespace's Authorization Rules |
> | Műveletek | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | Disables Disaster Recovery and stops replicating changes from primary to secondary namespaces. |
> | Műveletek | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Checks availability of namespace alias under given subscription. |
> | Műveletek | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | Deletes the Disaster Recovery configuration associated with the namespace. This operation can only be invoked via the primary namespace. |
> | Műveletek | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | Invokes a GEO DR failover and reconfigures the namespace alias to point to the secondary namespace. |
> | Műveletek | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | Gets the Disaster Recovery configuration associated with the namespace. |
> | Műveletek | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | Creates or Updates the Disaster Recovery configuration associated with the namespace. |
> | Műveletek | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | Deletes the Event Grid filter associated with the namespace. |
> | Műveletek | Microsoft.ServiceBus/namespaces/eventGridFilters/read | Gets the Event Grid filter associated with the namespace. |
> | Műveletek | Microsoft.ServiceBus/namespaces/eventGridFilters/write | Creates or Updates the Event Grid filter associated with the namespace. |
> | Műveletek | Microsoft.ServiceBus/namespaces/eventhubs/read | Get list of EventHub Resource Descriptions |
> | Műveletek | Microsoft.ServiceBus/namespaces/ipFilterRules/delete | Delete IP Filter Resource |
> | Műveletek | Microsoft.ServiceBus/namespaces/ipFilterRules/read | Get IP Filter Resource |
> | Műveletek | Microsoft.ServiceBus/namespaces/ipFilterRules/write | Create IP Filter Resource |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/receive/action | Üzenetek fogadása |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/send/action | Üzenetek küldése |
> | Műveletek | Microsoft.ServiceBus/namespaces/messagingPlan/read | Gets the Messaging Plan for a namespace.<br>This API is deprecated.<br>Properties exposed via the MessagingPlan resource are moved to the (parent) Namespace resource in later API versions..<br>This operation is not supported on API version 2017-04-01. |
> | Műveletek | Microsoft.ServiceBus/namespaces/messagingPlan/write | Updates the Messaging Plan for a namespace.<br>This API is deprecated.<br>Properties exposed via the MessagingPlan resource are moved to the (parent) Namespace resource in later API versions..<br>This operation is not supported on API version 2017-04-01. |
> | Műveletek | Microsoft.ServiceBus/namespaces/migrate/action | Migrate namespace operation |
> | Műveletek | Microsoft.ServiceBus/namespaces/migrationConfigurations/delete | Deletes the Migration configuration. |
> | Műveletek | Microsoft.ServiceBus/namespaces/migrationConfigurations/read | Gets the Migration configuration which indicates the state of the migration and pending replication operations |
> | Műveletek | Microsoft.ServiceBus/namespaces/migrationConfigurations/revert/action | Reverts the standard to premium namespace migration |
> | Műveletek | Microsoft.ServiceBus/namespaces/migrationConfigurations/upgrade/action | Assigns the DNS associated with the standard namespace to the premium namespace which completes the migration and stops the syncing resources from standard to premium namespace |
> | Műveletek | Microsoft.ServiceBus/namespaces/migrationConfigurations/write | Creates or Updates Migration configuration. This will start synchronizing resources from the standard to the premium namespace |
> | Műveletek | Microsoft.ServiceBus/namespaces/networkruleset/delete | Delete VNET Rule Resource |
> | Műveletek | Microsoft.ServiceBus/namespaces/networkruleset/read | Gets NetworkRuleSet Resource |
> | Műveletek | Microsoft.ServiceBus/namespaces/networkruleset/write | Create VNET Rule Resource |
> | Műveletek | Microsoft.ServiceBus/namespaces/networkrulesets/delete | Delete VNET Rule Resource |
> | Műveletek | Microsoft.ServiceBus/namespaces/networkrulesets/read | Gets NetworkRuleSet Resource |
> | Műveletek | Microsoft.ServiceBus/namespaces/networkrulesets/write | Create VNET Rule Resource |
> | Műveletek | Microsoft.ServiceBus/namespaces/operationresults/read | Get the status of Namespace operation |
> | Műveletek | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Get list of Namespace diagnostic settings Resource Descriptions |
> | Műveletek | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Get list of Namespace diagnostic settings Resource Descriptions |
> | Műveletek | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | Get list of Namespace logs Resource Descriptions |
> | Műveletek | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Get list of Namespace metrics Resource Descriptions |
> | Műveletek | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | Operation to update Queue. This operation is not supported on API version 2017-04-01. Authorization Rules. Please use a PUT call to update Authorization Rule. |
> | Műveletek | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | Operation to delete Queue Authorization Rules |
> | Műveletek | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | Get the Connection String to Queue |
> | Műveletek | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  Get the list of Queue Authorization Rules |
> | Műveletek | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | Regenerate the Primary or Secondary key to the Resource |
> | Műveletek | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | Create Queue Authorization Rules and Update its properties. The Authorization Rules Access Rights can be updated. |
> | Műveletek | Microsoft.ServiceBus/namespaces/queues/Delete | Operation to delete Queue Resource |
> | Műveletek | Microsoft.ServiceBus/namespaces/queues/read | Get list of Queue Resource Descriptions |
> | Műveletek | Microsoft.ServiceBus/namespaces/queues/write | Create or Update Queue properties. |
> | Műveletek | Microsoft.ServiceBus/namespaces/read | Get the list of Namespace Resource Description |
> | Műveletek | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | Remove ACS namespace |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | Operation to update Topic. This operation is not supported on API version 2017-04-01. Authorization Rules. Please use a PUT call to update Authorization Rule. |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | Operation to delete Topic Authorization Rules |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | Get the Connection String to Topic |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  Get the list of Topic Authorization Rules |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | Regenerate the Primary or Secondary key to the Resource |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | Create Topic Authorization Rules and Update its properties. The Authorization Rules Access Rights can be updated. |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/Delete | Operation to delete Topic Resource |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/read | Get list of Topic Resource Descriptions |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | Operation to delete TopicSubscription Resource |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | Get list of TopicSubscription Resource Descriptions |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | Operation to delete Rule Resource |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | Get list of Rule Resource Descriptions |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/write | Create or Update Rule properties. |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | Create or Update TopicSubscription properties. |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/write | Create or Update Topic properties. |
> | Műveletek | Microsoft.ServiceBus/namespaces/virtualNetworkRules/delete | Delete VNET Rule Resource |
> | Műveletek | Microsoft.ServiceBus/namespaces/virtualNetworkRules/read | Gets VNET Rule Resource |
> | Műveletek | Microsoft.ServiceBus/namespaces/virtualNetworkRules/write | Create VNET Rule Resource |
> | Műveletek | Microsoft.ServiceBus/namespaces/write | Create a Namespace Resource and Update its properties. Tags and Capacity of the Namespace are the properties which can be updated. |
> | Műveletek | Microsoft.ServiceBus/operations/read | Műveletek beolvasása |
> | Műveletek | Microsoft. ServiceBus/regisztráció/művelet | Regisztrálja az előfizetést a ServiceBus erőforrás-szolgáltatóhoz, és lehetővé teszi a ServiceBus-erőforrások létrehozását |
> | Műveletek | Microsoft. ServiceBus/SKU/READ | SKU-erőforrások leírásának beolvasása |
> | Műveletek | Microsoft. ServiceBus/SKU/régiók/olvasás | SkuRegions-erőforrás leírásának beolvasása |
> | Műveletek | Microsoft. ServiceBus/regisztráció/művelet | Regisztrálja az előfizetést a ServiceBus erőforrás-szolgáltatóhoz, és lehetővé teszi a ServiceBus-erőforrások létrehozását |

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. ServiceFabric/fürtök/alkalmazások/törlés | Bármely alkalmazás törlése |
> | Műveletek | Microsoft. ServiceFabric/fürtök/alkalmazások/olvasás | Bármely alkalmazás beolvasása |
> | Műveletek | Microsoft. ServiceFabric/fürtök/alkalmazások/szolgáltatások/törlés | Bármely szolgáltatás törlése |
> | Műveletek | Microsoft. ServiceFabric/fürtök/alkalmazások/szolgáltatások/partíciók/olvasás | Bármely partíció beolvasása |
> | Műveletek | Microsoft. ServiceFabric/fürtök/alkalmazások/szolgáltatások/partíciók/replikák/olvasás | Bármely replika beolvasása |
> | Műveletek | Microsoft. ServiceFabric/fürtök/alkalmazások/szolgáltatások/olvasás | Bármely szolgáltatás beolvasása |
> | Műveletek | Microsoft. ServiceFabric/fürtök/alkalmazások/szolgáltatások/állapotok/olvasás | Bármely szolgáltatás állapotának beolvasása |
> | Műveletek | Microsoft. ServiceFabric/fürtök/alkalmazások/szolgáltatások/írás | Bármely szolgáltatás létrehozása vagy frissítése |
> | Műveletek | Microsoft. ServiceFabric/fürtök/alkalmazások/írás | Bármely alkalmazás létrehozása vagy frissítése |
> | Műveletek | Microsoft. ServiceFabric/fürtök/applicationTypes/delete | Bármely alkalmazás típusának törlése |
> | Műveletek | Microsoft. ServiceFabric/fürtök/applicationTypes/READ | Bármely alkalmazás típusának beolvasása |
> | Műveletek | Microsoft. ServiceFabric/fürtök/applicationTypes/verziók/törlés | Bármely alkalmazás típusú verzió törlése |
> | Műveletek | Microsoft. ServiceFabric/fürtök/applicationTypes/Versions/READ | Bármely alkalmazás típusú verzió olvasása |
> | Műveletek | Microsoft. ServiceFabric/fürtök/applicationTypes/Versions/Write | Bármely alkalmazás típusú verzió létrehozása vagy frissítése |
> | Műveletek | Microsoft. ServiceFabric/fürtök/applicationTypes/írás | Bármely alkalmazás típusának létrehozása vagy frissítése |
> | Műveletek | Microsoft. ServiceFabric/fürtök/törlés | Bármely fürt törlése |
> | Műveletek | Microsoft. ServiceFabric/fürtök/csomópontok/olvasás | Bármely csomópont beolvasása |
> | Műveletek | Microsoft. ServiceFabric/fürtök/olvasás | Bármely fürt beolvasása |
> | Műveletek | Microsoft. ServiceFabric/fürtök/állapotok/olvasás | Bármely fürt állapotának olvasása |
> | Műveletek | Microsoft. ServiceFabric/fürtök/írás | Tetszőleges fürt létrehozása vagy frissítése |
> | Műveletek | Microsoft. ServiceFabric/Locations/clusterVersions/READ | Bármely fürt verziójának beolvasása |
> | Műveletek | Microsoft. ServiceFabric/Locations/Environments/clusterVersions/READ | Adott környezethez tartozó bármely fürt verziójának beolvasása |
> | Műveletek | Microsoft. ServiceFabric/Locations/operationresults/READ | A művelet eredményeinek olvasása |
> | Műveletek | Microsoft. ServiceFabric/Locations/Operations/READ | Bármilyen művelet beolvasása hely szerint |
> | Műveletek | Microsoft. ServiceFabric/Operations/READ | Az összes elérhető művelet beolvasása |
> | Műveletek | Microsoft. ServiceFabric/regisztráció/művelet | Bármilyen művelet regisztrálása |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. SignalRService/Locations/checknameavailability/Action | Annak ellenőrzése, hogy a név elérhető-e az új szignáló szolgáltatással való használatra |
> | Műveletek | Microsoft. SignalRService/Locations/operationresults/szignáló/olvasás | Aszinkron művelet állapotának lekérdezése |
> | Műveletek | Microsoft. SignalRService/Locations/operationStatuses/operationId/READ | Aszinkron művelet állapotának lekérdezése |
> | Műveletek | Microsoft. SignalRService/Locations/használati adatok/olvasás | Az Azure Signaler szolgáltatás kvóta-használatának beolvasása |
> | Műveletek | Microsoft. SignalRService/operationresults/READ | Aszinkron művelet állapotának lekérdezése |
> | Műveletek | Microsoft. SignalRService/Operations/READ | Az Azure Signaler szolgáltatás műveleteinek listázása. |
> | Műveletek | Microsoft. SignalRService/operationstatus/READ | Aszinkron művelet állapotának lekérdezése |
> | Műveletek | Microsoft. SignalRService/regisztráció/művelet | Regisztrálja a Microsoft. SignalRService erőforrás-szolgáltatót egy előfizetéssel |
> | Műveletek | Microsoft. SignalRService/szignáló/törlés | A teljes jelző szolgáltatás törlése |
> | Műveletek | Microsoft. SignalRService/jelző/eventGridFilters/delete | Event Grid-szűrő törlése a jelzőből. |
> | Műveletek | Microsoft. SignalRService/jelző/eventGridFilters/READ | A megadott Event Grid-szűrő tulajdonságainak beolvasása vagy a megadott jelzőhöz tartozó összes Event Grid-szűrő felsorolása. |
> | Műveletek | Microsoft. SignalRService/szignáló/eventGridFilters/írás | A megadott paraméterekkel rendelkező jelzőhöz tartozó Event Grid-szűrő létrehozása vagy frissítése. |
> | Műveletek | Microsoft. SignalRService/szignáló/listkeys műveletének beolvasása/művelet | A Signaler-hozzáférési kulcsok értékének megtekintése a felügyeleti portálon vagy az API-n keresztül |
> | Műveletek | Microsoft. SignalRService/jelző/privateEndpointConnectionProxies/delete | Privát végponti kapcsolatok proxyjának törlése |
> | Műveletek | Microsoft. SignalRService/jelző/privateEndpointConnectionProxies/READ | Privát végponti összekötő proxyjának beolvasása |
> | Műveletek | Microsoft. SignalRService/szignáló/privateEndpointConnectionProxies/érvényesítés/művelet | Privát végponti kapcsolatok proxyjának ellenőrzése |
> | Műveletek | Microsoft. SignalRService/szignáló/privateEndpointConnectionProxies/írás | Privát végponti kapcsolatok proxyjának létrehozása |
> | Műveletek | Microsoft. SignalRService/jelző/privateEndpointConnections/READ | Privát végponti kapcsolatok olvasása |
> | Műveletek | Microsoft. SignalRService/szignáló/privateEndpointConnections/írás | Privát végponti kapcsolatok jóváhagyása vagy elutasítása |
> | Műveletek | Microsoft. SignalRService/jelző/privateLinkResources/READ | Az összes Signal Private link-erőforrás listázása |
> | Műveletek | Microsoft. SignalRService/szignáló/olvasás | A jelző beállításainak és konfigurációjának megtekintése a felügyeleti portálon vagy az API-n keresztül |
> | Műveletek | Microsoft. SignalRService/szignáló/regeneratekey/művelet | Change the value of SignalR access keys in the management portal or through API |
> | Műveletek | Microsoft.SignalRService/SignalR/restart/action | To restart an Azure SignalR service in the management portal or through API. There will be certain downtime. |
> | Műveletek | Microsoft.SignalRService/SignalR/write | Modify the SignalR's settings and configurations in the management portal or through API |
> | Műveletek | Microsoft.SignalRService/unregister/action | Unregisters the 'Microsoft.SignalRService' resource provider with a subscription |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Solutions/applicationDefinitions/applicationArtifacts/read | Lists application artifacts of application definition. |
> | Műveletek | Microsoft.Solutions/applicationDefinitions/delete | Removes an application definition. |
> | Műveletek | Microsoft.Solutions/applicationDefinitions/read | Retrieves a list of application definitions. |
> | Műveletek | Microsoft.Solutions/applicationDefinitions/write | Add or modify an application definition. |
> | Műveletek | Microsoft.Solutions/applications/applicationArtifacts/read | Lists application artifacts. |
> | Műveletek | Microsoft.Solutions/applications/delete | Removes an application. |
> | Műveletek | Microsoft.Solutions/applications/read | Retrieves a list of applications. |
> | Műveletek | Microsoft.Solutions/applications/refreshPermissions/action | Refreshes application permission(s). |
> | Műveletek | Microsoft.Solutions/applications/updateAccess/action | Updates application access. |
> | Műveletek | Microsoft.Solutions/applications/write | Létrehoz egy alkalmazást. |
> | Műveletek | Microsoft.Solutions/jitRequests/delete | Remove a JitRequest |
> | Műveletek | Microsoft.Solutions/jitRequests/read | Retrieves a list of JitRequests |
> | Műveletek | Microsoft.Solutions/jitRequests/write | Creates a JitRequest |
> | Műveletek | Microsoft.Solutions/locations/operationStatuses/read | Reads the operation status for the resource. |
> | Műveletek | Microsoft.Solutions/operations/read | Gets the list of operations. |
> | Műveletek | Microsoft.Solutions/register/action | Register to Solutions. |
> | Műveletek | Microsoft.Solutions/unregister/action | Unregisters from Solutions. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Sql/checkNameAvailability/action | Verify whether given server name is available for provisioning worldwide for a given subscription. |
> | Műveletek | Microsoft.Sql/instancePools/delete | Deletes an instance pool |
> | Műveletek | Microsoft.Sql/instancePools/read | Gets an instance pool |
> | Műveletek | Microsoft.Sql/instancePools/usages/read | Gets an instance pool's usage info |
> | Műveletek | Microsoft.Sql/instancePools/write | Creates or updates an instance pool |
> | Műveletek | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | Retrieve result of the extended server blob auditing policy Set operation |
> | Műveletek | Microsoft.Sql/locations/auditingSettingsOperationResults/read | A kiszolgálói blob naplózási házirendjének beállítása művelet eredményének beolvasása |
> | Műveletek | Microsoft.Sql/locations/capabilities/read | Gets the capabilities for this subscription in a given location |
> | Műveletek | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | Egy adatbázis-művelet állapotának beolvasása. |
> | Műveletek | Microsoft.Sql/locations/databaseOperationResults/read | Egy adatbázis-művelet állapotának beolvasása. |
> | Műveletek | Microsoft.Sql/locations/deletedServerAsyncOperation/read | Gets in-progress operations on deleted server |
> | Műveletek | Microsoft.Sql/locations/deletedServerOperationResults/read | Gets in-progress operations on deleted server |
> | Műveletek | Microsoft.Sql/locations/deletedServers/read | Return the list of deleted servers or gets the properties for the specified deleted server. |
> | Műveletek | Microsoft.Sql/locations/deletedServers/recover/action | Recover a deleted server |
> | Műveletek | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | Gets the azure async operation for an elastic pool async operation |
> | Műveletek | Microsoft.Sql/locations/elasticPoolOperationResults/read | Gets the result of an elastic pool operation. |
> | Műveletek | Microsoft.Sql/locations/encryptionProtectorAzureAsyncOperation/read | Gets in-progress operations on transparent data encryption encryption protector |
> | Műveletek | Microsoft.Sql/locations/encryptionProtectorOperationResults/read | Gets in-progress operations on transparent data encryption encryption protector |
> | Műveletek | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | Retrieve result of the extended server blob auditing policy Set operation |
> | Műveletek | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | Retrieve result of the extended server blob auditing policy Set operation |
> | Műveletek | Microsoft.Sql/locations/firewallRulesAzureAsyncOperation/read | Gets the status of a firewall rule operation. |
> | Műveletek | Microsoft.Sql/locations/firewallRulesOperationResults/read | Gets the status of a firewall rule operation. |
> | Műveletek | Microsoft.Sql/locations/instanceFailoverGroups/delete | Deletes an existing instance failover group. |
> | Műveletek | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | Executes planned failover in an existing instance failover group. |
> | Műveletek | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | Executes forced failover in an existing instance failover group. |
> | Műveletek | Microsoft.Sql/locations/instanceFailoverGroups/read | Returns the list of instance failover groups or gets the properties for the specified instance failover group. |
> | Műveletek | Microsoft.Sql/locations/instanceFailoverGroups/write | Creates an instance failover group with the specified parameters or updates the properties or tags for the specified instance failover group. |
> | Műveletek | Microsoft.Sql/locations/instancePoolAzureAsyncOperation/read | Gets the status of an instance pool operation |
> | Műveletek | Microsoft.Sql/locations/instancePoolOperationResults/read | Gets the result for an instance pool operation |
> | Műveletek | Microsoft.Sql/locations/interfaceEndpointProfileAzureAsyncOperation/read | Returns the details of a specific interface endpoint Azure async operation |
> | Műveletek | Microsoft.Sql/locations/interfaceEndpointProfileOperationResults/read | Returns the details of the specified interface endpoint profile operation |
> | Műveletek | Microsoft.Sql/locations/jobAgentAzureAsyncOperation/read | Gets the status of an job agent operation. |
> | Műveletek | Microsoft.Sql/locations/jobAgentOperationResults/read | Gets the result of an job agent operation. |
> | Műveletek | Microsoft.Sql/locations/longTermRetentionBackups/read | Lists the long term retention backups for every database on every server in a location |
> | Műveletek | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read | Lists the long term retention backups for every database on a server |
> | Műveletek | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | Deletes a long term retention backup |
> | Műveletek | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | Lists the long term retention backups for a database |
> | Műveletek | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | Completes managed database restore operation |
> | Műveletek | Microsoft.Sql/locations/managedInstanceEncryptionProtectorAzureAsyncOperation/read | Gets in-progress operations on transparent data encryption managed instance encryption protector |
> | Műveletek | Microsoft.Sql/locations/managedInstanceEncryptionProtectorOperationResults/read | Gets in-progress operations on transparent data encryption managed instance encryption protector |
> | Műveletek | Microsoft.Sql/locations/managedInstanceKeyAzureAsyncOperation/read | Gets in-progress operations on transparent data encryption managed instance keys |
> | Műveletek | Microsoft.Sql/locations/managedInstanceKeyOperationResults/read | Gets in-progress operations on transparent data encryption managed instance keys |
> | Műveletek | Microsoft.Sql/locations/managedInstanceLongTermRetentionPolicyAzureAsyncOperation/read | Gets the status of a long term retention policy operation for a managed database |
> | Műveletek | Microsoft.Sql/locations/managedInstanceLongTermRetentionPolicyOperationResults/read | Gets the status of a long term retention policy operation for a managed database |
> | Műveletek | Microsoft.Sql/locations/managedShortTermRetentionPolicyOperationResults/read | Gets the status of a short term retention policy operation |
> | Műveletek | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | Gets in-progress operations on managed database transparent data encryption |
> | Műveletek | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | Gets in-progress operations on managed database transparent data encryption |
> | Műveletek | Microsoft.Sql/locations/privateEndpointConnectionAzureAsyncOperation/read | Gets the result for a private endpoint connection operation |
> | Műveletek | Microsoft.Sql/locations/privateEndpointConnectionOperationResults/read | Gets the result for a private endpoint connection operation |
> | Műveletek | Microsoft.Sql/locations/privateEndpointConnectionProxyAzureAsyncOperation/read | Gets the result for a private endpoint connection proxy operation |
> | Műveletek | Microsoft.Sql/locations/privateEndpointConnectionProxyOperationResults/read | Gets the result for a private endpoint connection proxy operation |
> | Műveletek | Microsoft.Sql/locations/read | Gets the available locations for a given subscription |
> | Műveletek | Microsoft.Sql/locations/serverKeyAzureAsyncOperation/read | Gets in-progress operations on transparent data encryption server keys |
> | Műveletek | Microsoft.Sql/locations/serverKeyOperationResults/read | Gets in-progress operations on transparent data encryption server keys |
> | Műveletek | Microsoft.Sql/locations/shortTermRetentionPolicyOperationResults/read | Gets the status of a short term retention policy operation |
> | Műveletek | Microsoft.Sql/locations/syncAgentOperationResults/read | Retrieve result of the sync agent resource operation |
> | Műveletek | Microsoft.Sql/locations/syncDatabaseIds/read | Retrieve the sync database ids for a particular region and subscription |
> | Műveletek | Microsoft.Sql/locations/syncGroupOperationResults/read | Retrieve result of the sync group resource operation |
> | Műveletek | Microsoft.Sql/locations/syncMemberOperationResults/read | Retrieve result of the sync member resource operation |
> | Műveletek | Microsoft.Sql/locations/usages/read | Gets a collection of usage metrics for this subscription in a location |
> | Műveletek | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | Returns the details of the specified virtual network rules azure async operation  |
> | Műveletek | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | Returns the details of the specified virtual network rules operation  |
> | Műveletek | Microsoft.Sql/managedInstances/administrators/delete | Deletes an existing administrator of managed instance. |
> | Műveletek | Microsoft.Sql/managedInstances/administrators/read | Gets a list of managed instance administrators. |
> | Műveletek | Microsoft.Sql/managedInstances/administrators/write | Creates or updates managed instance administrator with the specified parameters. |
> | Műveletek | Microsoft.Sql/managedInstances/databases/backupLongTermRetentionPolicies/read | Gets a long term retention policy for a managed database |
> | Műveletek | Microsoft.Sql/managedInstances/databases/backupLongTermRetentionPolicies/write | Updates a long term retention policy for a managed database |
> | Műveletek | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/read | Gets a short term retention policy for a managed database |
> | Műveletek | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/write | Updates a short term retention policy for a managed database |
> | Műveletek | Microsoft.Sql/managedInstances/databases/columns/read | Return a list of columns for a managed database |
> | Műveletek | Microsoft.Sql/managedInstances/databases/completeRestore/action | Completes managed database restore operation |
> | Műveletek | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/read | Egy adott adatbázis érzékenységi címkéjének listázása |
> | Műveletek | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/write | A Batch Update érzékenységi címkéi |
> | Műveletek | Microsoft.Sql/managedInstances/databases/delete | Deletes an existing managed database |
> | Műveletek | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/logDefinitions/read | Gets the available logs for managed instance databases |
> | Műveletek | Microsoft.Sql/managedInstances/databases/read | Gets existing managed database |
> | Műveletek | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/read | Egy adott adatbázis érzékenységi címkéjének listázása |
> | Műveletek | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/write | A Batch Update ajánlott érzékenységi címkéi |
> | Műveletek | Microsoft.Sql/managedInstances/databases/restoreDetails/read | Returns managed database restore details while restore is in progress. |
> | Műveletek | Microsoft.Sql/managedInstances/databases/schemas/read | Get a managed database schema. (schema only) |
> | Műveletek | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/read | Get a managed database column (schema only) |
> | Műveletek | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/delete | Egy adott oszlop érzékenységi címkéjének törlése |
> | Műveletek | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/disable/action | Az adott oszlopra vonatkozó érzékenységi javaslatok letiltása |
> | Műveletek | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/enable/action | Érzékenységi javaslatok engedélyezése egy adott oszlopban |
> | Műveletek | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/read | Egy adott oszlop érzékenységi címkéjének beolvasása |
> | Műveletek | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/write | Egy adott oszlop érzékenységi címkéjének létrehozása vagy frissítése |
> | Műveletek | Microsoft.Sql/managedInstances/databases/schemas/tables/read | Get a managed database table (schema only) |
> | Műveletek | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | Retrieve a list of managed database threat detection policies configured for a given server |
> | Műveletek | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | Change the database threat detection policy for a given managed database |
> | Műveletek | Microsoft.Sql/managedInstances/databases/securityEvents/read | Retrieves the managed database security events |
> | Műveletek | Microsoft.Sql/managedInstances/databases/sensitivityLabels/read | Egy adott adatbázis érzékenységi címkéjének listázása |
> | Műveletek | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | Retrieve details of the database Transparent Data Encryption on a given managed database |
> | Műveletek | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | Change the database Transparent Data Encryption for a given managed database |
> | Műveletek | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/delete | Egy adott adatbázis sebezhetőségi felmérésének eltávolítása |
> | Műveletek | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/read | A sebezhetőségi felmérési szabályzatok beolvasása egy givendatabase |
> | Műveletek | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/delete | Az adott adatbázis sebezhetőségi felmérési szabályának alaptervének eltávolítása |
> | Műveletek | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/read | Egy adott adatbázis sebezhetőségi felmérési szabályának alaptervének beolvasása |
> | Műveletek | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/write | Egy adott adatbázis sebezhetőségi felmérési szabályának alaptervének módosítása |
> | Műveletek | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/export/action | Meglévő vizsgálat eredményének konvertálása emberi olvasási formátumba. Ha már létezik, semmi nem történik |
> | Műveletek | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/initiateScan/action | Sebezhetőség-felmérési adatbázis vizsgálatának végrehajtása. |
> | Műveletek | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/read | Visszaadja az adatbázis sebezhetőségi felmérési rekordjainak listáját, vagy beolvassa a megadott vizsgálati AZONOSÍTÓhoz tartozó vizsgálati rekordot. |
> | Műveletek | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/write | Egy adott adatbázis sebezhetőségi felmérésének módosítása |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/írás | Létrehoz egy új adatbázist, vagy frissít egy meglévő adatbázist. |
> | Műveletek | Microsoft. SQL/managedInstances/delete | Egy meglévő felügyelt példány törlése. |
> | Műveletek | Microsoft. SQL/managedInstances/encryptionProtector/READ | Returns a list of server encryption protectors or gets the properties for the specified server encryption protector. |
> | Műveletek | Microsoft. SQL/managedInstances/encryptionProtector/revalidate/Action | Update the properties for the specified Server Encryption Protector. |
> | Műveletek | Microsoft. SQL/managedInstances/encryptionProtector/Write | Update the properties for the specified Server Encryption Protector. |
> | Műveletek | Microsoft. SQL/managedInstances/inaccessibleManagedDatabases/READ | A felügyelt példányban lévő nem elérhető felügyelt adatbázisok listájának beolvasása |
> | Műveletek | Microsoft. SQL/managedInstances/kulcsok/törlés | Egy meglévő Azure SQL felügyelt példány kulcsának törlése. |
> | Műveletek | Microsoft. SQL/managedInstances/kulcsok/olvasás | Visszaadja a felügyelt példány kulcsainak listáját, vagy lekéri a megadott felügyelt példány kulcsának tulajdonságait. |
> | Műveletek | Microsoft. SQL/managedInstances/kulcsok/írás | Létrehoz egy kulcsot a megadott paraméterekkel, vagy frissíti a megadott felügyelt példány kulcsának tulajdonságait vagy címkéit. |
> | Műveletek | Microsoft. SQL/managedInstances/metricDefinitions/READ | Felügyelt példány metrika-definícióinak beolvasása |
> | Műveletek | Microsoft. SQL/managedInstances/metrika/olvasás | Felügyelt példány metrikáinak beolvasása |
> | Műveletek | Microsoft. SQL/managedInstances/Operations/megszakítás/művelet | Megszakítja az Azure SQL felügyelt példányát, amely még nem fejeződött be, aszinkron műveletet vár. |
> | Műveletek | Microsoft. SQL/managedInstances/Operations/READ | Felügyelt példányok műveleteinek beolvasása |
> | Műveletek | Microsoft. SQL/managedInstances/Providers/Microsoft. bepillantást/diagnosticSettings/READ | Az erőforrás diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft. SQL/managedInstances/Providers/Microsoft. bepillantások/diagnosticSettings/írás | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft. SQL/managedInstances/Providers/Microsoft. bepillantást/logDefinitions/READ | A felügyelt példányok elérhető naplófájljainak beolvasása |
> | Műveletek | Microsoft. SQL/managedInstances/Providers/Microsoft. bepillantást/metricDefinitions/READ | A felügyelt példányok számára elérhető metrikák visszatérési típusai |
> | Műveletek | Microsoft. SQL/managedInstances/READ | Visszaadja a felügyelt példányok listáját, vagy lekéri a megadott felügyelt példány tulajdonságait. |
> | Műveletek | Microsoft. SQL/managedInstances/recoverableDatabases/READ | A helyreállítható felügyelt adatbázisok listáját adja vissza. |
> | Műveletek | Microsoft. SQL/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/READ | Rövid távú adatmegőrzési szabályzat beolvasása egy eldobott felügyelt adatbázishoz |
> | Műveletek | Microsoft. SQL/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/Write | Egy eldobott felügyelt adatbázis rövid távú adatmegőrzési szabályzatának frissítése |
> | Műveletek | Microsoft. SQL/managedInstances/restorableDroppedDatabases/READ | Visszaállítható felügyelt adatbázisok listájának beolvasása. |
> | Műveletek | Microsoft. SQL/managedInstances/securityAlertPolicies/READ | Egy adott kiszolgálóhoz konfigurált felügyelt kiszolgáló veszélyforrás-észlelési szabályzatok listájának beolvasása |
> | Műveletek | Microsoft. SQL/managedInstances/securityAlertPolicies/Write | Felügyelt kiszolgáló fenyegetés-észlelési szabályzatának módosítása egy adott felügyelt kiszolgálóhoz |
> | Műveletek | Microsoft. SQL/managedInstances/tdeCertificates/művelet | TDE-tanúsítvány létrehozása/frissítése |
> | Műveletek | Microsoft. SQL/managedInstances/vulnerabilityAssessments/delete | Egy adott felügyelt példány sebezhetőségi felmérésének eltávolítása |
> | Műveletek | Microsoft. SQL/managedInstances/vulnerabilityAssessments/READ | A sebezhetőségi felmérési szabályzatok beolvasása egy adott felügyelt példányon |
> | Műveletek | Microsoft. SQL/managedInstances/vulnerabilityAssessments/Write | Egy adott felügyelt példány sebezhetőségi felmérésének módosítása |
> | Műveletek | Microsoft. SQL/managedInstances/írás | Létrehoz egy felügyelt példányt a megadott paraméterekkel, vagy frissíti a megadott felügyelt példány tulajdonságait vagy címkéit. |
> | Műveletek | Microsoft. SQL/Operations/READ | Elérhető REST-műveletek beolvasása |
> | Műveletek | Microsoft. SQL/privateEndpointConnectionsApproval/művelet | Determines if user is allowed to approve a private endpoint connection |
> | Műveletek | Microsoft. SQL/regisztráció/művelet | Regisztrálja a Microsoft SQL Database erőforrás-szolgáltató előfizetését, és engedélyezi a Microsoft SQL-adatbázisok létrehozását. |
> | Műveletek | Microsoft. SQL/kiszolgálók/rendszergazdák/törlés | Egy adott Azure Active Directory rendszergazdai objektum törlése |
> | Műveletek | Microsoft. SQL/kiszolgálók/rendszergazdák/olvasás | Egy adott Azure Active Directory rendszergazdai objektum beolvasása |
> | Műveletek | Microsoft. SQL/kiszolgálók/rendszergazdák/írás | Egy adott Azure Active Directory rendszergazdai objektum hozzáadására vagy frissítésére szolgál. |
> | Műveletek | Microsoft. SQL/kiszolgálók/tanácsadók/olvasás | A kiszolgálóhoz elérhető tanácsadók listáját adja vissza. |
> | Műveletek | Microsoft. SQL/kiszolgálók/tanácsadók/recommendedActions/olvasás | A kiszolgáló megadott tanácsadójának javasolt műveleteinek listáját adja vissza. |
> | Műveletek | Microsoft. SQL/kiszolgálók/tanácsadók/recommendedActions/írás | A javasolt művelet alkalmazása a kiszolgálón |
> | Műveletek | Microsoft. SQL/kiszolgálók/tanácsadók/írás | A tanácsadók kiszolgálói szinten történő automatikus végrehajtási állapotának frissítése. |
> | Műveletek | Microsoft. SQL/kiszolgálók/auditingPolicies/olvasás | Az adott kiszolgálón konfigurált alapértelmezett kiszolgálóoldali naplózási szabályzat részleteinek beolvasása |
> | Műveletek | Microsoft. SQL/kiszolgálók/auditingPolicies/írás | Az alapértelmezett kiszolgálói tábla naplózásának módosítása egy adott kiszolgálón |
> | Műveletek | Microsoft. SQL/Servers/auditingSettings/operationResults/READ | A kiszolgálói blob naplózási házirendjének beállítása művelet eredményének beolvasása |
> | Műveletek | Microsoft. SQL/kiszolgálók/auditingSettings/olvasás | Egy adott kiszolgálón konfigurált kiszolgálói blob naplózási szabályzat részleteinek beolvasása |
> | Műveletek | Microsoft. SQL/kiszolgálók/auditingSettings/írás | A kiszolgáló blob-naplózásának módosítása egy adott kiszolgálón |
> | Műveletek | Microsoft. SQL/kiszolgálók/automaticTuning/olvasás | A kiszolgáló Automatikus hangolási beállításainak visszaadása |
> | Műveletek | Microsoft. SQL/kiszolgálók/automaticTuning/írás | Frissíti a kiszolgáló Automatikus hangolási beállításait, és visszaadja a frissített beállításokat |
> | Műveletek | Microsoft. SQL/Servers/backupLongTermRetentionVaults/delete | Töröl egy meglévő biztonsági mentési archiváló tárat. |
> | Műveletek | Microsoft. SQL/kiszolgálók/backupLongTermRetentionVaults/olvasás | Ezzel a művelettel egy biztonsági mentési hosszú távú adatmegőrzési tár olvasható be. Információt ad vissza a kiszolgálón regisztrált tárolóról |
> | Műveletek | Microsoft. SQL/kiszolgálók/backupLongTermRetentionVaults/írás | Ezzel a művelettel lehet regisztrálni egy biztonsági mentési hosszú távú adatmegőrzési tárolót egy kiszolgálóra |
> | Műveletek | Microsoft. SQL/Servers/communicationLinks/delete | Egy meglévő kiszolgálói kommunikációs hivatkozás törlése. |
> | Műveletek | Microsoft. SQL/kiszolgálók/communicationLinks/olvasás | Egy megadott kiszolgáló kommunikációs hivatkozásainak listáját adja vissza. |
> | Műveletek | Microsoft. SQL/kiszolgálók/communicationLinks/írás | Kiszolgáló-kommunikációs hivatkozás létrehozása vagy frissítése. |
> | Műveletek | Microsoft. SQL/kiszolgálók/connectionPolicies/olvasás | Egy adott kiszolgáló kiszolgálói csatlakoztatási házirendjeinek a listáját adja vissza. |
> | Műveletek | Microsoft. SQL/kiszolgálók/connectionPolicies/írás | Kiszolgálói kapcsolatok házirendjének létrehozása vagy frissítése. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/tanácsadók/olvasás | Az adatbázishoz elérhető tanácsadók listájának beolvasása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/tanácsadók/recommendedActions/olvasás | Az adatbázis megadott tanácsadójának javasolt műveleteinek listáját adja vissza. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/tanácsadók/recommendedActions/írás | A javasolt művelet alkalmazása az adatbázison |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/tanácsadók/írás | Egy Advisor automatikus végrehajtási állapotának frissítése az adatbázis szintjén. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/auditingPolicies/olvasás | Az adott adatbázisban konfigurált táblázatos naplózási szabályzat részleteinek beolvasása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/auditingPolicies/írás | A tábla naplózási házirendjének módosítása egy adott adatbázisra vonatkozóan |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/auditingSettings/olvasás | Egy adott adatbázisban konfigurált blob-naplózási szabályzat részleteinek beolvasása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/auditingSettings/írás | Egy adott adatbázis blob-naplózási házirendjének módosítása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/auditRecords/olvasás | Az adatbázis blob-naplózási rekordjainak beolvasása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/automaticTuning/olvasás | Egy adatbázis Automatikus hangolási beállításainak visszaadása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/automaticTuning/írás | Frissíti az adatbázis Automatikus hangolási beállításait, és visszaadja a frissített beállításokat |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/azureAsyncOperation/olvasás | Egy adatbázis-művelet állapotának beolvasása. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/backupLongTermRetentionPolicies/olvasás | Egy adott adatbázis biztonsági mentési archiválási házirendjeinek a listáját adja vissza. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/backupLongTermRetentionPolicies/írás | Adatbázis biztonsági mentési archiválási szabályzatának létrehozása vagy frissítése. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/backupShortTermRetentionPolicies/olvasás | Egy adatbázis rövid távú adatmegőrzési szabályzatának beolvasása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/backupShortTermRetentionPolicies/írás | Egy adatbázis rövid távú adatmegőrzési szabályzatának frissítése |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/oszlopok/olvasás | Egy adatbázis oszlopainak listájának visszaadása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/connectionPolicies/olvasás | Egy adott adatbázisban konfigurált kapcsolati szabályzat részleteinek beolvasása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/connectionPolicies/írás | Egy adott adatbázishoz tartozó kapcsolatbiztonsági szabályzat módosítása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/currentSensitivityLabels/olvasás | Egy adott adatbázis érzékenységi címkéjének listázása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/currentSensitivityLabels/írás | A Batch Update érzékenységi címkéi |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/dataMaskingPolicies/olvasás | Az adatbázis-adatmaszkolási szabályzatok listájának visszaadása. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/dataMaskingPolicies/szabályok/törlés | Az adatmaszkolási házirend szabályának törlése egy adott adatbázisra vonatkozóan |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/dataMaskingPolicies/szabályok/olvasás | Az adott adatbázisban konfigurált adatmaszkolási házirend-szabály részleteinek beolvasása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/dataMaskingPolicies/szabályok/írás | Az adatmaszkolási házirend szabályának módosítása egy adott adatbázisra vonatkozóan |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/dataMaskingPolicies/írás | Egy adott adatbázis adatmaszkolási házirendjének módosítása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/dataWarehouseQueries/dataWarehouseQuerySteps/READ | Az adatraktár-lekérdezés elosztott lekérdezési lépésének információit adja vissza a kiválasztott lépés-AZONOSÍTÓhoz |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/dataWarehouseQueries/olvasás | Az adatraktár-terjesztési lekérdezési adatokat adja vissza a kiválasztott lekérdezési AZONOSÍTÓhoz. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/dataWarehouseUserActivities/olvasás | Lekéri egy SQL Data Warehouse példány felhasználói tevékenységét, amely tartalmazza a futó és a felfüggesztett lekérdezéseket |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/törlés | Töröl egy meglévő adatbázist. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/exportálás/művelet | Azure SQL Database exportálása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/extendedAuditingSettings/olvasás | Az adott adatbázisban konfigurált kiterjesztett blob-naplózási szabályzat részleteinek beolvasása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/extendedAuditingSettings/írás | Egy adott adatbázis kiterjesztett blob-naplózási szabályzatának módosítása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/bővítmények/olvasás | Az adatbázishoz tartozó bővítmények gyűjteményét kéri le. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/bővítmények/írás | Egy adott adatbázis kiterjesztésének módosítása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/feladatátvétel/művelet | Ügyfél által kezdeményezett adatbázis-feladatátvétel. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/geoBackupPolicies/olvasás | Egy adott adatbázishoz tartozó földrajzi biztonsági mentési szabályzatok beolvasása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/geoBackupPolicies/írás | Adatbázis-geobackup szabályzat létrehozása vagy frissítése |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/importExportOperationResults/olvasás | Gets in-progress import/export operations |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/maintenanceWindowOptions/olvasás | A kiválasztott adatbázishoz elérhető karbantartási időszakok listájának beolvasása. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/maintenanceWindows/olvasás | A kiválasztott adatbázis karbantartási Windows-beállításainak beolvasása. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/maintenanceWindows/írás | Beállítja a karbantartási Windows-beállításokat a kiválasztott adatbázishoz. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/metricDefinitions/olvasás | Az adatbázisokhoz elérhető metrikák visszatérési típusai |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/mérőszámok/olvasás | Adatbázisok visszaküldési metrikái |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/áthelyezés/művelet | Meglévő adatbázis nevének módosítása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/operationResults/olvasás | Egy adatbázis-művelet állapotának beolvasása. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/műveletek/megszakítás/művelet | Megszakítja Azure SQL Database folyamatban lévő aszinkron műveletet, amely még nem fejeződött be. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/műveletek/olvasás | Az adatbázison végrehajtott műveletek listájának visszaadása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/szüneteltetés/művelet | Az Azure SQL Datawarehouse-adatbázis szüneteltetése |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/szolgáltatók/Microsoft. bepillantást/diagnosticSettings/READ | Az erőforrás diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/szolgáltatók/Microsoft. bepillantások/diagnosticSettings/írás | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/szolgáltatók/Microsoft. bepillantást/logDefinitions/READ | A rendelkezésre álló naplók beolvasása az adatbázisokhoz |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/szolgáltatók/Microsoft. bepillantást/metricDefinitions/READ | Az adatbázisokhoz elérhető metrikák visszatérési típusai |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/queryStore/queryTexts/READ | A megadott paramétereknek megfelelő lekérdezési szövegek gyűjteményét adja vissza. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/queryStore/olvasás | Az adatbázishoz tartozó Query Store-beállítások aktuális értékeit adja vissza. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/queryStore/írás | Frissíti az adatbázis lekérdezés-tárolójának beállításait |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/olvasás | Az adatbázisok listájának visszaadása vagy a megadott adatbázis tulajdonságainak beolvasása. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/recommendedSensitivityLabels/olvasás | Egy adott adatbázis érzékenységi címkéjének listázása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/recommendedSensitivityLabels/írás | A Batch Update ajánlott érzékenységi címkéi |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/replicationLinks/delete | A replikálási kapcsolat kényszerített leállítása és lehetséges adatvesztése |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/replicationLinks/feladatátvétel/művelet | Feladatátvétel az elsődlegesről az összes módosítás szinkronizálása után, hogy az adatbázis az elsődleges replikációs relationship\u0027s legyen, és hogy a távoli elsődleges legyen a másodlagos |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/replicationLinks/forceFailoverAllowDataLoss/művelet | Azonnali feladatátvétel a lehetséges adatvesztéssel, az adatbázis replikálása az elsődleges replikálási relationship\u0027s, és a távoli elsődleges folyamat másodlagosra állítása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/replicationLinks/olvasás | Visszaadja a replikációs hivatkozások listáját, vagy lekéri a megadott replikációs hivatkozások tulajdonságait. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/replicationLinks/leválasztás/művelet | A replikációs kapcsolat kényszerített leállítása vagy a partnerrel való szinkronizálás után |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/replicationLinks/updateReplicationMode/művelet | Replikációs mód frissítése a szinkron vagy aszinkron üzemmódra való hivatkozáshoz |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/restorePoints/művelet | Új visszaállítási pont létrehozása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/restorePoints/delete | Törli az adatbázis visszaállítási pontját. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/restorePoints/olvasás | Az adatbázis visszaállítási pontjait adja vissza. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/folytatás/művelet | Az Azure SQL Datawarehouse-adatbázis folytatása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/sémák/olvasás | Adatbázis-séma beszerzése (csak séma). |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/sémák/táblák/oszlopok/olvasás | Adatbázis-oszlop beolvasása (csak séma). |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/sémák/táblák/oszlopok/sensitivityLabels/törlés | Egy adott oszlop érzékenységi címkéjének törlése |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/sémák/táblák/oszlopok/sensitivityLabels/letiltás/művelet | Az adott oszlopra vonatkozó érzékenységi javaslatok letiltása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/sémák/táblák/oszlopok/sensitivityLabels/engedélyezés/művelet | Érzékenységi javaslatok engedélyezése egy adott oszlopban |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/sémák/táblák/oszlopok/sensitivityLabels/olvasás | Egy adott oszlop érzékenységi címkéjének beolvasása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/sémák/táblák/oszlopok/sensitivityLabels/írás | Egy adott oszlop érzékenységi címkéjének létrehozása vagy frissítése |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/sémák/táblák/olvasás | Adatbázis-tábla beszerzése (csak séma). |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/sémák/táblák/recommendedIndexes/olvasás | Tárgymutató-javaslatok listájának beolvasása egy adatbázison |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/sémák/táblák/recommendedIndexes/írás | Tárgymutató-javaslat alkalmazása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/securityAlertPolicies/olvasás | Az adott kiszolgálóhoz konfigurált adatbázis-veszélyforrások észlelési házirendjeinek listájának beolvasása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/securityAlertPolicies/írás | Az adatbázis-veszélyforrások észlelési házirendjének módosítása egy adott adatbázisra vonatkozóan |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/securityMetrics/olvasás | Adatbázis-biztonsági mérőszámok gyűjteményének beolvasása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/sensitivityLabels/olvasás | Egy adott adatbázis érzékenységi címkéjének listázása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/serviceTierAdvisors/olvasás | A teljesítmény növelése vagy a költséghatékonyság csökkentése érdekében a lekérdezés végrehajtási statisztikái alapján fel-vagy leskálázásával kapcsolatos javaslat visszaküldése |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/SKU/READ | Egy adatbázishoz elérhető SKU-gyűjtemény beolvasása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/syncGroups/cancelSync/művelet | Szinkronizálási csoport szinkronizálásának megszakítása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/syncGroups/delete | Töröl egy meglévő szinkronizálási csoportot. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/syncGroups/hubSchemas/READ | A Szinkronizáló központ adatbázis-sémáinak listájának visszaadása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/syncGroups/naplók/olvasás | A szinkronizálási csoport naplóiból álló lista visszaküldése |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/syncGroups/olvasás | Visszaadja a szinkronizálási csoportok listáját, vagy lekéri a megadott szinkronizálási csoport tulajdonságait. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/syncGroups/refreshHubSchema/művelet | A Szinkronizáló központ adatbázis-sémájának frissítése |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/syncGroups/refreshHubSchemaOperationResults/READ | A szinkronizálási központ sémájának frissítési művelete eredményének beolvasása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/syncGroups/syncMembers/delete | Töröl egy meglévő szinkronizálási tagot. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/syncGroups/syncMembers/READ | Visszaadja a szinkronizálási tagok listáját, vagy lekéri a megadott szinkronizálási tag tulajdonságait. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/syncGroups/syncMembers/refreshSchema/művelet | Szinkronizálási tag sémájának frissítése |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/syncGroups/syncMembers/refreshSchemaOperationResults/READ | A szinkronizálási tag séma-frissítési műveletének eredményének beolvasása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/syncGroups/syncMembers/sémák/olvasás | A szinkronizálási tag adatbázis-sémáinak listájának visszaadása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/syncGroups/syncMembers/írás | Egy szinkronizálási tagot hoz létre a megadott paraméterekkel, vagy frissíti a megadott szinkronizálási tag tulajdonságait. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/syncGroups/triggerSync/művelet | Szinkronizálási csoport szinkronizálásának elindítása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/syncGroups/írás | Egy szinkronizálási csoportot hoz létre a megadott paraméterekkel, vagy frissíti a megadott szinkronizálási csoport tulajdonságait. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/topQueries/queryText/művelet | A kiválasztott lekérdezési azonosító Transact-SQL-szövegét adja vissza. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/topQueries/olvasás | Összesített futtatókörnyezeti statisztikákat ad vissza a kijelölt lekérdezéshez a kiválasztott időszakban |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/topQueries/statisztika/olvasás | Összesített futtatókörnyezeti statisztikákat ad vissza a kijelölt lekérdezéshez a kiválasztott időszakban |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/transparentDataEncryption/operationResults/READ | Folyamatban lévő műveletek beolvasása transzparens adattitkosítással |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/transparentDataEncryption/olvasás | Egy adott adatbázis transzparens adattitkosítási biztonsági funkciójának állapotának és részleteinek beolvasása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/transparentDataEncryption/írás | Transzparens adattitkosítási állapot módosítása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/upgradeDataWarehouse/művelet | Az Azure SQL Datawarehouse-adatbázis frissítése |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/használati adatok/olvasás | A Azure SQL Database használati adatok beolvasása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/vulnerabilityAssessments/delete | Egy adott adatbázis sebezhetőségi felmérésének eltávolítása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/vulnerabilityAssessments/olvasás | A sebezhetőségi felmérési szabályzatok beolvasása egy givendatabase |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/vulnerabilityAssessments/szabályok/alaptervek/törlés | Az adott adatbázis sebezhetőségi felmérési szabályának alaptervének eltávolítása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/vulnerabilityAssessments/szabályok/alaptervek/olvasás | Egy adott adatbázis sebezhetőségi felmérési szabályának alaptervének beolvasása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/vulnerabilityAssessments/szabályok/alaptervek/írás | Egy adott adatbázis sebezhetőségi felmérési szabályának alaptervének módosítása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/vulnerabilityAssessments/vizsgálatok/exportálás/művelet | Meglévő vizsgálat eredményének konvertálása emberi olvasási formátumba. Ha már létezik, semmi nem történik |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/vulnerabilityAssessments/Scans/initiateScan/Action | Sebezhetőség-felmérési adatbázis vizsgálatának végrehajtása. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/vulnerabilityAssessments/vizsgálatok/olvasás | Visszaadja az adatbázis sebezhetőségi felmérési rekordjainak listáját, vagy beolvassa a megadott vizsgálati AZONOSÍTÓhoz tartozó vizsgálati rekordot. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/vulnerabilityAssessments/írás | Egy adott adatbázis sebezhetőségi felmérésének módosítása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/vulnerabilityAssessmentScans/művelet | Sebezhetőség-felmérési adatbázis vizsgálatának végrehajtása. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/vulnerabilityAssessmentScans/operationResults/READ | Az adatbázis sebezhetőség-felmérési vizsgálatának végrehajtási műveletének eredményének beolvasása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/vulnerabilityAssessmentSettings/olvasás | Egy adott adatbázison konfigurált sebezhetőségi felmérés részleteinek beolvasása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/vulnerabilityAssessmentSettings/írás | Egy adott adatbázis sebezhetőségi felmérésének módosítása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/workloadGroups/delete | Elveszít egy adott munkaterhelés-csoportot. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/workloadGroups/olvasás | Felsorolja a kiválasztott adatbázis munkaterhelés-csoportjait. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/workloadGroups/workloadClassifiers/delete | Egy adott számítási feladat besorolásának elejtése. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/workloadGroups/workloadClassifiers/READ | Felsorolja a kiválasztott adatbázis számítási feladatainak besorolását. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/workloadGroups/workloadClassifiers/írás | Beállítja egy adott munkaterhelés-osztályozó tulajdonságainak beállítását. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/workloadGroups/írás | Egy adott munkaterhelés-csoport tulajdonságainak beállítása. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/írás | Létrehoz egy adatbázist a megadott paraméterekkel, vagy frissíti a megadott adatbázis tulajdonságait vagy címkéit. |
> | Műveletek | Microsoft. SQL/kiszolgálók/törlés | Töröl egy meglévő kiszolgálót. |
> | Műveletek | Microsoft. SQL/Servers/disasterRecoveryConfiguration/delete | Egy meglévő vész-helyreállítási konfiguráció törlése egy adott kiszolgálóhoz |
> | Műveletek | Microsoft. SQL/kiszolgálók/disasterRecoveryConfiguration/feladatátvétel/művelet | DisasterRecoveryConfiguration feladatátvétele |
> | Műveletek | Microsoft. SQL/Servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/Action | DisasterRecoveryConfiguration-feladatátvétel kényszerítése |
> | Műveletek | Microsoft. SQL/kiszolgálók/disasterRecoveryConfiguration/olvasás | A kiszolgálót tartalmazó vész-helyreállítási konfigurációk gyűjteményének beolvasása |
> | Műveletek | Microsoft. SQL/kiszolgálók/disasterRecoveryConfiguration/írás | A kiszolgáló vész-helyreállítási konfigurációjának módosítása |
> | Műveletek | Microsoft. SQL/kiszolgálók/elasticPoolEstimates/olvasás | A kiszolgálóhoz már létrehozott rugalmas készletre vonatkozó becslések listáját adja vissza. |
> | Műveletek | Microsoft. SQL/kiszolgálók/elasticPoolEstimates/írás | Új rugalmas készletre vonatkozó becslést hoz létre a megadott adatbázisok listájához |
> | Műveletek | Microsoft. SQL/kiszolgálók/elasticPools/tanácsadók/olvasás | A rugalmas készlethez elérhető tanácsadók listájának beolvasása |
> | Műveletek | Microsoft. SQL/kiszolgálók/elasticPools/Advisors/recommendedActions/READ | A rugalmas készlethez megadott tanácsadó által javasolt műveletek listáját adja vissza. |
> | Műveletek | Microsoft. SQL/kiszolgálók/elasticPools/Advisors/recommendedActions/Write | A javasolt művelet alkalmazása a rugalmas készleten |
> | Műveletek | Microsoft. SQL/kiszolgálók/elasticPools/Advisors/Write | Az Advisor automatikus végrehajtási állapotának frissítése rugalmas készlet szintjén. |
> | Műveletek | Microsoft. SQL/kiszolgálók/elasticPools/adatbázisok/olvasás | A rugalmas készlethez tartozó adatbázisok listájának beolvasása |
> | Műveletek | Microsoft. SQL/Servers/elasticPools/delete | Meglévő rugalmas készlet törlése |
> | Műveletek | Microsoft. SQL/Servers/elasticPools/elasticPoolActivity/READ | Tevékenységek és részletek beolvasása egy adott rugalmas adatbázis-készleten |
> | Műveletek | Microsoft. SQL/Servers/elasticPools/elasticPoolDatabaseActivity/READ | Tevékenységek és részletek beolvasása egy adott adatbázisra, amely a rugalmas adatbázis-készlet részét képezi. |
> | Műveletek | Microsoft. SQL/kiszolgálók/elasticPools/feladatátvétel/művelet | Az ügyfél által kezdeményezett rugalmas készlet feladatátvétele. |
> | Műveletek | Microsoft. SQL/Servers/elasticPools/metricDefinitions/READ | A rugalmas adatbázis-készletek számára elérhető metrikák visszatérési típusai |
> | Műveletek | Microsoft. SQL/kiszolgálók/elasticPools/mérőszámok/olvasás | Rugalmas adatbázis-készletek visszaküldési metrikái |
> | Műveletek | Microsoft. SQL/kiszolgálók/elasticPools/műveletek/megszakítás/művelet | Megszakítja az Azure SQL rugalmas készlet megszakítását, amely még nem fejeződött be aszinkron művelet. |
> | Műveletek | Microsoft. SQL/Servers/elasticPools/Operations/READ | A rugalmas készleten végrehajtott műveletek listájának visszaadása |
> | Műveletek | Microsoft. SQL/Servers/elasticPools/Providers/Microsoft. bepillantást/diagnosticSettings/READ | Az erőforrás diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft. SQL/Servers/elasticPools/Providers/Microsoft. bepillantást/diagnosticSettings/Write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft. SQL/Servers/elasticPools/Providers/Microsoft. bepillantást/metricDefinitions/READ | A rugalmas adatbázis-készletek számára elérhető metrikák visszatérési típusai |
> | Műveletek | Microsoft. SQL/kiszolgálók/elasticPools/olvasás | Az adott kiszolgálón található rugalmas készlet adatainak beolvasása |
> | Műveletek | Microsoft. SQL/kiszolgálók/elasticPools/SKU/READ | Egy rugalmas készlethez rendelkezésre álló SKU-gyűjtemény beolvasása |
> | Műveletek | Microsoft. SQL/kiszolgálók/elasticPools/írás | Meglévő rugalmas készlet új vagy módosított tulajdonságainak létrehozása |
> | Műveletek | Microsoft. SQL/kiszolgálók/encryptionProtector/olvasás | Returns a list of server encryption protectors or gets the properties for the specified server encryption protector. |
> | Műveletek | Microsoft.Sql/servers/encryptionProtector/revalidate/action | Update the properties for the specified Server Encryption Protector. |
> | Műveletek | Microsoft.Sql/servers/encryptionProtector/write | Update the properties for the specified Server Encryption Protector. |
> | Műveletek | Microsoft.Sql/servers/extendedAuditingSettings/read | Retrieve details of the extended server blob auditing policy configured on a given server |
> | Műveletek | Microsoft.Sql/servers/extendedAuditingSettings/write | Change the extended server blob auditing for a given server |
> | Műveletek | Microsoft.Sql/servers/failoverGroups/delete | Deletes an existing failover group. |
> | Műveletek | Microsoft.Sql/servers/failoverGroups/failover/action | Executes planned failover in an existing failover group. |
> | Műveletek | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | Executes forced failover in an existing failover group. |
> | Műveletek | Microsoft.Sql/servers/failoverGroups/read | Returns the list of failover groups or gets the properties for the specified failover group. |
> | Műveletek | Microsoft.Sql/servers/failoverGroups/write | Creates a failover group with the specified parameters or updates the properties or tags for the specified failover group. |
> | Műveletek | Microsoft.Sql/servers/firewallRules/delete | Deletes an existing server firewall rule. |
> | Műveletek | Microsoft.Sql/servers/firewallRules/read | Return the list of server firewall rules or gets the properties for the specified server firewall rule. |
> | Műveletek | Microsoft.Sql/servers/firewallRules/write | Creates a server firewall rule with the specified parameters, update the properties for the specified rule or overwrite all existing rules with new server firewall rule(s). |
> | Műveletek | Microsoft.Sql/servers/import/action | Create a new database on the server and deploy schema and data from a DacPac package |
> | Műveletek | Microsoft.Sql/servers/importExportOperationResults/read | Gets in-progress import/export operations |
> | Műveletek | Microsoft.Sql/servers/inaccessibleDatabases/read | Return a list of inaccessible database(s) in a logical server. |
> | Műveletek | Microsoft.Sql/servers/interfaceEndpointProfiles/delete | Deletes the specified interface endpoint profile |
> | Műveletek | Microsoft.Sql/servers/interfaceEndpointProfiles/read | Returns the properties for the specified interface endpoint profile |
> | Műveletek | Microsoft.Sql/servers/interfaceEndpointProfiles/write | Creates a interface endpoint profile with the specified parameters or updates the properties or tags for the specified interface endpoint |
> | Műveletek | Microsoft.Sql/servers/jobAgents/delete | Deletes an Azure SQL DB job agent |
> | Műveletek | Microsoft.Sql/servers/jobAgents/read | Gets an Azure SQL DB job agent |
> | Műveletek | Microsoft.Sql/servers/jobAgents/write | Creates or updates an Azure SQL DB job agent |
> | Műveletek | Microsoft.Sql/servers/keys/delete | Deletes an existing server key. |
> | Műveletek | Microsoft.Sql/servers/keys/read | Return the list of server keys or gets the properties for the specified server key. |
> | Műveletek | Microsoft.Sql/servers/keys/write | Creates a key with the specified parameters or update the properties or tags for the specified server key. |
> | Műveletek | Microsoft.Sql/servers/operationResults/read | Gets in-progress server operations |
> | Műveletek | Microsoft.Sql/servers/operations/read | Return the list of operations performed on the server |
> | Műveletek | Microsoft.Sql/servers/privateEndpointConnectionProxies/delete | Deletes an existing private endpoint connection proxy |
> | Műveletek | Microsoft.Sql/servers/privateEndpointConnectionProxies/read | Returns the list of private endpoint connection proxies or gets the properties for the specified private endpoint connection proxy. |
> | Műveletek | Microsoft.Sql/servers/privateEndpointConnectionProxies/validate/action | Validates a private endpoint connection create call from NRP side |
> | Műveletek | Microsoft.Sql/servers/privateEndpointConnectionProxies/write | Creates a private endpoint connection proxy with the specified parameters or updates the properties or tags for the specified private endpoint connection proxy. |
> | Műveletek | Microsoft.Sql/servers/privateEndpointConnections/delete | Deletes an existing private endpoint connection |
> | Műveletek | Microsoft.Sql/servers/privateEndpointConnections/read | Returns the list of private endpoint connections or gets the properties for the specified private endpoint connection. |
> | Műveletek | Microsoft.Sql/servers/privateEndpointConnections/write | Approves or rejects an existing private endpoint connection |
> | Műveletek | Microsoft.Sql/servers/privateEndpointConnectionsApproval/action | Determines if user is allowed to approve a private endpoint connection |
> | Műveletek | Microsoft.Sql/servers/privateLinkResources/read | Get the private link resources for the corresponding sql server |
> | Műveletek | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | Return types of metrics that are available for servers |
> | Műveletek | Microsoft.Sql/servers/read | Return the list of servers or gets the properties for the specified server. |
> | Műveletek | Microsoft.Sql/servers/recommendedElasticPools/databases/read | Retrieve metrics for recommended elastic database pools for a given server |
> | Műveletek | Microsoft.Sql/servers/recommendedElasticPools/read | Retrieve recommendation for elastic database pools to reduce cost or improve performance based on historical resource utilization |
> | Műveletek | Microsoft.Sql/servers/recoverableDatabases/read | Ez a művelet az élő adatbázis vész-helyreállítására szolgál az adatbázis utolsó ismert jó biztonsági mentési pontra való visszaállításához. Az utolsó jó biztonsági mentésre vonatkozó adatokat ad vissza, de az doesn\u0027t valójában visszaállítja az adatbázist. |
> | Műveletek | Microsoft. SQL/kiszolgálók/replicationLinks/olvasás | Visszaadja a replikációs hivatkozások listáját, vagy lekéri a megadott replikációs hivatkozások tulajdonságait. |
> | Műveletek | Microsoft. SQL/kiszolgálók/restorableDroppedDatabases/olvasás | Azon adatbázisok listájának lekérése, amelyek a megőrzési házirendben még nem találhatók meg az adott kiszolgálón. |
> | Műveletek | Microsoft. SQL/Servers/securityAlertPolicies/operationResults/READ | A kiszolgálói veszélyforrások észlelése házirend írási műveletének eredményeinek beolvasása |
> | Műveletek | Microsoft. SQL/kiszolgálók/securityAlertPolicies/olvasás | Egy adott kiszolgálóhoz konfigurált kiszolgálói veszélyforrások észlelési házirendjeinek listájának beolvasása |
> | Műveletek | Microsoft. SQL/kiszolgálók/securityAlertPolicies/írás | A kiszolgálói veszélyforrások észlelési házirendjének módosítása egy adott kiszolgálóhoz |
> | Műveletek | Microsoft. SQL/kiszolgálók/serviceObjectives/olvasás | Az adott kiszolgálón elérhető szolgáltatási szintű célkitűzések (más néven teljesítményszint) listájának beolvasása |
> | Műveletek | Microsoft. SQL/Servers/syncAgents/delete | Töröl egy meglévő szinkronizálási ügynököt. |
> | Műveletek | Microsoft. SQL/Servers/syncAgents/Kulcslétrehozási programjával/Action | Szinkronizálási ügynök regisztrációs kulcsának előállítása |
> | Műveletek | Microsoft. SQL/Servers/syncAgents/linkedDatabases/READ | A szinkronizálási ügynökhöz csatolt adatbázisok listájának visszaadása |
> | Műveletek | Microsoft. SQL/kiszolgálók/syncAgents/olvasás | Visszaadja a Szinkronizáló ügynökök listáját, vagy lekéri a megadott szinkronizálási ügynök tulajdonságait. |
> | Műveletek | Microsoft. SQL/kiszolgálók/syncAgents/írás | Létrehoz egy szinkronizáló ügynököt a megadott paraméterekkel, vagy frissíti a megadott szinkronizálási ügynök tulajdonságait. |
> | Műveletek | Microsoft. SQL/kiszolgálók/tdeCertificates/művelet | TDE-tanúsítvány létrehozása/frissítése |
> | Műveletek | Microsoft. SQL/kiszolgálók/használati adatok/olvasás | A kiszolgáló DTU-kvótájának és aktuális DTU-felhasználásának visszaadása a kiszolgálón található összes adatbázis esetében |
> | Műveletek | Microsoft. SQL/Servers/virtualNetworkRules/delete | Meglévő Virtual Network-szabály törlése |
> | Műveletek | Microsoft. SQL/kiszolgálók/virtualNetworkRules/olvasás | Visszaadja a virtuális hálózati szabályok listáját, vagy lekéri a megadott virtuális hálózati szabály tulajdonságait. |
> | Műveletek | Microsoft. SQL/kiszolgálók/virtualNetworkRules/írás | Létrehoz egy virtuális hálózati szabályt a megadott paraméterekkel, vagy frissíti a megadott virtuális hálózati szabály tulajdonságait vagy címkéit. |
> | Műveletek | Microsoft. SQL/Servers/vulnerabilityAssessments/delete | Egy adott kiszolgáló sebezhetőségi felmérésének eltávolítása |
> | Műveletek | Microsoft. SQL/kiszolgálók/vulnerabilityAssessments/olvasás | A sebezhetőségi felmérési szabályzatok beolvasása egy adott kiszolgálón |
> | Műveletek | Microsoft. SQL/kiszolgálók/vulnerabilityAssessments/írás | Egy adott kiszolgáló sebezhetőségi felmérésének módosítása |
> | Műveletek | Microsoft. SQL/kiszolgálók/írás | Létrehoz egy kiszolgálót a megadott paraméterekkel, vagy frissíti a megadott kiszolgálóhoz tartozó tulajdonságokat vagy címkéket. |
> | Műveletek | Microsoft. SQL/regisztráció/művelet | A Microsoft SQL Database erőforrás-szolgáltatóhoz tartozó előfizetés regisztrációjának törlése és a Microsoft SQL-adatbázisok létrehozásának engedélyezése. |
> | Műveletek | Microsoft. SQL/virtualClusters/delete | Töröl egy meglévő virtuális fürtöt. |
> | Műveletek | Microsoft. SQL/virtualClusters/READ | Visszaadja a virtuális fürtök listáját, vagy lekéri a megadott virtuális fürt tulajdonságait. |
> | Műveletek | Microsoft. SQL/virtualClusters/írás | Frissíti a virtuális fürtök címkéit. |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. SqlVirtualMachine/Locations/availabilityGroupListenerOperationResults/READ | Egy rendelkezésre állási csoport figyelője által végrehajtott művelet eredményének beolvasása |
> | Műveletek | Microsoft. SqlVirtualMachine/Locations/sqlVirtualMachineGroupOperationResults/READ | SQL virtuálisgép-csoport művelet eredményének beolvasása |
> | Műveletek | Microsoft. SqlVirtualMachine/Locations/sqlVirtualMachineOperationResults/READ | SQL-virtuálisgép-művelet eredményének beolvasása |
> | Műveletek | Microsoft. SqlVirtualMachine/Operations/READ |  |
> | Műveletek | Microsoft. SqlVirtualMachine/regisztráció/művelet | Előfizetés regisztrálása a Microsoft. SqlVirtualMachine erőforrás-szolgáltatóval |
> | Műveletek | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/availabilityGroupListeners/delete | Meglévő rendelkezésre állási csoport figyelő törlése |
> | Műveletek | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/availabilityGroupListeners/READ | Az SQL rendelkezésre állási csoport figyelője adatainak beolvasása egy adott SQL-virtuálisgép-csoporton |
> | Műveletek | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/availabilityGroupListeners/Write | Meglévő SQL rendelkezésre állási csoport figyelője új vagy módosított tulajdonságainak létrehozása |
> | Műveletek | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/delete | Meglévő SQL-virtuálisgép-csoport törlése |
> | Műveletek | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/READ | SQL-virtuálisgép-csoport részleteinek beolvasása |
> | Műveletek | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/sqlVirtualMachines/READ | SQL-alapú virtuális gépek listázása egy adott SQL virtuális virtuálisgép-csoport alapján |
> | Műveletek | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/Write | Új vagy meglévő SQL-virtuálisgép-csoport tulajdonságainak módosítása |
> | Műveletek | Microsoft. SqlVirtualMachine/sqlVirtualMachines/delete | Meglévő SQL-alapú virtuális gép törlése |
> | Műveletek | Microsoft. SqlVirtualMachine/sqlVirtualMachines/READ | SQL virtuális gép adatainak beolvasása |
> | Műveletek | Microsoft. SqlVirtualMachine/sqlVirtualMachines/Write | Meglévő SQL-alapú virtuális gép új vagy módosított tulajdonságainak létrehozása |
> | Műveletek | Microsoft. SqlVirtualMachine/regisztráció/művelet | Előfizetés regisztrációjának törlése a Microsoft. SqlVirtualMachine erőforrás-szolgáltatóval |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Storage/checknameavailability/olvasás | Ellenőrzi, hogy a fiók neve érvényes-e, és nincs-e használatban. |
> | Műveletek | Microsoft. Storage/Locations/checknameavailability/READ | Ellenőrzi, hogy a fiók neve érvényes-e, és nincs-e használatban. |
> | Műveletek | Microsoft. Storage/Locations/deleteVirtualNetworkOrSubnets/Action | A Microsoft. Storage értesítése arról, hogy a virtuális hálózat vagy az alhálózat törölve lett |
> | Műveletek | Microsoft. Storage/Locations/használat/olvasás | A megadott előfizetésben lévő erőforrások korlátját és aktuális használati számát adja vissza. |
> | Műveletek | Microsoft. Storage/Operations/READ | Egy aszinkron művelet állapotát kérdezi le. |
> | Műveletek | Microsoft. Storage/regisztráció/művelet | Regisztrálja az előfizetést a tárolási erőforrás-szolgáltató számára, és lehetővé teszi a Storage-fiókok létrehozását. |
> | Műveletek | Microsoft. Storage/SKU/READ | Felsorolja a Microsoft. Storage által támogatott SKU-ket. |
> | DataAction | Microsoft. Storage/storageAccounts/blobServices/containers/Blobok/Hozzáadás/művelet | A blob-tartalom hozzáadásának eredményét adja vissza. |
> | DataAction | Microsoft. Storage/storageAccounts/blobServices/containers/Blobok/delete | Egy blob törlésének eredményét adja vissza. |
> | DataAction | Microsoft. Storage/storageAccounts/blobServices/containers/Blobok/deleteBlobVersion/Action | Egy blob-verzió törlésének eredményét adja vissza. |
> | DataAction | Microsoft. Storage/storageAccounts/blobServices/containers/Blobok/Filter/művelet | Egy olyan fiókban lévő Blobok listáját adja vissza, amelyeknek megfelelő címkék szűrője van |
> | DataAction | Microsoft. Storage/storageAccounts/blobServices/tárolók/Blobok/olvasás | Blobot vagy Blobok listáját adja vissza. |
> | DataAction | Microsoft. Storage/storageAccounts/blobServices/containers/Blobok/runAsSuperUser/Action | A blob parancs eredményét adja vissza. |
> | DataAction | Microsoft. Storage/storageAccounts/blobServices/containers/Blobok/címkék/olvasás | A blob-címkék olvasásának eredményét adja vissza. |
> | DataAction | Microsoft. Storage/storageAccounts/blobServices/tárolók/Blobok/címkék/írás | A blob-címkék írásának eredményét adja vissza. |
> | DataAction | Microsoft. Storage/storageAccounts/blobServices/tárolók/Blobok/írás | Egy blob írásának eredményét adja vissza. |
> | Műveletek | Microsoft. Storage/storageAccounts/blobServices/containers/clearLegalHold/Action | BLOB Container jogi megtartásának törlése |
> | Műveletek | Microsoft. Storage/storageAccounts/blobServices/containers/delete | Egy tároló törlésének eredményét adja vissza. |
> | Műveletek | Microsoft. Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | BLOB-tároló módosíthatatlansági-házirendjének törlése |
> | Műveletek | Microsoft. Storage/storageAccounts/blobServices/containers/immutabilityPolicies/kiterjesztés/művelet | BLOB-tároló módosíthatatlansági-házirendjének kiterjesztése |
> | Műveletek | Microsoft. Storage/storageAccounts/blobServices/containers/immutabilityPolicies/Lock/Action | BLOB-tároló módosíthatatlansági szabályzatának zárolása |
> | Műveletek | Microsoft. Storage/storageAccounts/blobServices/containers/immutabilityPolicies/READ | BLOB-tároló módosíthatatlansági szabályzatának beolvasása |
> | Műveletek | Microsoft. Storage/storageAccounts/blobServices/containers/immutabilityPolicies/Write | BLOB-tároló módosíthatatlansági szabályzatának elhelyezése |
> | Műveletek | Microsoft. Storage/storageAccounts/blobServices/containers/Lease/Action | A blob-tároló bérletének eredményét adja vissza. |
> | Műveletek | Microsoft. Storage/storageAccounts/blobServices/tárolók/olvasás | Egy tárolót ad vissza. |
> | Műveletek | Microsoft. Storage/storageAccounts/blobServices/tárolók/olvasás | Tárolók listájának visszaadása |
> | Műveletek | Microsoft. Storage/storageAccounts/blobServices/containers/setLegalHold/Action | BLOB-tároló jogi megtartásának beállítása |
> | Műveletek | Microsoft. Storage/storageAccounts/blobServices/tárolók/írás | A blob-tároló javításának eredményét adja vissza. |
> | Műveletek | Microsoft. Storage/storageAccounts/blobServices/tárolók/írás | A blob-tároló Put eredményét adja vissza. |
> | Műveletek | Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey/művelet | Egy felhasználói delegálási kulcsot ad vissza a blob szolgáltatáshoz |
> | Műveletek | Microsoft. Storage/storageAccounts/blobServices/olvasás |  |
> | Műveletek | Microsoft. Storage/storageAccounts/blobServices/olvasás | A blob szolgáltatás tulajdonságainak vagy statisztikáinak visszaadása |
> | Műveletek | Microsoft. Storage/storageAccounts/blobServices/írás | A Put blob szolgáltatás tulajdonságainak eredményét adja vissza. |
> | Műveletek | Microsoft. Storage/storageAccounts/delete | Töröl egy meglévő Storage-fiókot. |
> | Műveletek | Microsoft. Storage/storageAccounts/encryptionScopes/olvasás |  |
> | Műveletek | Microsoft. Storage/storageAccounts/encryptionScopes/olvasás |  |
> | Műveletek | Microsoft. Storage/storageAccounts/encryptionScopes/írás |  |
> | Műveletek | Microsoft. Storage/storageAccounts/encryptionScopes/írás |  |
> | Műveletek | Microsoft. Storage/storageAccounts/feladatátvétel/művelet | Az ügyfél a rendelkezésre állási problémák esetén képes a feladatátvétel vezérlésére |
> | DataAction | Microsoft. Storage/storageAccounts/fileServices/állnak/Files/actassuperuser/művelet | Fájl-rendszergazdai jogosultságok beolvasása |
> | DataAction | Microsoft. Storage/storageAccounts/fileServices/állnak/Files/delete | Egy fájl vagy mappa törlésének eredményét adja vissza. |
> | DataAction | Microsoft. Storage/storageAccounts/fileServices/állnak/Files/modifypermissions/művelet | Egy fájlra vagy mappára vonatkozó engedély módosításának eredményét adja vissza. |
> | DataAction | Microsoft. Storage/storageAccounts/fileServices/állnak/Files/READ | Egy fájlt/mappát, vagy a fájlok/mappák listáját adja vissza. |
> | DataAction | Microsoft. Storage/storageAccounts/fileServices/állnak/Files/Write | Egy fájl írásának vagy egy mappa létrehozásának eredményét adja vissza. |
> | Műveletek | Microsoft. Storage/storageAccounts/fileServices/olvasás |  |
> | Műveletek | Microsoft. Storage/storageAccounts/fileServices/olvasás | File Service-tulajdonságok beolvasása |
> | Műveletek | Microsoft. Storage/storageAccounts/fileServices/shares/delete |  |
> | Műveletek | Microsoft. Storage/storageAccounts/fileServices/shares/READ |  |
> | Műveletek | Microsoft. Storage/storageAccounts/fileServices/shares/READ |  |
> | Műveletek | Microsoft. Storage/storageAccounts/fileServices/shares/Write |  |
> | Műveletek | Microsoft. Storage/storageAccounts/fileServices/írás |  |
> | Műveletek | Microsoft. Storage/storageAccounts/listAccountSas/művelet | A megadott Storage-fiókhoz tartozó SAS-tokent adja vissza. |
> | Műveletek | Microsoft. Storage/storageAccounts/listkeys műveletének beolvasása/művelet | A megadott Storage-fiókhoz tartozó hozzáférési kulcsokat adja vissza. |
> | Műveletek | Microsoft. Storage/storageAccounts/listServiceSas/művelet | A megadott Storage-fiókhoz tartozó szolgáltatás SAS-jogkivonatát adja vissza. |
> | Műveletek | Microsoft. Storage/storageAccounts/managementPolicies/delete | Storage-fiókok felügyeleti házirendjeinek törlése |
> | Műveletek | Microsoft. Storage/storageAccounts/managementPolicies/olvasás | Storage-felügyeleti fiókok házirendjeinek beolvasása |
> | Műveletek | Microsoft. Storage/storageAccounts/managementPolicies/írás | Storage-fiókok felügyeleti házirendjeinek elhelyezése |
> | Műveletek | Microsoft. Storage/storageAccounts/objectReplicationPolicies/delete |  |
> | Műveletek | Microsoft. Storage/storageAccounts/objectReplicationPolicies/olvasás |  |
> | Műveletek | Microsoft. Storage/storageAccounts/objectReplicationPolicies/írás |  |
> | Műveletek | Microsoft. Storage/storageAccounts/privateEndpointConnectionProxies/delete | Privát végponti kapcsolatok Proxyinak törlése |
> | Műveletek | Microsoft. Storage/storageAccounts/privateEndpointConnectionProxies/olvasás | Privát végponti kapcsolatok proxyjának beolvasása |
> | Műveletek | Microsoft. Storage/storageAccounts/privateEndpointConnectionProxies/írás | Magánhálózati végponti kapcsolatok Proxyinak elhelyezése |
> | Műveletek | Microsoft. Storage/storageAccounts/privateEndpointConnections/delete | Privát végponti kapcsolatok törlése |
> | Műveletek | Microsoft. Storage/storageAccounts/privateEndpointConnections/olvasás | Privát végponti kapcsolatok beszerzése |
> | Műveletek | Microsoft. Storage/storageAccounts/privateEndpointConnections/írás | Magánhálózati végponti kapcsolatok elhelyezése |
> | Műveletek | Microsoft. Storage/storageAccounts/PrivateEndpointConnectionsApproval/művelet | Approve Private Endpoint Connections |
> | Műveletek | Microsoft. Storage/storageAccounts/privateLinkResources/olvasás | StorageAccount-groupids beolvasása |
> | Műveletek | Microsoft. Storage/storageAccounts/queueServices/Queues/delete | Egy várólista törlésének eredményét adja vissza. |
> | DataAction | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/Hozzáadás/művelet | Üzenet hozzáadásának eredményét adja vissza. |
> | DataAction | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/delete | Egy üzenet törlésének eredményét adja vissza. |
> | DataAction | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/Process/Action | Egy üzenet feldolgozásának eredményét adja vissza. |
> | DataAction | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/READ | Egy üzenetet ad vissza. |
> | DataAction | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/Write | Egy üzenet írásának eredményét adja vissza. |
> | Műveletek | Microsoft. Storage/storageAccounts/queueServices/Queues/READ | Várólistát vagy várólisták listáját adja vissza. |
> | Műveletek | Microsoft. Storage/storageAccounts/queueServices/Queues/Write | Egy üzenetsor írásának eredményét adja vissza. |
> | Műveletek | Microsoft. Storage/storageAccounts/queueServices/olvasás | Queue szolgáltatás tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Storage/storageAccounts/queueServices/olvasás | A várólista-szolgáltatás tulajdonságait vagy statisztikáit adja vissza. |
> | Műveletek | Microsoft. Storage/storageAccounts/queueServices/írás | A várólista-szolgáltatás tulajdonságainak beállításának eredményét adja vissza. |
> | Műveletek | Microsoft. Storage/storageAccounts/olvasás | A Storage-fiókok listáját adja vissza, vagy lekéri a megadott Storage-fiók tulajdonságait. |
> | Műveletek | Microsoft. Storage/storageAccounts/regeneratekey/művelet | Újragenerálja a megadott Storage-fiók hozzáférési kulcsait. |
> | Műveletek | Microsoft. Storage/storageAccounts/restoreBlobRanges/művelet | BLOB-tartományok visszaállítása a megadott idő állapotára |
> | Műveletek | Microsoft. Storage/storageAccounts/revokeUserDelegationKeys/művelet | A megadott Storage-fiók összes felhasználói delegálási kulcsának visszavonása. |
> | Műveletek | Microsoft. Storage/storageAccounts/Services/diagnosticSettings/írás | Storage-fiók diagnosztikai beállításainak létrehozása/frissítése. |
> | Műveletek | Microsoft. Storage/storageAccounts/tableServices/olvasás | Table service tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Storage/storageAccounts/írás | Létrehoz egy Storage-fiókot a megadott paraméterekkel, vagy frissíti a tulajdonságokat vagy címkéket, vagy hozzáadja az egyéni tartományt a megadott Storage-fiókhoz. |
> | Műveletek | Microsoft. Storage/használat/olvasás | A megadott előfizetésben lévő erőforrások korlátját és aktuális használati számát adja vissza. |

## <a name="microsoftstoragesync"></a>Microsoft. storagesync

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. storagesync/Locations/checkNameAvailability/Action | Ellenőrzi, hogy a Storage Sync szolgáltatás neve érvényes-e, és nincs-e használatban. |
> | Műveletek | Microsoft. storagesync/helyszínek/munkafolyamatok/műveletek/olvasás | Gets the status of an asynchronous operation |
> | Műveletek | Microsoft. storagesync/Operations/READ | A támogatott műveletek listájának beolvasása |
> | Műveletek | Microsoft. storagesync/regisztráció/művelet | Regisztrálja az előfizetést a Storage Sync Provider számára |
> | Műveletek | Microsoft. storagesync/storageSyncServices/delete | A Storage Sync szolgáltatásainak törlése |
> | Műveletek | Microsoft. storagesync/storageSyncServices/Providers/Microsoft. bepillantást/metricDefinitions/READ | A Storage Sync Services elérhető metrikáinak beolvasása |
> | Műveletek | Microsoft. storagesync/storageSyncServices/READ | A Storage Sync szolgáltatásainak beolvasása |
> | Műveletek | Microsoft. storagesync/storageSyncServices/registeredServers/delete | Bármely regisztrált kiszolgáló törlése |
> | Műveletek | Microsoft. storagesync/storageSyncServices/registeredServers/Providers/Microsoft. bepillantást/metricDefinitions/READ | Gets the available metrics for Registered Server |
> | Műveletek | microsoft.storagesync/storageSyncServices/registeredServers/read | Read any Registered Server |
> | Műveletek | microsoft.storagesync/storageSyncServices/registeredServers/write | Create or Update any Registered Server |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | Delete any Cloud Endpoints |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | Gets the status of an asynchronous backup/restore operation |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | Call this action after backup |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | Call this action after restore |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | Call this action before backup |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | Call this action before restore |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | Read any Cloud Endpoints |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | Restore heartbeat |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/triggerChangeDetection/action | Call this action to trigger detection of changes on a cloud endpoint's file share |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | Create or Update any Cloud Endpoints |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/delete | Delete any Sync Groups |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/providers/Microsoft.Insights/metricDefinitions/read | Gets the available metrics for Sync Groups |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/read | Read any Sync Groups |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | Delete any Server Endpoints |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/providers/Microsoft.Insights/metricDefinitions/read | Gets the available metrics for Server Endpoints |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | Read any Server Endpoints |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | Call this action to recall files to a server |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | Create or Update any Server Endpoints |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/write | Create or Update any Sync Groups |
> | Műveletek | microsoft.storagesync/storageSyncServices/workflows/operationresults/read | Gets the status of an asynchronous operation |
> | Műveletek | microsoft.storagesync/storageSyncServices/workflows/operations/read | Gets the status of an asynchronous operation |
> | Műveletek | microsoft.storagesync/storageSyncServices/workflows/read | Read Workflows |
> | Műveletek | microsoft.storagesync/storageSyncServices/write | Create or Update any Storage Sync Services |
> | Műveletek | microsoft.storagesync/unregister/action | Unregisters the subscription for the Storage Sync Provider |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.StorSimple/managers/accessControlRecords/delete | Deletes the Access Control Records |
> | Műveletek | Microsoft.StorSimple/managers/accessControlRecords/operationResults/read | Lists or gets the Operation Results |
> | Műveletek | Microsoft.StorSimple/managers/accessControlRecords/read | Lists or gets the Access Control Records |
> | Műveletek | Microsoft.StorSimple/managers/accessControlRecords/write | Create or update the Access Control Records |
> | Műveletek | Microsoft.StorSimple/managers/alerts/read | Lists or gets the Alerts |
> | Műveletek | Microsoft.StorSimple/managers/backups/read | A biztonságimásolat-készlet listája vagy beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/bandwidthSettings/delete | Deletes an existing Bandwidth Settings (8000 Series Only) |
> | Műveletek | Microsoft.StorSimple/managers/bandwidthSettings/operationResults/read | List the Operation Results |
> | Műveletek | Microsoft.StorSimple/managers/bandwidthSettings/read | List the Bandwidth Settings (8000 Series Only) |
> | Műveletek | Microsoft.StorSimple/managers/bandwidthSettings/write | Creates a new or updates Bandwidth Settings (8000 Series Only) |
> | Műveletek | Microsoft.StorSimple/managers/certificates/write | Create or update the Certificates |
> | Műveletek | Microsoft.StorSimple/Managers/certificates/write | The Update Resource Certificate operation updates the resource/vault credential certificate. |
> | Műveletek | Microsoft.StorSimple/managers/clearAlerts/action | Clear all the alerts associated with the device manager. |
> | Műveletek | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | List the Cloud Appliance Supported Configurations |
> | Műveletek | Microsoft.StorSimple/managers/configureDevice/action | Configures a device |
> | Műveletek | Microsoft.StorSimple/managers/delete | Deletes the Device Managers |
> | Műveletek | Microsoft.StorSimple/Managers/delete | The Delete Vault operation deletes the specified Azure resource of type 'vault' |
> | Műveletek | Microsoft.StorSimple/managers/devices/alertSettings/operationResults/read | Lists or gets the Operation Results |
> | Műveletek | Microsoft.StorSimple/managers/devices/alertSettings/read | Lists or gets the Alert Settings |
> | Műveletek | Microsoft.StorSimple/managers/devices/alertSettings/write | Create or update the Alert Settings |
> | Műveletek | Microsoft.StorSimple/managers/devices/authorizeForServiceEncryptionKeyRollover/action | A szolgáltatás titkosítási kulcsának átváltásának engedélyezése az eszközökön |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/backupPolicies/biztonsági mentés/művelet | Készítsen manuális biztonsági mentést a szabályzat által védett összes kötetre vonatkozó igény szerinti biztonsági mentés létrehozásához. |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/backupPolicies/delete | Meglévő biztonsági mentési házirendek törlése (csak 8000 sorozat) |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/backupPolicies/operationResults/READ | List the Operation Results |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/backupPolicies/olvasás | A biztonsági mentési házirendek listázása (csak 8000 sorozat) |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/backupPolicies/ütemtervek/törlés | Meglévő ütemtervek törlése |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/backupPolicies/Schedules/operationResults/READ | List the Operation Results |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/backupPolicies/ütemterv/olvasás | Az ütemtervek listázása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/backupPolicies/ütemterv/írás | Új vagy frissítési ütemtervet hoz létre |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/backupPolicies/írás | Új vagy frissítési biztonsági mentési szabályzatok létrehozása (csak 8000 sorozat) |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/biztonsági másolatok/törlés | A biztonságimásolat-készlet törlése |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/biztonsági másolatok/elemek/klónozás/művelet | Megosztás vagy kötet klónozása egy biztonsági mentési elem használatával. |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/biztonsági másolatok/elemek/operationResults/olvasás | Lists or gets the Operation Results |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/Backups/operationResults/READ | Lists or gets the Operation Results |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/biztonsági másolatok/olvasás | A biztonságimásolat-készlet listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/biztonsági másolatok/visszaállítás/művelet | Állítsa vissza az összes kötetet egy biztonságimásolat-készletből. |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/backupScheduleGroups/delete | A biztonsági mentési ütemterv csoportok törlése |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/backupScheduleGroups/operationResults/READ | Lists or gets the Operation Results |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/backupScheduleGroups/olvasás | A biztonsági mentési ütemterv csoportok listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/backupScheduleGroups/írás | A biztonsági mentési ütemterv csoportok létrehozása vagy frissítése |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/chapSettings/delete | A CHAP-beállítások törlése |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/chapSettings/operationResults/READ | Lists or gets the Operation Results |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/chapSettings/olvasás | A CHAP-beállítások listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/chapSettings/írás | A CHAP-beállítások létrehozása vagy frissítése |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/inaktiválás/művelet | Inaktiválja az eszközt. |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/törlés | Az eszközök törlése |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/lemezek/olvasás | A lemezek listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/Letöltés/művelet | Egy eszköz frissítéseinek letöltése. |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/feladatátvétel/művelet | Az eszköz feladatátvétele. |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/feladatátvétel/operationResults/olvasás | Lists or gets the Operation Results |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/failoverTargets/olvasás | Felsorolja vagy lekéri az eszközök feladatátvételi céljait |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/fileservers/biztonsági mentés/művelet | Készítsen biztonsági másolatot a fájlkiszolgálón. |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/fileservers/delete | A fájlkiszolgálók törlése |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/fileservers/mérőszámok/olvasás | Lists or gets the Metrics |
> | Műveletek | Microsoft. StorSimple/menedzserek/eszközök/fileservers/metricsDefinitions/READ | Lists or gets the Metrics Definitions |
> | Műveletek | Microsoft. StorSimple/menedzserek/eszközök/fileservers/operationResults/READ | Lists or gets the Operation Results |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/fileservers/olvasás | Lists or gets the File Servers |
> | Műveletek | Microsoft. StorSimple/Manager/Devices/fileservers/shares/delete | A megosztások törlése |
> | Műveletek | Microsoft. StorSimple/menedzserek/eszközök/fileservek/megosztások/mérőszámok/olvasás | Lists or gets the Metrics |
> | Műveletek | Microsoft. StorSimple/Manager/Devices/fileservers/shares/metricsDefinitions/READ | Lists or gets the Metrics Definitions |
> | Műveletek | Microsoft. StorSimple/Manager/Devices/fileservers/shares/operationResults/READ | Lists or gets the Operation Results |
> | Műveletek | Microsoft. StorSimple/Manager/Devices/fileservers/shares/READ | A megosztások listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/Manager/Devices/fileservers/shares/Write | Megosztások létrehozása vagy frissítése |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/fileservers/Write | Fájlkiszolgálók létrehozása vagy frissítése |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/hardwareComponentGroups/changeControllerPowerState/Action | Hardver-összetevő csoportok vezérlő energiagazdálkodási állapotának módosítása |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/hardwareComponentGroups/operationResults/READ | List the Operation Results |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/hardwareComponentGroups/olvasás | A hardver-összetevő csoportjai listázása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/telepítés/művelet | Frissítések telepítése az eszközön. |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/installUpdates/művelet | Telepíti a frissítéseket az eszközökön (csak 8000 sorozat esetén). |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/iscsiservers/biztonsági mentés/művelet | Készítsen biztonsági másolatot egy iSCSI-kiszolgálóról. |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/iscsiservers/delete | Az iSCSI-kiszolgálók törlése |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/iscsiservers/lemezek/törlés | A lemezek törlése |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/iscsiservers/lemezek/metrikák/olvasás | Lists or gets the Metrics |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/iscsiservers/Disks/metricsDefinitions/READ | Lists or gets the Metrics Definitions |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/iscsiservers/Disks/operationResults/READ | Lists or gets the Operation Results |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/iscsiservers/lemezek/olvasás | A lemezek listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/iscsiservers/lemezek/írás | Lemezek létrehozása vagy frissítése |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/iscsiservers/mérőszámok/olvasás | Lists or gets the Metrics |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/iscsiservers/metricsDefinitions/READ | Lists or gets the Metrics Definitions |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/iscsiservers/operationResults/READ | Lists or gets the Operation Results |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/iscsiservers/olvasás | Lists or gets the iSCSI Servers |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/iscsiservers/írás | ISCSI-kiszolgálók létrehozása vagy frissítése |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/feladatok/megszakítás/művelet | Futó feladat megszakítása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/feladatok/operationResults/olvasás | List the Operation Results |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/feladatok/olvasás | Lists or gets the Jobs |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/listFailoverSets/művelet | Egy meglévő eszközhöz tartozó feladatátvevő készletek listázása (csak 8000 sorozat esetén). |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/listFailoverTargets/művelet | Az eszközök feladatátvételi céljainak listázása (csak 8000 sorozat esetén). |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/mérőszámok/olvasás | Lists or gets the Metrics |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/metricsDefinitions/olvasás | Lists or gets the Metrics Definitions |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/confirmMigration/Action | Ellenőrzi a sikeres áttelepítést, és véglegesíti azt. |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/confirmMigrationStatus/READ | Az áttelepítés megerősítési állapotának listázása |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/Action | Az áttelepítés megerősítési állapotának beolvasása. |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/fetchMigrationEstimate/Action | Az áttelepítési becslési feladatokhoz tartozó állapot beolvasása. |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/fetchMigrationStatus/Action | Az áttelepítés állapotának beolvasása. |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/migrationSourceConfigurations/importálás/művelet | Forrás-konfigurációk importálása az áttelepítéshez |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/migrationEstimate/READ | Az áttelepítési becslés listázása |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/migrationStatus/READ | Az áttelepítés állapotának listázása |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/operationResults/READ | List the Operation Results |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/startMigration/Action | Áttelepítés indítása a forrás-konfigurációk használatával |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/startMigrationEstimate/Action | Az áttelepítési folyamat időtartamának becsléséhez indítsa el a feladatot. |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/networkSettings/operationResults/READ | List the Operation Results |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/networkSettings/olvasás | A hálózati beállítások listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/networkSettings/írás | Új vagy frissített hálózati beállításokat hoz létre |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/operationResults/olvasás | Lists or gets the Operation Results |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/publicEncryptionKey/művelet | Az Eszközkezelő nyilvános titkosítási kulcsának listázása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/publishSupportPackage/művelet | Egy meglévő eszköz támogatási csomagjának közzététele. A StorSimple-támogatási csomag egy könnyen használható mechanizmus, amely az összes releváns naplót gyűjti, hogy segítséget nyújtson Microsoft ügyfélszolgálatanek a StorSimple-eszközök hibáinak elhárításában. |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/olvasás | Az eszközök listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/scanForUpdates/művelet | Frissítések keresése az eszközön. |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/securitySettings/operationResults/READ | Lists or gets the Operation Results |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/securitySettings/olvasás | A biztonsági beállítások listázása |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/securitySettings/syncRemoteManagementCertificate/Action | Egy eszköz távfelügyeleti tanúsítványának szinkronizálása. |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/securitySettings/Update/Action | Frissítse a biztonsági beállításokat. |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/securitySettings/írás | Új vagy frissített biztonsági beállítások létrehozása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/sendTestAlertEmail/művelet | Tesztüzenet küldése e-mailben a konfigurált e-mail-címzetteknek. |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/megosztások/olvasás | A megosztások listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/timeSettings/operationResults/READ | List the Operation Results |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/timeSettings/olvasás | Lists or gets the Time Settings |
> | Műveletek | Microsoft.StorSimple/managers/devices/timeSettings/write | Creates a new or updates Time Settings |
> | Műveletek | Microsoft.StorSimple/managers/devices/updates/operationResults/read | Lists or gets the Operation Results |
> | Műveletek | Microsoft.StorSimple/managers/devices/updateSummary/read | Lists or gets the Update Summary |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/delete | Deletes an existing Volume Containers (8000 Series Only) |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | List the Metrics |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/metricsDefinitions/read | List the Metrics Definitions |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/operationResults/read | List the Operation Results |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/read | List the Volume Containers (8000 Series Only) |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/delete | Deletes an existing Volumes |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | List the Metrics |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | List the Metrics Definitions |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/operationResults/read | List the Operation Results |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/read | List the Volumes |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/write | Creates a new or updates Volumes |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/write | Creates a new or updates Volume Containers (8000 Series Only) |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumes/read | List the Volumes |
> | Műveletek | Microsoft.StorSimple/managers/devices/write | Create or update the Devices |
> | Műveletek | Microsoft.StorSimple/managers/encryptionSettings/read | Lists or gets the Encryption Settings |
> | Műveletek | Microsoft.StorSimple/managers/extendedInformation/delete | Deletes the Extended Vault Information |
> | Műveletek | Microsoft.StorSimple/Managers/extendedInformation/delete | The Get Extended Info operation gets an object's Extended Info representing the Azure resource of type ?vault? |
> | Műveletek | Microsoft.StorSimple/managers/extendedInformation/read | Lists or gets the Extended Vault Information |
> | Műveletek | Microsoft.StorSimple/Managers/extendedInformation/read | The Get Extended Info operation gets an object's Extended Info representing the Azure resource of type ?vault? |
> | Műveletek | Microsoft.StorSimple/managers/extendedInformation/write | Create or update the Extended Vault Information |
> | Műveletek | Microsoft.StorSimple/Managers/extendedInformation/write | The Get Extended Info operation gets an object's Extended Info representing the Azure resource of type ?vault? |
> | Műveletek | Microsoft.StorSimple/managers/features/read | List the Features |
> | Műveletek | Microsoft.StorSimple/managers/fileservers/read | Lists or gets the File Servers |
> | Műveletek | Microsoft.StorSimple/managers/getEncryptionKey/action | Get encryption key for the device manager. |
> | Műveletek | Microsoft.StorSimple/managers/iscsiservers/read | Lists or gets the iSCSI Servers |
> | Műveletek | Microsoft.StorSimple/managers/jobs/read | Lists or gets the Jobs |
> | Műveletek | Microsoft.StorSimple/managers/listActivationKey/action | Gets the activation key of the StorSimple Device Manager. |
> | Műveletek | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | List public encryption keys of a StorSimple Device Manager. |
> | Műveletek | Microsoft.StorSimple/managers/metrics/read | Lists or gets the Metrics |
> | Műveletek | Microsoft.StorSimple/managers/metricsDefinitions/read | Lists or gets the Metrics Definitions |
> | Műveletek | Microsoft.StorSimple/managers/migrateClassicToResourceManager/action | Migrate from Classic To Resource Manager |
> | Műveletek | Microsoft.StorSimple/managers/migrationSourceConfigurations/read | List the Migration Source Configurations (8000 Series Only) |
> | Műveletek | Microsoft.StorSimple/managers/operationResults/read | Lists or gets the Operation Results |
> | Műveletek | Microsoft.StorSimple/managers/provisionCloudAppliance/action | Create a new cloud appliance. |
> | Műveletek | Microsoft.StorSimple/managers/read | Lists or gets the Device Managers |
> | Műveletek | Microsoft.StorSimple/Managers/read | The Get Vault operation gets an object representing the Azure resource of type 'vault' |
> | Műveletek | Microsoft.StorSimple/managers/regenerateActivationKey/action | Regenerate the Activation key for an existing StorSimple Device Manager. |
> | Műveletek | Microsoft.StorSimple/managers/storageAccountCredentials/delete | Deletes the Storage Account Credentials |
> | Műveletek | Microsoft.StorSimple/managers/storageAccountCredentials/operationResults/read | Lists or gets the Operation Results |
> | Műveletek | Microsoft.StorSimple/managers/storageAccountCredentials/read | Lists or gets the Storage Account Credentials |
> | Műveletek | Microsoft.StorSimple/managers/storageAccountCredentials/write | Create or update the Storage Account Credentials |
> | Műveletek | Microsoft.StorSimple/managers/storageDomains/delete | Deletes the Storage Domains |
> | Műveletek | Microsoft.StorSimple/managers/storageDomains/operationResults/read | Lists or gets the Operation Results |
> | Műveletek | Microsoft.StorSimple/managers/storageDomains/read | Lists or gets the Storage Domains |
> | Műveletek | Microsoft.StorSimple/managers/storageDomains/write | Create or update the Storage Domains |
> | Műveletek | Microsoft.StorSimple/managers/write | Create or update the Device Managers |
> | Műveletek | Microsoft.StorSimple/Managers/write | Create Vault operation creates an Azure resource of type 'vault' |
> | Műveletek | Microsoft.StorSimple/operations/read | Lists or gets the Operations |
> | Műveletek | Microsoft.StorSimple/register/action | Register Provider Microsoft.StorSimple |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.StreamAnalytics/locations/quotas/Read | Read Stream Analytics Subscription Quota |
> | Műveletek | Microsoft.StreamAnalytics/operations/Read | Read Stream Analytics Operations |
> | Műveletek | Microsoft.StreamAnalytics/Register/action | Register subscription with Stream Analytics Resource Provider |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/Delete | Delete Stream Analytics Job |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | Delete Stream Analytics Job Function |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/functions/operationresults/Read | Read operation results for Stream Analytics Job Function |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/functions/Read | Read Stream Analytics Job Function |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | Retrieve Default Definition of a Stream Analytics Job Function |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | Test Stream Analytics Job Function |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/functions/Write | Write Stream Analytics Job Function |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/inputs/Delete | Delete Stream Analytics Job Input |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/inputs/operationresults/Read | Read operation results for Stream Analytics Job Input |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/inputs/Read | Read Stream Analytics Job Input |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/inputs/Sample/action | Sample Stream Analytics Job Input |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | Test Stream Analytics Job Input |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | Write Stream Analytics Job Input |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | Read Metric Definitions |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | Read operation results for Stream Analytics Job |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | Delete Stream Analytics Job Output |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | Read operation results for Stream Analytics Job Output |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | Read Stream Analytics Job Output |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | Test Stream Analytics Job Output |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | Write Stream Analytics Job Output |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read | Read diagnostic setting. |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write | Write diagnostic setting. |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | Gets the available logs for streamingjobs |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | Gets the available metrics for streamingjobs |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/PublishEdgePackage/action | Publish edge package for Stream Analytics Job |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/Read | Read Stream Analytics Job |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/Scale/action | Scale Stream Analytics Job |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/Start/action | Stream Analytics-feladat indítása |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/Stop/action | Stop Stream Analytics Job |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | Delete Stream Analytics Job Transformation |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | Read Stream Analytics Job Transformation |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | Write Stream Analytics Job Transformation |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/Write | Write Stream Analytics Job |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Subscription/cancel/action | Cancels the Subscription |
> | Műveletek | Microsoft.Subscription/CreateSubscription/action | Azure-előfizetés létrehozása |
> | Műveletek | Microsoft.Subscription/register/action | Registers Subscription with Microsoft.Subscription resource provider |
> | Műveletek | Microsoft.Subscription/rename/action | Renames the subscription |
> | Műveletek | Microsoft.Subscription/SubscriptionDefinitions/read | Get an Azure subscription definition within a management group. |
> | Műveletek | Microsoft.Subscription/SubscriptionDefinitions/write | Create an Azure subscription definition |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Support/register/action | Regisztrálás a támogatási erőforrás-szolgáltatóra. |
> | Műveletek | Microsoft.Support/supportTickets/read | Támogatási jegy részleteinek lekérése (beleértve az állapotot, súlyosságot, kapcsolattartási adatokat és kommunikációkat) vagy az összes előfizetés támogatási jegyeinek lekérése. |
> | Műveletek | Microsoft.Support/supportTickets/write | Támogatási jegy létrehozása vagy frissítése. Támogatási jegyet technikai, számlázási, kvótákkal kapcsolatos vagy előfizetés-kezelési problémákkal kapcsolatban hozhat létre. A meglévő támogatási jegyeknek frissítheti a súlyosságát, kapcsolattartási adatait és kommunikációit. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | Deletes the access policy. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | Get the properties of an access policy. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | Creates a new access policy for an environment, or updates an existing access policy. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/delete | Deletes the environment. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/eventsources/delete | Deletes the event source. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/eventsources/read | Get the properties of an event source. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/eventsources/write | Creates a new event source for an environment, or updates an existing event source. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/read | Get the properties of an environment. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | Deletes the reference data set. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | Get the properties of a reference data set. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | Creates a new reference data set for an environment, or updates an existing reference data set. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/status/read | Get the status of the environment, state of its associated operations like ingress. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/write | Creates a new environment, or updates an existing environment. |
> | Műveletek | Microsoft.TimeSeriesInsights/register/action | Registers the subscription for the Time Series Insights resource provider and enables the creation of Time Series Insights environments. |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.VisualStudio/Account/Delete | Fiók törlése |
> | Műveletek | Microsoft.VisualStudio/Account/Extension/Read | Read Account/Extension |
> | Műveletek | Microsoft.VisualStudio/Account/Project/Read | Read Account/Project |
> | Műveletek | Microsoft.VisualStudio/Account/Project/Write | Set Account/Project |
> | Műveletek | Microsoft.VisualStudio/Account/Read | Read Account |
> | Műveletek | Microsoft.VisualStudio/Account/Write | Set Account |
> | Műveletek | Microsoft.VisualStudio/Extension/Delete | Delete Extension |
> | Műveletek | Microsoft.VisualStudio/Extension/Read | Read Extension |
> | Műveletek | Microsoft.VisualStudio/Extension/Write | Set Extension |
> | Műveletek | Microsoft.VisualStudio/Project/Delete | Delete Project |
> | Műveletek | Microsoft.VisualStudio/Project/Read | Read Project |
> | Műveletek | Microsoft.VisualStudio/Project/Write | Set Project |
> | Műveletek | Microsoft.VisualStudio/Register/Action | Register Azure Subscription with Microsoft.VisualStudio provider |

## <a name="microsoftweb"></a>microsoft.web

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | microsoft.web/apimanagementaccounts/apiacls/read | Get Api Management Accounts Apiacls. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/apiacls/delete | Delete Api Management Accounts APIs Apiacls. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/apiacls/read | Get Api Management Accounts APIs Apiacls. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/apiacls/write | Update Api Management Accounts APIs Apiacls. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/connectionacls/read | Get Api Management Accounts APIs Connectionacls. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/connections/confirmconsentcode/action | Confirm Consent Code Api Management Accounts APIs Connections. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/delete | Delete Api Management Accounts APIs Connections Connectionacls. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/read | Get Api Management Accounts APIs Connections Connectionacls. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/write | Update Api Management Accounts APIs Connections Connectionacls. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/connections/delete | Delete Api Management Accounts APIs Connections. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/connections/getconsentlinks/action | Get Consent Links for Api Management Accounts APIs Connections. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/connections/listconnectionkeys/action | List Connection Keys Api Management Accounts APIs Connections. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/connections/listsecrets/action | List Secrets Api Management Accounts APIs Connections. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/connections/read | Get Api Management Accounts APIs Connections. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/connections/write | Update Api Management Accounts APIs Connections. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/delete | Delete Api Management Accounts APIs. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/delete | Delete Api Management Accounts APIs Localized Definitions. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/read | Get Api Management Accounts APIs Localized Definitions. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/write | Update Api Management Accounts APIs Localized Definitions. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/read | Get Api Management Accounts APIs. |
> | Műveletek | microsoft.web/apimanagementaccounts/apis/write | Update Api Management Accounts APIs. |
> | Műveletek | microsoft.web/apimanagementaccounts/connectionacls/read | Get Api Management Accounts Connectionacls. |
> | Műveletek | microsoft.web/availablestacks/read | Get Available Stacks. |
> | Műveletek | Microsoft.Web/certificates/Delete | Delete an existing certificate. |
> | Műveletek | Microsoft.Web/certificates/Read | Get the list of certificates. |
> | Műveletek | Microsoft.Web/certificates/Write | Add a new certificate or update an existing one. |
> | Műveletek | microsoft.web/checknameavailability/read | Check if resource name is available. |
> | Műveletek | microsoft.web/classicmobileservices/read | Get Classic Mobile Services. |
> | Műveletek | Microsoft.Web/connectionGateways/Delete | Deletes a Connection Gateway. |
> | Műveletek | Microsoft.Web/connectionGateways/Join/Action | Joins a Connection Gateway. |
> | Műveletek | Microsoft.Web/connectionGateways/ListStatus/Action | Lists status of a Connection Gateway. |
> | Műveletek | Microsoft.Web/connectionGateways/Move/Action | Moves a Connection Gateway. |
> | Műveletek | Microsoft.Web/connectionGateways/Read | Get the list of Connection Gateways. |
> | Műveletek | Microsoft.Web/connectionGateways/Write | Creates or updates a Connection Gateway. |
> | Műveletek | microsoft.web/connections/confirmconsentcode/action | Confirm Connections Consent Code. |
> | Műveletek | Microsoft.Web/connections/Delete | Deletes a Connection. |
> | Műveletek | Microsoft.Web/connections/Join/Action | Joins a Connection. |
> | Műveletek | microsoft.web/connections/listconsentlinks/action | List Consent Links for Connections. |
> | Műveletek | Microsoft.Web/connections/Move/Action | Moves a Connection. |
> | Műveletek | Microsoft.Web/connections/Read | Get the list of Connections. |
> | Műveletek | Microsoft.Web/connections/Write | Creates or updates a Connection. |
> | Műveletek | Microsoft.Web/customApis/Delete | Deletes a Custom API. |
> | Műveletek | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | Extracts API definition from a WSDL. |
> | Műveletek | Microsoft.Web/customApis/Join/Action | Joins a Custom API. |
> | Műveletek | Microsoft.Web/customApis/listWsdlInterfaces/Action | Lists WSDL interfaces for a Custom API. |
> | Műveletek | Microsoft.Web/customApis/Move/Action | Moves a Custom API. |
> | Műveletek | Microsoft.Web/customApis/Read | Get the list of Custom API. |
> | Műveletek | Microsoft.Web/customApis/Write | Creates or updates a Custom API. |
> | Műveletek | Microsoft.Web/deletedSites/Read | Get the properties of a Deleted Web App |
> | Műveletek | microsoft.web/deploymentlocations/read | Get Deployment Locations. |
> | Műveletek | Microsoft.Web/geoRegions/Read | Get the list of Geo regions. |
> | Műveletek | microsoft.web/hostingenvironments/capacities/read | Get Hosting Environments Capacities. |
> | Műveletek | Microsoft.Web/hostingEnvironments/Delete | Delete an App Service Environment |
> | Műveletek | microsoft.web/hostingenvironments/detectors/read | Get Hosting Environments Detectors. |
> | Műveletek | microsoft.web/hostingenvironments/diagnostics/read | Get Hosting Environments Diagnostics. |
> | Műveletek | microsoft.web/hostingenvironments/inboundnetworkdependenciesendpoints/read | Get the network endpoints of all inbound dependencies. |
> | Műveletek | Microsoft.Web/hostingEnvironments/Join/Action | Joins an App Service Environment |
> | Műveletek | microsoft.web/hostingenvironments/metricdefinitions/read | Get Hosting Environments Metric Definitions. |
> | Műveletek | microsoft.web/hostingenvironments/multirolepools/metricdefinitions/read | Get Hosting Environments MultiRole Pools Metric Definitions. |
> | Műveletek | microsoft.web/hostingenvironments/multirolepools/metrics/read | Get Hosting Environments MultiRole Pools Metrics. |
> | Műveletek | Microsoft.Web/hostingEnvironments/multiRolePools/Read | Get the properties of a FrontEnd Pool in an App Service Environment |
> | Műveletek | microsoft.web/hostingenvironments/multirolepools/skus/read | Get Hosting Environments MultiRole Pools SKUs. |
> | Műveletek | microsoft.web/hostingenvironments/multirolepools/usages/read | Get Hosting Environments MultiRole Pools Usages. |
> | Műveletek | Microsoft.Web/hostingEnvironments/multiRolePools/Write | Create a new FrontEnd Pool in an App Service Environment or update an existing one |
> | Műveletek | microsoft.web/hostingenvironments/operations/read | Get Hosting Environments Operations. |
> | Műveletek | microsoft.web/hostingenvironments/outboundnetworkdependenciesendpoints/read | Get the network endpoints of all outbound dependencies. |
> | Műveletek | Microsoft.Web/hostingEnvironments/PrivateEndpointConnectionsApproval/action | Approve Private Endpoint Connections |
> | Műveletek | Microsoft.Web/hostingEnvironments/Read | Get the properties of an App Service Environment |
> | Műveletek | Microsoft.Web/hostingEnvironments/reboot/Action | Reboot all machines in an App Service Environment |
> | Műveletek | microsoft.web/hostingenvironments/resume/action | Resume Hosting Environments. |
> | Műveletek | microsoft.web/hostingenvironments/serverfarms/read | Get Hosting Environments App Service Plans. |
> | Műveletek | microsoft.web/hostingenvironments/sites/read | Get Hosting Environments Web Apps. |
> | Műveletek | microsoft.web/hostingenvironments/suspend/action | Suspend Hosting Environments. |
> | Műveletek | microsoft.web/hostingenvironments/usages/read | Get Hosting Environments Usages. |
> | Műveletek | microsoft.web/hostingenvironments/workerpools/metricdefinitions/read | Get Hosting Environments Workerpools Metric Definitions. |
> | Műveletek | microsoft.web/hostingenvironments/workerpools/metrics/read | Get Hosting Environments Workerpools Metrics. |
> | Műveletek | Microsoft.Web/hostingEnvironments/workerPools/Read | Get the properties of a Worker Pool in an App Service Environment |
> | Műveletek | microsoft.web/hostingenvironments/workerpools/skus/read | Get Hosting Environments Workerpools SKUs. |
> | Műveletek | microsoft.web/hostingenvironments/workerpools/usages/read | Get Hosting Environments Workerpools Usages. |
> | Műveletek | Microsoft.Web/hostingEnvironments/workerPools/Write | Create a new Worker Pool in an App Service Environment or update an existing one |
> | Műveletek | Microsoft.Web/hostingEnvironments/Write | Create a new App Service Environment or update existing one |
> | Műveletek | microsoft.web/ishostingenvironmentnameavailable/read | Get if Hosting Environment Name is available. |
> | Műveletek | microsoft.web/ishostnameavailable/read | Check if Hostname is Available. |
> | Műveletek | microsoft.web/isusernameavailable/read | Check if Username is available. |
> | Műveletek | Microsoft.Web/listSitesAssignedToHostName/Read | Get names of sites assigned to hostname. |
> | Műveletek | microsoft.web/locations/apioperations/read | Get Locations API Operations. |
> | Műveletek | microsoft.web/locations/connectiongatewayinstallations/read | Get Locations Connection Gateway Installations. |
> | Műveletek | microsoft.web/locations/deleteVirtualNetworkOrSubnets/action | Vnet or subnet deletion notification for Locations. |
> | Műveletek | microsoft.web/locations/extractapidefinitionfromwsdl/action | Extract Api Definition from WSDL for Locations. |
> | Műveletek | microsoft.web/locations/listwsdlinterfaces/action | List WSDL Interfaces for Locations. |
> | Műveletek | microsoft.web/locations/managedapis/apioperations/read | Get Locations Managed API Operations. |
> | Műveletek | Microsoft.Web/locations/managedapis/Join/Action | Joins a Managed API. |
> | Műveletek | microsoft.web/locations/managedapis/read | Get Locations Managed APIs. |
> | Műveletek | microsoft.web/locations/operationResults/read | Get Operations. |
> | Műveletek | microsoft.web/locations/operations/read | Get Operations. |
> | Műveletek | microsoft.web/operations/read | Get Operations. |
> | Műveletek | microsoft.web/publishingusers/read | Get Publishing Users. |
> | Műveletek | microsoft.web/publishingusers/write | Update Publishing Users. |
> | Műveletek | Microsoft.Web/recommendations/Read | Get the list of recommendations for subscriptions. |
> | Műveletek | microsoft.web/register/action | Register Microsoft.Web resource provider for the subscription. |
> | Műveletek | microsoft.web/resourcehealthmetadata/read | Get Resource Health Metadata. |
> | Műveletek | microsoft.web/serverfarms/capabilities/read | Get App Service Plans Capabilities. |
> | Műveletek | Microsoft.Web/serverfarms/Delete | Delete an existing App Service Plan |
> | Műveletek | Microsoft.Web/serverfarms/eventGridFilters/delete | Delete Event Grid Filter on server farm. |
> | Műveletek | Microsoft.Web/serverfarms/eventGridFilters/read | Get Event Grid Filter on server farm. |
> | Műveletek | Microsoft.Web/serverfarms/eventGridFilters/write | Put Event Grid Filter on server farm. |
> | Műveletek | microsoft.web/serverfarms/firstpartyapps/settings/delete | Delete App Service Plans First Party Apps Settings. |
> | Műveletek | microsoft.web/serverfarms/firstpartyapps/settings/read | Get App Service Plans First Party Apps Settings. |
> | Műveletek | microsoft.web/serverfarms/firstpartyapps/settings/write | Update App Service Plans First Party Apps Settings. |
> | Műveletek | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/delete | Delete App Service Plans Hybrid Connection Namespaces Relays. |
> | Műveletek | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/read | Get App Service Plans Hybrid Connection Namespaces Relays. |
> | Műveletek | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/sites/read | Get App Service Plans Hybrid Connection Namespaces Relays Web Apps. |
> | Műveletek | microsoft.web/serverfarms/hybridconnectionplanlimits/read | Get App Service Plans Hybrid Connection Plan Limits. |
> | Műveletek | microsoft.web/serverfarms/hybridconnectionrelays/read | Get App Service Plans Hybrid Connection Relays. |
> | Műveletek | microsoft.web/serverfarms/metricdefinitions/read | Get App Service Plans Metric Definitions. |
> | Műveletek | microsoft.web/serverfarms/metrics/read | Get App Service Plans Metrics. |
> | Műveletek | microsoft.web/serverfarms/operationresults/read | Get App Service Plans Operation Results. |
> | Műveletek | Microsoft.Web/serverfarms/Read | Get the properties on an App Service Plan |
> | Műveletek | Microsoft.Web/serverfarms/restartSites/Action | Restart all Web Apps in an App Service Plan |
> | Műveletek | microsoft.web/serverfarms/sites/read | Get App Service Plans Web Apps. |
> | Műveletek | microsoft.web/serverfarms/skus/read | Get App Service Plans SKUs. |
> | Műveletek | microsoft.web/serverfarms/usages/read | Get App Service Plans Usages. |
> | Műveletek | microsoft.web/serverfarms/virtualnetworkconnections/gateways/write | Update App Service Plans Virtual Network Connections Gateways. |
> | Műveletek | microsoft.web/serverfarms/virtualnetworkconnections/read | Get App Service Plans Virtual Network Connections. |
> | Műveletek | microsoft.web/serverfarms/virtualnetworkconnections/routes/delete | Delete App Service Plans Virtual Network Connections Routes. |
> | Műveletek | microsoft.web/serverfarms/virtualnetworkconnections/routes/read | Get App Service Plans Virtual Network Connections Routes. |
> | Műveletek | microsoft.web/serverfarms/virtualnetworkconnections/routes/write | Update App Service Plans Virtual Network Connections Routes. |
> | Műveletek | microsoft.web/serverfarms/workers/reboot/action | Reboot App Service Plans Workers. |
> | Műveletek | Microsoft.Web/serverfarms/Write | Create a new App Service Plan or update an existing one |
> | Műveletek | microsoft.web/sites/analyzecustomhostname/read | Analyze Custom Hostname. |
> | Műveletek | Microsoft.Web/sites/applySlotConfig/Action | Apply web app slot configuration from target slot to the current web app |
> | Műveletek | Microsoft.Web/sites/backup/Action | Create a new web app backup |
> | Műveletek | microsoft.web/sites/backup/read | Get Web Apps Backup. |
> | Műveletek | microsoft.web/sites/backup/write | Update Web Apps Backup. |
> | Műveletek | microsoft.web/sites/backups/action | Discovers an existing app backup that can be restored from a blob in Azure storage. |
> | Műveletek | microsoft.web/sites/backups/delete | Delete Web Apps Backups. |
> | Műveletek | microsoft.web/sites/backups/list/action | List Web Apps Backups. |
> | Műveletek | Microsoft.Web/sites/backups/Read | Get the properties of a web app's backup |
> | Műveletek | microsoft.web/sites/backups/restore/action | Restore Web Apps Backups. |
> | Műveletek | microsoft.web/sites/backups/write | Update Web Apps Backups. |
> | Műveletek | microsoft.web/sites/config/delete | Delete Web Apps Config. |
> | Műveletek | Microsoft.Web/sites/config/list/Action | List Web App's security sensitive settings, such as publishing credentials, app settings and connection strings |
> | Műveletek | Microsoft.Web/sites/config/Read | Get Web App configuration settings |
> | Műveletek | microsoft.web/sites/config/snapshots/read | Get Web Apps Config Snapshots. |
> | Műveletek | Microsoft.Web/sites/config/Write | Update Web App's configuration settings |
> | Műveletek | microsoft.web/sites/containerlogs/action | Get Zipped Container Logs for Web App. |
> | Műveletek | microsoft.web/sites/containerlogs/download/action | Download Web Apps Container Logs. |
> | Műveletek | microsoft.web/sites/continuouswebjobs/delete | Delete Web Apps Continuous Web Jobs. |
> | Műveletek | microsoft.web/sites/continuouswebjobs/read | Get Web Apps Continuous Web Jobs. |
> | Műveletek | microsoft.web/sites/continuouswebjobs/start/action | Start Web Apps Continuous Web Jobs. |
> | Műveletek | microsoft.web/sites/continuouswebjobs/stop/action | Stop Web Apps Continuous Web Jobs. |
> | Műveletek | Microsoft.Web/sites/Delete | Delete an existing Web App |
> | Műveletek | microsoft.web/sites/deployments/delete | Delete Web Apps Deployments. |
> | Műveletek | microsoft.web/sites/deployments/log/read | Get Web Apps Deployments Log. |
> | Műveletek | microsoft.web/sites/deployments/read | Get Web Apps Deployments. |
> | Műveletek | microsoft.web/sites/deployments/write | Update Web Apps Deployments. |
> | Műveletek | microsoft.web/sites/detectors/read | Get Web Apps Detectors. |
> | Műveletek | microsoft.web/sites/diagnostics/analyses/execute/Action | Run Web Apps Diagnostics Analysis. |
> | Műveletek | microsoft.web/sites/diagnostics/analyses/read | Get Web Apps Diagnostics Analysis. |
> | Műveletek | microsoft.web/sites/diagnostics/aspnetcore/read | Get Web Apps Diagnostics for ASP.NET Core app. |
> | Műveletek | microsoft.web/sites/diagnostics/autoheal/read | Get Web Apps Diagnostics Autoheal. |
> | Műveletek | microsoft.web/sites/diagnostics/deployment/read | Get Web Apps Diagnostics Deployment. |
> | Műveletek | microsoft.web/sites/diagnostics/deployments/read | Get Web Apps Diagnostics Deployments. |
> | Műveletek | microsoft.web/sites/diagnostics/detectors/execute/Action | Run Web Apps Diagnostics Detector. |
> | Műveletek | microsoft.web/sites/diagnostics/detectors/read | Get Web Apps Diagnostics Detector. |
> | Műveletek | microsoft.web/sites/diagnostics/failedrequestsperuri/read | Get Web Apps Diagnostics Failed Requests Per Uri. |
> | Műveletek | microsoft.web/sites/diagnostics/frebanalysis/read | Get Web Apps Diagnostics FREB Analysis. |
> | Műveletek | microsoft.web/sites/diagnostics/loganalyzer/read | Get Web Apps Diagnostics Log Analyzer. |
> | Műveletek | microsoft.web/sites/diagnostics/read | Get Web Apps Diagnostics Categories. |
> | Műveletek | microsoft.web/sites/diagnostics/runtimeavailability/read | Get Web Apps Diagnostics Runtime Availability. |
> | Műveletek | microsoft.web/sites/diagnostics/servicehealth/read | Get Web Apps Diagnostics Service Health. |
> | Műveletek | microsoft.web/sites/diagnostics/sitecpuanalysis/read | Get Web Apps Diagnostics Site CPU Analysis. |
> | Műveletek | microsoft.web/sites/diagnostics/sitecrashes/read | Get Web Apps Diagnostics Site Crashes. |
> | Műveletek | microsoft.web/sites/diagnostics/sitelatency/read | Get Web Apps Diagnostics Site Latency. |
> | Műveletek | microsoft.web/sites/diagnostics/sitememoryanalysis/read | Get Web Apps Diagnostics Site Memory Analysis. |
> | Műveletek | microsoft.web/sites/diagnostics/siterestartsettingupdate/read | Get Web Apps Diagnostics Site Restart Setting Update. |
> | Műveletek | microsoft.web/sites/diagnostics/siterestartuserinitiated/read | Get Web Apps Diagnostics Site Restart User Initiated. |
> | Műveletek | microsoft.web/sites/diagnostics/siteswap/read | Get Web Apps Diagnostics Site Swap. |
> | Műveletek | microsoft.web/sites/diagnostics/threadcount/read | Get Web Apps Diagnostics Thread Count. |
> | Műveletek | microsoft.web/sites/diagnostics/workeravailability/read | Get Web Apps Diagnostics Workeravailability. |
> | Műveletek | microsoft.web/sites/diagnostics/workerprocessrecycle/read | Get Web Apps Diagnostics Worker Process Recycle. |
> | Műveletek | microsoft.web/sites/domainownershipidentifiers/read | Get Web Apps Domain Ownership Identifiers. |
> | Műveletek | microsoft.web/sites/domainownershipidentifiers/write | Update Web Apps Domain Ownership Identifiers. |
> | Műveletek | Microsoft.Web/sites/eventGridFilters/delete | Delete Event Grid Filter on web app. |
> | Műveletek | Microsoft.Web/sites/eventGridFilters/read | Get Event Grid Filter on web app. |
> | Műveletek | Microsoft.Web/sites/eventGridFilters/write | Put Event Grid Filter on web app. |
> | Műveletek | microsoft.web/sites/extensions/delete | Delete Web Apps Site Extensions. |
> | Műveletek | microsoft.web/sites/extensions/read | Get Web Apps Site Extensions. |
> | Műveletek | microsoft.web/sites/extensions/write | Update Web Apps Site Extensions. |
> | Műveletek | microsoft.web/sites/functions/action | Functions Web Apps. |
> | Műveletek | microsoft.web/sites/functions/delete | Delete Web Apps Functions. |
> | Műveletek | microsoft.web/sites/functions/keys/delete | Delete Function keys. |
> | Műveletek | microsoft.web/sites/functions/keys/write | Update Function keys. |
> | Műveletek | microsoft.web/sites/functions/listkeys/action | List Function keys. |
> | Műveletek | microsoft.web/sites/functions/listsecrets/action | List Function secrets. |
> | Műveletek | microsoft.web/sites/functions/masterkey/read | Get Web Apps Functions Masterkey. |
> | Műveletek | microsoft.web/sites/functions/properties/read | Read Web Apps Functions Properties. |
> | Műveletek | microsoft.web/sites/functions/properties/write | Update Web Apps Functions Properties. |
> | Műveletek | microsoft.web/sites/functions/read | Get Web Apps Functions. |
> | Műveletek | microsoft.web/sites/functions/token/read | Get Web Apps Functions Token. |
> | Műveletek | microsoft.web/sites/functions/write | Update Web Apps Functions. |
> | Műveletek | microsoft.web/sites/host/functionkeys/delete | Delete Functions Host Function keys. |
> | Műveletek | microsoft.web/sites/host/functionkeys/write | Update Functions Host Function keys. |
> | Műveletek | microsoft.web/sites/host/listkeys/action | List Functions Host keys. |
> | Műveletek | microsoft.web/sites/host/listsyncstatus/action | List Sync Function Triggers Status. |
> | Műveletek | microsoft.web/sites/host/properties/read | Read Web Apps Functions Host Properties. |
> | Műveletek | microsoft.web/sites/host/sync/action | Sync Function Triggers. |
> | Műveletek | microsoft.web/sites/host/systemkeys/delete | Delete Functions Host System keys. |
> | Műveletek | microsoft.web/sites/host/systemkeys/write | Update Functions Host System keys. |
> | Műveletek | microsoft.web/sites/hostnamebindings/delete | Delete Web Apps Hostname Bindings. |
> | Műveletek | microsoft.web/sites/hostnamebindings/read | Get Web Apps Hostname Bindings. |
> | Műveletek | microsoft.web/sites/hostnamebindings/write | Update Web Apps Hostname Bindings. |
> | Műveletek | microsoft.web/sites/hostruntime/functions/keys/read | Get Web Apps Hostruntime Functions Keys. |
> | Műveletek | Microsoft.Web/sites/hostruntime/host/_master/read | Get Function App's master key for admin operations |
> | Műveletek | Microsoft.Web/sites/hostruntime/host/action | Perform Function App runtime action like sync triggers, add functions, invoke functions, delete functions etc. |
> | Műveletek | microsoft.web/sites/hostruntime/host/read | Get Web Apps Hostruntime Host. |
> | Műveletek | microsoft.web/sites/hybridconnection/delete | Delete Web Apps Hybrid Connection. |
> | Műveletek | microsoft.web/sites/hybridconnection/read | Get Web Apps Hybrid Connection. |
> | Műveletek | microsoft.web/sites/hybridconnection/write | Update Web Apps Hybrid Connection. |
> | Műveletek | microsoft.web/sites/hybridconnectionnamespaces/relays/delete | Delete Web Apps Hybrid Connection Namespaces Relays. |
> | Műveletek | microsoft.web/sites/hybridconnectionnamespaces/relays/listkeys/action | List Keys Web Apps Hybrid Connection Namespaces Relays. |
> | Műveletek | microsoft.web/sites/hybridconnectionnamespaces/relays/read | Get Web Apps Hybrid Connection Namespaces Relays. |
> | Műveletek | microsoft.web/sites/hybridconnectionnamespaces/relays/write | Update Web Apps Hybrid Connection Namespaces Relays. |
> | Műveletek | microsoft.web/sites/hybridconnectionrelays/read | Get Web Apps Hybrid Connection Relays. |
> | Műveletek | microsoft.web/sites/instances/deployments/delete | Delete Web Apps Instances Deployments. |
> | Műveletek | microsoft.web/sites/instances/deployments/read | Get Web Apps Instances Deployments. |
> | Műveletek | microsoft.web/sites/instances/extensions/log/read | Get Web Apps Instances Extensions Log. |
> | Műveletek | microsoft.web/sites/instances/extensions/processes/read | Get Web Apps Instances Extensions Processes. |
> | Műveletek | microsoft.web/sites/instances/extensions/read | Get Web Apps Instances Extensions. |
> | Műveletek | microsoft.web/sites/instances/processes/delete | Delete Web Apps Instances Processes. |
> | Műveletek | microsoft.web/sites/instances/processes/modules/read | Get Web Apps Instances Processes Modules. |
> | Műveletek | microsoft.web/sites/instances/processes/read | Get Web Apps Instances Processes. |
> | Műveletek | microsoft.web/sites/instances/processes/threads/read | Get Web Apps Instances Processes Threads. |
> | Műveletek | microsoft.web/sites/instances/read | Get Web Apps Instances. |
> | Műveletek | microsoft.web/sites/listsyncfunctiontriggerstatus/action | List Sync Function Trigger Status. |
> | Műveletek | microsoft.web/sites/metricdefinitions/read | Get Web Apps Metric Definitions. |
> | Műveletek | microsoft.web/sites/metrics/read | Get Web Apps Metrics. |
> | Műveletek | microsoft.web/sites/metricsdefinitions/read | Get Web Apps Metrics Definitions. |
> | Műveletek | microsoft.web/sites/migratemysql/action | Migrate MySql Web Apps. |
> | Műveletek | microsoft.web/sites/migratemysql/read | Get Web Apps Migrate MySql. |
> | Műveletek | microsoft.web/sites/networktrace/action | Network Trace Web Apps. |
> | Műveletek | microsoft.web/sites/networktraces/operationresults/read | Get Web Apps Network Trace Operation Results. |
> | Műveletek | microsoft.web/sites/newpassword/action | Newpassword Web Apps. |
> | Műveletek | microsoft.web/sites/operationresults/read | Get Web Apps Operation Results. |
> | Műveletek | microsoft.web/sites/operations/read | Get Web Apps Operations. |
> | Műveletek | microsoft.web/sites/perfcounters/read | Get Web Apps Performance Counters. |
> | Műveletek | microsoft.web/sites/premieraddons/delete | Delete Web Apps Premier Addons. |
> | Műveletek | microsoft.web/sites/premieraddons/read | Get Web Apps Premier Addons. |
> | Műveletek | microsoft.web/sites/premieraddons/write | Update Web Apps Premier Addons. |
> | Műveletek | microsoft.web/sites/privateaccess/read | Get data around private site access enablement and authorized Virtual Networks that can access the site. |
> | Műveletek | Microsoft.Web/sites/PrivateEndpointConnectionsApproval/action | Approve Private Endpoint Connections |
> | Műveletek | microsoft.web/sites/processes/modules/read | Get Web Apps Processes Modules. |
> | Műveletek | microsoft.web/sites/processes/read | Get Web Apps Processes. |
> | Műveletek | microsoft.web/sites/processes/threads/read | Get Web Apps Processes Threads. |
> | Műveletek | microsoft.web/sites/publiccertificates/delete | Delete Web Apps Public Certificates. |
> | Műveletek | microsoft.web/sites/publiccertificates/read | Get Web Apps Public Certificates. |
> | Műveletek | microsoft.web/sites/publiccertificates/write | Update Web Apps Public Certificates. |
> | Műveletek | Microsoft.Web/sites/publish/Action | Publish a Web App |
> | Műveletek | Microsoft.Web/sites/publishxml/Action | Get publishing profile xml for a Web App |
> | Műveletek | microsoft.web/sites/publishxml/read | Get Web Apps Publishing XML. |
> | Műveletek | Microsoft.Web/sites/Read | Get the properties of a Web App |
> | Műveletek | microsoft.web/sites/recommendationhistory/read | Get Web Apps Recommendation History. |
> | Műveletek | microsoft.web/sites/recommendations/disable/action | Disable Web Apps Recommendations. |
> | Műveletek | Microsoft.Web/sites/recommendations/Read | Get the list of recommendations for web app. |
> | Műveletek | microsoft.web/sites/recover/action | Recover Web Apps. |
> | Műveletek | Microsoft.Web/sites/resetSlotConfig/Action | Reset web app configuration |
> | Műveletek | microsoft.web/sites/resourcehealthmetadata/read | Get Web Apps Resource Health Metadata. |
> | Műveletek | Microsoft.Web/sites/restart/Action | Restart a Web App |
> | Műveletek | microsoft.web/sites/restore/read | Get Web Apps Restore. |
> | Műveletek | microsoft.web/sites/restore/write | Restore Web Apps. |
> | Műveletek | microsoft.web/sites/restorefrombackupblob/action | Restore Web App From Backup Blob. |
> | Műveletek | microsoft.web/sites/restorefromdeletedapp/action | Restore Web Apps From Deleted App. |
> | Műveletek | microsoft.web/sites/restoresnapshot/action | Restore Web Apps Snapshots. |
> | Műveletek | microsoft.web/sites/siteextensions/delete | Delete Web Apps Site Extensions. |
> | Műveletek | microsoft.web/sites/siteextensions/read | Get Web Apps Site Extensions. |
> | Műveletek | microsoft.web/sites/siteextensions/write | Update Web Apps Site Extensions. |
> | Műveletek | microsoft.web/sites/slots/analyzecustomhostname/read | Get Web Apps Slots Analyze Custom Hostname. |
> | Műveletek | Microsoft.Web/sites/slots/applySlotConfig/Action | Apply web app slot configuration from target slot to the current slot. |
> | Műveletek | Microsoft.Web/sites/slots/backup/Action | Create new Web App Slot backup. |
> | Műveletek | microsoft.web/sites/slots/backup/read | Get Web Apps Slots Backup. |
> | Műveletek | microsoft.web/sites/slots/backup/write | Update Web Apps Slots Backup. |
> | Műveletek | microsoft.web/sites/slots/backups/action | Discover Web Apps Slots Backups. |
> | Műveletek | microsoft.web/sites/slots/backups/delete | Delete Web Apps Slots Backups. |
> | Műveletek | microsoft.web/sites/slots/backups/list/action | List Web Apps Slots Backups. |
> | Műveletek | Microsoft.Web/sites/slots/backups/Read | Get the properties of a web app slots' backup |
> | Műveletek | microsoft.web/sites/slots/backups/restore/action | Restore Web Apps Slots Backups. |
> | Műveletek | microsoft.web/sites/slots/config/delete | Delete Web Apps Slots Config. |
> | Műveletek | Microsoft.Web/sites/slots/config/list/Action | List Web App Slot's security sensitive settings, such as publishing credentials, app settings and connection strings |
> | Műveletek | Microsoft.Web/sites/slots/config/Read | Get Web App Slot's configuration settings |
> | Műveletek | Microsoft.Web/sites/slots/config/Write | Update Web App Slot's configuration settings |
> | Műveletek | microsoft.web/sites/slots/containerlogs/action | Get Zipped Container Logs for Web App Slot. |
> | Műveletek | microsoft.web/sites/slots/containerlogs/download/action | Download Web Apps Slots Container Logs. |
> | Műveletek | microsoft.web/sites/slots/continuouswebjobs/delete | Delete Web Apps Slots Continuous Web Jobs. |
> | Műveletek | microsoft.web/sites/slots/continuouswebjobs/read | Get Web Apps Slots Continuous Web Jobs. |
> | Műveletek | microsoft.web/sites/slots/continuouswebjobs/start/action | Start Web Apps Slots Continuous Web Jobs. |
> | Műveletek | microsoft.web/sites/slots/continuouswebjobs/stop/action | Stop Web Apps Slots Continuous Web Jobs. |
> | Műveletek | Microsoft.Web/sites/slots/Delete | Delete an existing Web App Slot |
> | Műveletek | microsoft.web/sites/slots/deployments/delete | Delete Web Apps Slots Deployments. |
> | Műveletek | microsoft.web/sites/slots/deployments/log/read | Get Web Apps Slots Deployments Log. |
> | Műveletek | microsoft.web/sites/slots/deployments/read | Get Web Apps Slots Deployments. |
> | Műveletek | microsoft.web/sites/slots/deployments/write | Update Web Apps Slots Deployments. |
> | Műveletek | microsoft.web/sites/slots/detectors/read | Get Web Apps Slots Detectors. |
> | Műveletek | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | Run Web Apps Slots Diagnostics Analysis. |
> | Műveletek | microsoft.web/sites/slots/diagnostics/analyses/read | Get Web Apps Slots Diagnostics Analysis. |
> | Műveletek | microsoft.web/sites/slots/diagnostics/aspnetcore/read | Get Web Apps Slots Diagnostics for ASP.NET Core app. |
> | Műveletek | microsoft.web/sites/slots/diagnostics/autoheal/read | Get Web Apps Slots Diagnostics Autoheal. |
> | Műveletek | microsoft.web/sites/slots/diagnostics/deployment/read | Get Web Apps Slots Diagnostics Deployment. |
> | Műveletek | microsoft.web/sites/slots/diagnostics/deployments/read | Get Web Apps Slots Diagnostics Deployments. |
> | Műveletek | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | Run Web Apps Slots Diagnostics Detector. |
> | Műveletek | microsoft.web/sites/slots/diagnostics/detectors/read | Get Web Apps Slots Diagnostics Detector. |
> | Műveletek | microsoft.web/sites/slots/diagnostics/frebanalysis/read | Get Web Apps Slots Diagnostics FREB Analysis. |
> | Műveletek | microsoft.web/sites/slots/diagnostics/loganalyzer/read | Get Web Apps Slots Diagnostics Log Analyzer. |
> | Műveletek | microsoft.web/sites/slots/diagnostics/read | Get Web Apps Slots Diagnostics. |
> | Műveletek | microsoft.web/sites/slots/diagnostics/runtimeavailability/read | Get Web Apps Slots Diagnostics Runtime Availability. |
> | Műveletek | microsoft.web/sites/slots/diagnostics/servicehealth/read | Get Web Apps Slots Diagnostics Service Health. |
> | Műveletek | microsoft.web/sites/slots/diagnostics/sitecpuanalysis/read | Get Web Apps Slots Diagnostics Site CPU Analysis. |
> | Műveletek | microsoft.web/sites/slots/diagnostics/sitecrashes/read | Get Web Apps Slots Diagnostics Site Crashes. |
> | Műveletek | microsoft.web/sites/slots/diagnostics/sitelatency/read | Get Web Apps Slots Diagnostics Site Latency. |
> | Műveletek | microsoft.web/sites/slots/diagnostics/sitememoryanalysis/read | Get Web Apps Slots Diagnostics Site Memory Analysis. |
> | Műveletek | microsoft.web/sites/slots/diagnostics/siterestartsettingupdate/read | Get Web Apps Slots Diagnostics Site Restart Setting Update. |
> | Műveletek | microsoft.web/sites/slots/diagnostics/siterestartuserinitiated/read | Get Web Apps Slots Diagnostics Site Restart User Initiated. |
> | Műveletek | microsoft.web/sites/slots/diagnostics/siteswap/read | Get Web Apps Slots Diagnostics Site Swap. |
> | Műveletek | microsoft.web/sites/slots/diagnostics/threadcount/read | Get Web Apps Slots Diagnostics Thread Count. |
> | Műveletek | microsoft.web/sites/slots/diagnostics/workeravailability/read | Get Web Apps Slots Diagnostics Workeravailability. |
> | Műveletek | microsoft.web/sites/slots/diagnostics/workerprocessrecycle/read | Get Web Apps Slots Diagnostics Worker Process Recycle. |
> | Műveletek | microsoft.web/sites/slots/domainownershipidentifiers/read | Get Web Apps Slots Domain Ownership Identifiers. |
> | Műveletek | microsoft.web/sites/slots/functions/listsecrets/action | List Secrets Web Apps Slots Functions. |
> | Műveletek | microsoft.web/sites/slots/functions/read | Get Web Apps Slots Functions. |
> | Műveletek | microsoft.web/sites/slots/hostnamebindings/delete | Delete Web Apps Slots Hostname Bindings. |
> | Műveletek | microsoft.web/sites/slots/hostnamebindings/read | Get Web Apps Slots Hostname Bindings. |
> | Műveletek | microsoft.web/sites/slots/hostnamebindings/write | Update Web Apps Slots Hostname Bindings. |
> | Műveletek | microsoft.web/sites/slots/hybridconnection/delete | Delete Web Apps Slots Hybrid Connection. |
> | Műveletek | microsoft.web/sites/slots/hybridconnection/read | Get Web Apps Slots Hybrid Connection. |
> | Műveletek | microsoft.web/sites/slots/hybridconnection/write | Update Web Apps Slots Hybrid Connection. |
> | Műveletek | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/delete | Delete Web Apps Slots Hybrid Connection Namespaces Relays. |
> | Műveletek | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/write | Update Web Apps Slots Hybrid Connection Namespaces Relays. |
> | Műveletek | microsoft.web/sites/slots/hybridconnectionrelays/read | Get Web Apps Slots Hybrid Connection Relays. |
> | Műveletek | microsoft.web/sites/slots/instances/deployments/read | Get Web Apps Slots Instances Deployments. |
> | Műveletek | microsoft.web/sites/slots/instances/processes/delete | Delete Web Apps Slots Instances Processes. |
> | Műveletek | microsoft.web/sites/slots/instances/processes/read | Get Web Apps Slots Instances Processes. |
> | Műveletek | microsoft.web/sites/slots/instances/read | Get Web Apps Slots Instances. |
> | Műveletek | microsoft.web/sites/slots/metricdefinitions/read | Get Web Apps Slots Metric Definitions. |
> | Műveletek | microsoft.web/sites/slots/metrics/read | Get Web Apps Slots Metrics. |
> | Műveletek | microsoft.web/sites/slots/migratemysql/read | Get Web Apps Slots Migrate MySql. |
> | Műveletek | microsoft.web/sites/slots/networktrace/action | Network Trace Web Apps Slots. |
> | Műveletek | microsoft.web/sites/slots/networktraces/operationresults/read | Get Web Apps Slots Network Trace Operation Results. |
> | Műveletek | microsoft.web/sites/slots/newpassword/action | Newpassword Web Apps Slots. |
> | Műveletek | microsoft.web/sites/slots/operationresults/read | Get Web Apps Slots Operation Results. |
> | Műveletek | microsoft.web/sites/slots/operations/read | Get Web Apps Slots Operations. |
> | Műveletek | microsoft.web/sites/slots/perfcounters/read | Get Web Apps Slots Performance Counters. |
> | Műveletek | microsoft.web/sites/slots/phplogging/read | Get Web Apps Slots Phplogging. |
> | Műveletek | microsoft.web/sites/slots/premieraddons/delete | Delete Web Apps Slots Premier Addons. |
> | Műveletek | microsoft.web/sites/slots/premieraddons/read | Get Web Apps Slots Premier Addons. |
> | Műveletek | microsoft.web/sites/slots/premieraddons/write | Update Web Apps Slots Premier Addons. |
> | Műveletek | microsoft.web/sites/slots/processes/read | Get Web Apps Slots Processes. |
> | Műveletek | microsoft.web/sites/slots/publiccertificates/delete | Delete Web Apps Slots Public Certificates. |
> | Műveletek | microsoft.web/sites/slots/publiccertificates/read | Get Web Apps Slots Public Certificates. |
> | Műveletek | microsoft.web/sites/slots/publiccertificates/write | Create or Update Web Apps Slots Public Certificates. |
> | Műveletek | Microsoft.Web/sites/slots/publish/Action | Publish a Web App Slot |
> | Műveletek | Microsoft.Web/sites/slots/publishxml/Action | Get publishing profile xml for Web App Slot |
> | Műveletek | Microsoft.Web/sites/slots/Read | Get the properties of a Web App deployment slot |
> | Műveletek | microsoft.web/sites/slots/recover/action | Recover Web Apps Slots. |
> | Műveletek | Microsoft.Web/sites/slots/resetSlotConfig/Action | Reset web app slot configuration |
> | Műveletek | microsoft.web/sites/slots/resourcehealthmetadata/read | Get Web Apps Slots Resource Health Metadata. |
> | Műveletek | Microsoft.Web/sites/slots/restart/Action | Restart a Web App Slot |
> | Műveletek | microsoft.web/sites/slots/restore/read | Get Web Apps Slots Restore. |
> | Műveletek | microsoft.web/sites/slots/restore/write | Restore Web Apps Slots. |
> | Műveletek | microsoft.web/sites/slots/restorefrombackupblob/action | Restore Web Apps Slot From Backup Blob. |
> | Műveletek | microsoft.web/sites/slots/restorefromdeletedapp/action | Restore Web App Slots From Deleted App. |
> | Műveletek | microsoft.web/sites/slots/restoresnapshot/action | Restore Web Apps Slots Snapshots. |
> | Műveletek | microsoft.web/sites/slots/siteextensions/delete | Delete Web Apps Slots Site Extensions. |
> | Műveletek | microsoft.web/sites/slots/siteextensions/read | Get Web Apps Slots Site Extensions. |
> | Műveletek | microsoft.web/sites/slots/siteextensions/write | Update Web Apps Slots Site Extensions. |
> | Műveletek | Microsoft.Web/sites/slots/slotsdiffs/Action | Get differences in configuration between web app and slots |
> | Műveletek | Microsoft.Web/sites/slots/slotsswap/Action | Swap Web App deployment slots |
> | Műveletek | microsoft.web/sites/slots/snapshots/read | Get Web Apps Slots Snapshots. |
> | Műveletek | Microsoft.Web/sites/slots/sourcecontrols/Delete | Delete Web App Slot's source control configuration settings |
> | Műveletek | Microsoft.Web/sites/slots/sourcecontrols/Read | Get Web App Slot's source control configuration settings |
> | Műveletek | Microsoft.Web/sites/slots/sourcecontrols/Write | Update Web App Slot's source control configuration settings |
> | Műveletek | Microsoft.Web/sites/slots/start/Action | Start a Web App Slot |
> | Műveletek | Microsoft.Web/sites/slots/stop/Action | Stop a Web App Slot |
> | Műveletek | microsoft.web/sites/slots/sync/action | Sync Web Apps Slots. |
> | Műveletek | microsoft.web/sites/slots/triggeredwebjobs/delete | Delete Web Apps Slots Triggered WebJobs. |
> | Műveletek | microsoft.web/sites/slots/triggeredwebjobs/read | Get Web Apps Slots Triggered WebJobs. |
> | Műveletek | microsoft.web/sites/slots/triggeredwebjobs/run/action | Run Web Apps Slots Triggered WebJobs. |
> | Műveletek | microsoft.web/sites/slots/usages/read | Get Web Apps Slots Usages. |
> | Műveletek | microsoft.web/sites/slots/virtualnetworkconnections/delete | Delete Web Apps Slots Virtual Network Connections. |
> | Műveletek | microsoft.web/sites/slots/virtualnetworkconnections/gateways/write | Update Web Apps Slots Virtual Network Connections Gateways. |
> | Műveletek | microsoft.web/sites/slots/virtualnetworkconnections/read | Get Web Apps Slots Virtual Network Connections. |
> | Műveletek | microsoft.web/sites/slots/virtualnetworkconnections/write | Update Web Apps Slots Virtual Network Connections. |
> | Műveletek | microsoft.web/sites/slots/webjobs/read | Get Web Apps Slots WebJobs. |
> | Műveletek | Microsoft.Web/sites/slots/Write | Create a new Web App Slot or update an existing one |
> | Műveletek | Microsoft.Web/sites/slotsdiffs/Action | Get differences in configuration between web app and slots |
> | Műveletek | Microsoft.Web/sites/slotsswap/Action | Swap Web App deployment slots |
> | Műveletek | microsoft.web/sites/snapshots/read | Get Web Apps Snapshots. |
> | Műveletek | Microsoft.Web/sites/sourcecontrols/Delete | Delete Web App's source control configuration settings |
> | Műveletek | Microsoft.Web/sites/sourcecontrols/Read | Get Web App's source control configuration settings |
> | Műveletek | Microsoft.Web/sites/sourcecontrols/Write | Update Web App's source control configuration settings |
> | Műveletek | Microsoft.Web/sites/start/Action | Start a Web App |
> | Műveletek | Microsoft.Web/sites/stop/Action | Stop a Web App |
> | Műveletek | microsoft.web/sites/sync/action | Sync Web Apps. |
> | Műveletek | microsoft.web/sites/syncfunctiontriggers/action | Sync Function Triggers. |
> | Műveletek | microsoft.web/sites/triggeredwebjobs/delete | Delete Web Apps Triggered WebJobs. |
> | Műveletek | microsoft.web/sites/triggeredwebjobs/history/read | Get Web Apps Triggered WebJobs History. |
> | Műveletek | microsoft.web/sites/triggeredwebjobs/read | Get Web Apps Triggered WebJobs. |
> | Műveletek | microsoft.web/sites/triggeredwebjobs/run/action | Run Web Apps Triggered WebJobs. |
> | Műveletek | microsoft.web/sites/usages/read | Get Web Apps Usages. |
> | Műveletek | microsoft.web/sites/virtualnetworkconnections/delete | Delete Web Apps Virtual Network Connections. |
> | Műveletek | microsoft.web/sites/virtualnetworkconnections/gateways/read | Get Web Apps Virtual Network Connections Gateways. |
> | Műveletek | microsoft.web/sites/virtualnetworkconnections/gateways/write | Update Web Apps Virtual Network Connections Gateways. |
> | Műveletek | microsoft.web/sites/virtualnetworkconnections/read | Get Web Apps Virtual Network Connections. |
> | Műveletek | microsoft.web/sites/virtualnetworkconnections/write | Update Web Apps Virtual Network Connections. |
> | Műveletek | microsoft.web/sites/webjobs/read | Get Web Apps WebJobs. |
> | Műveletek | Microsoft.Web/sites/Write | Create a new Web App or update an existing one |
> | Műveletek | microsoft.web/skus/read | Get SKUs. |
> | Műveletek | microsoft.web/sourcecontrols/read | Get Source Controls. |
> | Műveletek | microsoft.web/sourcecontrols/write | Update Source Controls. |
> | Műveletek | microsoft.web/unregister/action | Unregister Microsoft.Web resource provider for the subscription. |
> | Műveletek | microsoft.web/validate/action | Validate . |
> | Műveletek | microsoft.web/verifyhostingenvironmentvnet/action | Verify Hosting Environment Vnet. |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.WorkloadMonitor/components/read | Gets components for the resource |
> | Műveletek | Microsoft.WorkloadMonitor/componentsSummary/read | Gets summary of components |
> | Műveletek | Microsoft.WorkloadMonitor/monitorInstances/read | Gets instances of monitors for the resource |
> | Műveletek | Microsoft.WorkloadMonitor/monitorInstancesSummary/read | Gets summary of monitor instances |
> | Műveletek | Microsoft.WorkloadMonitor/monitors/read | Gets monitors for the resource |
> | Műveletek | Microsoft.WorkloadMonitor/monitors/write | Configure monitor for the resource |
> | Műveletek | Microsoft.WorkloadMonitor/notificationSettings/read | Gets notification settings for the resource |
> | Műveletek | Microsoft.WorkloadMonitor/notificationSettings/write | Configure notification settings for the resource |
> | Műveletek | Microsoft.WorkloadMonitor/operations/read | Gets the supported operations |

## <a name="next-steps"></a>Következő lépések

- [Match resource provider to service](../azure-resource-manager/azure-services-resource-providers.md)
- [Egyéni szerepkörök Azure-erőforrásokhoz](custom-roles.md)
- [Beépített szerepkörök Azure-erőforrásokhoz](built-in-roles.md)
