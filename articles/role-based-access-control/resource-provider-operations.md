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
ms.openlocfilehash: b00e699eb49d88e41dff18d6c86024c9b0537e50
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872315"
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
> | Műveletek | Microsoft. aadiam/diagnosticsettings/delete | Diagnosztikai beállítások törlése |
> | Műveletek | Microsoft. aadiam/diagnosticsettings/READ | Diagnosztikai beállítások olvasása |
> | Műveletek | Microsoft. aadiam/diagnosticsettings/Write | Diagnosztikai beállítások írása |
> | Műveletek | Microsoft. aadiam/diagnosticsettingscategories/READ | Diagnosztikai beállítások kategóriáinak olvasása |
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
> | Műveletek | Microsoft. AnalysisServices/Locations/operationresults/READ | A megadott műveleti eredmény információinak beolvasása. |
> | Műveletek | Microsoft. AnalysisServices/Locations/operationstatuses/READ | Lekéri a megadott műveleti állapot adatait. |
> | Műveletek | Microsoft. AnalysisServices/Operations/READ | A műveletek információinak lekérése |
> | Műveletek | Microsoft. AnalysisServices/regisztráció/művelet | Regisztrálja Analysis Services erőforrás-szolgáltatót. |
> | Műveletek | Microsoft. AnalysisServices/kiszolgálók/törlés | Törli a Analysis Server. |
> | Műveletek | Microsoft. AnalysisServices/Servers/listGatewayStatus/Action | A kiszolgálóhoz társított átjáró állapotának listázása. |
> | Műveletek | Microsoft. AnalysisServices/kiszolgálók/olvasás | A megadott Analysis Server információinak beolvasása. |
> | Műveletek | Microsoft. AnalysisServices/kiszolgálók/folytatás/művelet | A Analysis Server folytatása. |
> | Műveletek | Microsoft. AnalysisServices/kiszolgálók/SKU/READ | A kiszolgáló rendelkezésre álló SKU-adatainak beolvasása |
> | Műveletek | Microsoft. AnalysisServices/kiszolgálók/felfüggesztés/művelet | Felfüggeszti a Analysis Server. |
> | Műveletek | Microsoft. AnalysisServices/kiszolgálók/írás | Létrehozza vagy frissíti a megadott Analysis Server. |
> | Műveletek | Microsoft. AnalysisServices/SKU/READ | A SKU-ra vonatkozó információk beolvasása |

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
> | Műveletek | Microsoft. Authorization/házirendek/deployIfNotExists/művelet | A Azure Policy kiértékelésének eredményeképpen végrehajtott művelet "deployIfNotExists" hatással |
> | Műveletek | Microsoft. Authorization/policyAssignments/delete | Szabályzat-hozzárendelés törlése a megadott hatókörben. |
> | Műveletek | Microsoft. Authorization/policyAssignments/olvasás | Szabályzat-hozzárendelés adatainak beolvasása. |
> | Műveletek | Microsoft. Authorization/policyAssignments/írás | Szabályzat-hozzárendelés létrehozása a megadott hatókörben. |
> | Műveletek | Microsoft. Authorization/policyDefinitions/delete | Házirend-definíció törlése. |
> | Műveletek | Microsoft. Authorization/policyDefinitions/olvasás | Házirend-definíció adatainak beolvasása. |
> | Műveletek | Microsoft. Authorization/policyDefinitions/írás | Hozzon létre egy egyéni szabályzat-definíciót. |
> | Műveletek | Microsoft. Authorization/policySetDefinitions/delete | Házirend-készlet definíciójának törlése. |
> | Műveletek | Microsoft. Authorization/policySetDefinitions/olvasás | Házirend-készlet definíciójának beolvasása. |
> | Műveletek | Microsoft. Authorization/policySetDefinitions/írás | Hozzon létre egy egyéni házirend-készlet definícióját. |
> | Műveletek | Microsoft. Authorization/providerOperations/olvasás | A szerepkör-definíciókban használható összes erőforrás-szolgáltató műveleteinek beolvasása. |
> | Műveletek | Microsoft. Authorization/roleAssignments/delete | Szerepkör-hozzárendelés törlése a megadott hatókörben. |
> | Műveletek | Microsoft. Authorization/roleAssignments/olvasás | Szerepkör-hozzárendelés adatainak beolvasása. |
> | Műveletek | Microsoft.Authorization/roleAssignments/write | Szerepkör-hozzárendelés létrehozása a megadott hatókörben. |
> | Műveletek | Microsoft. Authorization/roleDefinitions/delete | A megadott egyéni szerepkör-definíció törlése. |
> | Műveletek | Microsoft. Authorization/roleDefinitions/olvasás | Szerepkör-definíció adatainak beolvasása. |
> | Műveletek | Microsoft. Authorization/roleDefinitions/írás | Egyéni szerepkör-definíció létrehozása vagy frissítése megadott engedélyekkel és hozzárendelhető hatókörökkel. |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Automation/automationAccounts/agentRegistrationInformation/READ | Azure Automation DSC regisztrációs adatainak beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/agentRegistrationInformation/regenerateKey/művelet | Azure Automation DSC-kulcsok újragenerálása iránti kérés írása |
> | Műveletek | Microsoft. Automation/automationAccounts/tanúsítványok/törlés | Azure Automation tanúsítvány törlése |
> | Műveletek | Microsoft. Automation/automationAccounts/Certificates/getCount/Action | A tanúsítványok számának beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/tanúsítványok/olvasás | Azure Automation-tanúsítvány beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/tanúsítványok/írás | Létrehoz vagy frissít egy Azure Automation tanúsítvány-eszközt |
> | Műveletek | Microsoft. Automation/automationAccounts/compilationjobs/READ | Egy Azure Automation DSC fordításának beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/compilationjobs/READ | Egy Azure Automation DSC fordításának beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/compilationjobs/Write | Egy Azure Automation DSC fordításának írása |
> | Műveletek | Microsoft. Automation/automationAccounts/compilationjobs/Write | Egy Azure Automation DSC fordításának írása |
> | Műveletek | Microsoft. Automation/automationAccounts/konfigurációk/tartalom/olvasás | A konfigurációs adathordozó tartalmának beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/konfigurációk/törlés | Azure Automation DSC tartalmának törlése |
> | Műveletek | Microsoft. Automation/automationAccounts/konfigurációk/getCount/művelet | Beolvas egy Azure Automation DSC tartalmának számát |
> | Műveletek | Microsoft. Automation/automationAccounts/konfigurációk/olvasás | Azure Automation DSC tartalmának beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/konfigurációk/írás | Egy Azure Automation DSC tartalmának írása |
> | Műveletek | Microsoft. Automation/automationAccounts/Connections/delete | Azure Automation-összekötő törlése |
> | Műveletek | Microsoft. Automation/automationAccounts/Connections/getCount/Action | A kapcsolatok számának beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/kapcsolatok/olvasás | Azure Automation a kapcsolatok adategységének beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/kapcsolatok/írás | Létrehoz vagy frissít egy Azure Automation-összekötő eszközt |
> | Műveletek | Microsoft. Automation/automationAccounts/connectionTypes/delete | Egy Azure Automation típusú kapcsolattípus-eszköz törlése |
> | Műveletek | Microsoft. Automation/automationAccounts/connectionTypes/READ | Azure Automation kapcsolattípus-eszköz beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/connectionTypes/Write | Létrehoz egy Azure Automation kapcsolattípus-eszközt |
> | Műveletek | Microsoft. Automation/automationAccounts/hitelesítő adatok/törlés | Azure Automation hitelesítőadat-eszköz törlése |
> | Műveletek | Microsoft. Automation/automationAccounts/hitelesítő adatok/getCount/művelet | A hitelesítő adatok számának beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/hitelesítő adatok/olvasás | Azure Automation hitelesítőadat-eszköz beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/hitelesítő adatok/írás | Egy Azure Automation hitelesítőadat-eszköz létrehozása vagy frissítése |
> | Műveletek | Microsoft. Automation/automationAccounts/delete | Azure Automation fiók törlése |
> | Műveletek | Microsoft. Automation/automationAccounts/hybridRunbookWorkerGroups/delete | Hibrid Runbook-feldolgozó erőforrások törlése |
> | Műveletek | Microsoft. Automation/automationAccounts/hybridRunbookWorkerGroups/READ | Hibrid Runbook Worker-erőforrások beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/feladatok/kimenet/olvasás | A feladatok kimenetének beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/feladatok/olvasás | Azure Automation feladatok beolvasása |
> | Műveletek | Microsoft. Automation/automationAccounts/feladatok/folytatás/művelet | Egy Azure Automation feladatot folytat |
> | Műveletek | Microsoft. Automation/automationAccounts/Jobs/runbookContent/művelet | Lekéri a Azure Automation runbook tartalmát a feladatok végrehajtásának időpontjában. |
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
> | Műveletek | Microsoft. ClassicCompute/Operations/READ | A műveletek listájának beolvasása. |
> | Műveletek | Microsoft. ClassicCompute/operationStatuses/READ | Az erőforrás műveleti állapotának beolvasása. |
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
> | Műveletek | Microsoft. ClassicCompute/virtualMachines/Write | Virtuális gépek hozzáadása vagy módosítása. |

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
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/Disks/operationStatuses/READ | Az erőforrás műveleti állapotának beolvasása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/lemezek/olvasás | A Storage-fiók lemezét adja vissza. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/lemezek/írás | Egy Storage-fiók lemezét adja meg. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/fileServices/Providers/Microsoft. bepillantást/diagnosticSettings/READ | A diagnosztikai beállítások beolvasása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/fileServices/Providers/Microsoft. bepillantást/diagnosticSettings/Write | Diagnosztikai beállítások hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/fileServices/Providers/Microsoft. bepillantást/metricDefinitions/READ | A metrikák definícióinak beolvasása. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/images/delete | Egy adott Storage-fiók rendszerképének törlése. Elavult. A "Microsoft. ClassicStorage/storageAccounts/lemezképet" használata |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/images/operationstatuses/READ | A Storage-fiók rendszerképének műveleti állapotát adja vissza. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/images/READ | A Storage-fiók rendszerképét adja vissza. Elavult. A "Microsoft. ClassicStorage/storageAccounts/lemezképet" használata |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/Listkeys műveletének beolvasása/művelet | Felsorolja a Storage-fiókok hozzáférési kulcsait. |
> | Műveletek | Microsoft. ClassicStorage/storageAccounts/operationStatuses/READ | Az erőforrás műveleti állapotának beolvasása. |
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
> | Műveletek | Microsoft. számítás/helyszínek/műveletek/olvasás | Aszinkron művelet állapotának beolvasása |
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
> | Műveletek | Microsoft. Tárolószolgáltatás/Locations/Operations/READ | Aszinkron művelet állapotának beolvasása |
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
> | Műveletek | Microsoft. Tárolószolgáltatás/managedClusters/privateEndpointConnectionsApproval/művelet | Meghatározza, hogy a felhasználó jóváhagyhat-e privát végponti kapcsolatokat |
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
> | Műveletek | Microsoft. Databricks/Locations/operationstatuses/READ | Az erőforrás műveleti állapotának beolvasása. |
> | Műveletek | Microsoft. Databricks/Operations/READ | A műveletek listájának beolvasása. |
> | Műveletek | Microsoft. Databricks/regisztráció/művelet | Regisztráljon a Databricks. |
> | Műveletek | Microsoft. Databricks/munkaterületek/törlés | Eltávolít egy Databricks-munkaterületet. |
> | Műveletek | Microsoft. Databricks/munkaterületek/szolgáltatók/Microsoft. bepillantást/diagnosticSettings/READ | A Databricks munkaterület elérhető diagnosztikai beállításainak megadása |
> | Műveletek | Microsoft. Databricks/munkaterületek/szolgáltatók/Microsoft. bepillantások/diagnosticSettings/írás | Diagnosztikai beállítások hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. Databricks/munkaterületek/szolgáltatók/Microsoft. bepillantást/logDefinitions/READ | A Databricks munkaterület elérhető naplózási definícióinak beolvasása |
> | Műveletek | Microsoft. Databricks/munkaterületek/olvasás | A Databricks-munkaterületek listájának beolvasása. |
> | Műveletek | Microsoft. Databricks/munkaterületek/refreshPermissions/művelet | Munkaterületre vonatkozó engedélyek frissítése |
> | Műveletek | Microsoft. Databricks/munkaterületek/updateDenyAssignment/művelet | Munkaterület felügyelt erőforráscsoport nem műveleteinek megtagadási hozzárendelésének frissítése |
> | Műveletek | Microsoft. Databricks/munkaterület/virtualNetworkPeerings/delete | Virtuális hálózati társak törlése |
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
> | Műveletek | Microsoft. DBforMariaDB/checkNameAvailability/művelet | Győződjön meg arról, hogy az adott kiszolgálónév elérhető-e a világ egy adott előfizetéshez való kiépítés céljából. |
> | Műveletek | Microsoft. DBforMariaDB/Locations/azureAsyncOperation/READ | MariaDB-kiszolgáló műveleti eredményeinek visszaadása |
> | Műveletek | Microsoft. DBforMariaDB/Locations/operationResults/READ | ResourceGroup-alapú MariaDB-kiszolgáló műveleti eredményeinek visszaadása |
> | Műveletek | Microsoft. DBforMariaDB/Locations/operationResults/READ | MariaDB-kiszolgáló műveleti eredményeinek visszaadása |
> | Műveletek | Microsoft. DBforMariaDB/Locations/performanceTiers/READ | A rendelkezésre álló teljesítményi rétegek listáját adja vissza. |
> | Műveletek | Microsoft. DBforMariaDB/Locations/privateEndpointConnectionAzureAsyncOperation/READ | Egy privát végponti kapcsolatok műveletének eredményét kapja meg |
> | Műveletek | Microsoft. DBforMariaDB/Locations/privateEndpointConnectionOperationResults/READ | Egy privát végponti kapcsolatok műveletének eredményét kapja meg |
> | Műveletek | Microsoft. DBforMariaDB/Locations/privateEndpointConnectionProxyAzureAsyncOperation/READ | A magánhálózati végponti kapcsolatok proxyjának eredményének beolvasása |
> | Műveletek | Microsoft. DBforMariaDB/Locations/privateEndpointConnectionProxyOperationResults/READ | A magánhálózati végponti kapcsolatok proxyjának eredményének beolvasása |
> | Műveletek | Microsoft. DBforMariaDB/Locations/securityAlertPoliciesAzureAsyncOperation/READ | A kiszolgáló fenyegetés-észlelési műveletének eredményét visszaadó lista. |
> | Műveletek | Microsoft. DBforMariaDB/Locations/securityAlertPoliciesOperationResults/READ | A kiszolgáló fenyegetés-észlelési műveletének eredményét visszaadó lista. |
> | Műveletek | Microsoft. DBforMariaDB/Locations/serverKeyAzureAsyncOperation/READ | Folyamatban lévő műveletek beolvasása transzparens adattitkosítási kiszolgáló kulcsain |
> | Műveletek | Microsoft. DBforMariaDB/Locations/serverKeyOperationResults/READ | Folyamatban lévő műveletek beolvasása transzparens adattitkosítási kiszolgáló kulcsain |
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
> | Műveletek | Microsoft. DBforMariaDB/kiszolgálók/kulcsok/törlés | Töröl egy meglévő kiszolgálói kulcsot. |
> | Műveletek | Microsoft. DBforMariaDB/kiszolgálók/kulcsok/olvasás | Visszaadja a kiszolgálói kulcsok listáját, vagy lekéri a megadott kiszolgálói kulcs tulajdonságait. |
> | Műveletek | Microsoft. DBforMariaDB/kiszolgálók/kulcsok/írás | Létrehoz egy kulcsot a megadott paraméterekkel, vagy frissíti a megadott kiszolgálói kulcs tulajdonságait vagy címkéit. |
> | Műveletek | Microsoft. DBforMariaDB/kiszolgálók/naplófájlok/olvasás | A MariaDB-naplófájlok listájának visszaadása. |
> | Műveletek | Microsoft. DBforMariaDB/Servers/privateEndpointConnectionProxies/delete | Meglévő privát végponti kapcsolatkezelő törlése |
> | Műveletek | Microsoft. DBforMariaDB/Servers/privateEndpointConnectionProxies/READ | A privát végponti kapcsolatok proxyinak listáját adja vissza, vagy beolvassa a megadott privát végpont-összekötő tulajdonságait. |
> | Műveletek | Microsoft. DBforMariaDB/Servers/privateEndpointConnectionProxies/validate/Action | Egy privát végponti kapcsolatok ellenőrzése az NRP oldalról |
> | Műveletek | Microsoft. DBforMariaDB/Servers/privateEndpointConnectionProxies/Write | Létrehoz egy privát végponti ügyfélkapcsolati proxyt a megadott paraméterekkel, vagy frissíti a megadott magánhálózati végponthoz tartozó tulajdonságokat vagy címkéket. |
> | Műveletek | Microsoft. DBforMariaDB/Servers/privateEndpointConnections/delete | Meglévő magánhálózati végponti kapcsolatok törlése |
> | Műveletek | Microsoft. DBforMariaDB/Servers/privateEndpointConnections/READ | A privát végponti kapcsolatok listáját adja vissza, vagy lekéri a megadott privát végponti kapcsolat tulajdonságait. |
> | Műveletek | Microsoft. DBforMariaDB/Servers/privateEndpointConnections/Write | Meglévő magánhálózati végponti kapcsolatok jóváhagyása vagy elutasítása |
> | Műveletek | Microsoft. DBforMariaDB/Servers/privateLinkResources/READ | A kapcsolódó MariaDB-kiszolgáló magánhálózati kapcsolati erőforrásainak beolvasása |
> | Műveletek | Microsoft. DBforMariaDB/Servers/Providers/Microsoft. bepillantást/diagnosticSettings/READ | Az erőforráshoz tartozó nem független beállítás beolvasása |
> | Műveletek | Microsoft. DBforMariaDB/kiszolgálók/szolgáltatók/Microsoft. bepillantások/diagnosticSettings/írás | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft. DBforMariaDB/Servers/Providers/Microsoft. bepillantást/logDefinitions/READ | A MariaDB-kiszolgálók számára elérhető naplók beolvasása |
> | Műveletek | Microsoft. DBforMariaDB/Servers/Providers/Microsoft. bepillantást/metricDefinitions/READ | Az adatbázisokhoz elérhető metrikák visszatérési típusai |
> | Műveletek | Microsoft. DBforMariaDB/Servers/queryTexts/Action | A lekérdezések listájához tartozó szövegek visszaküldése |
> | Műveletek | Microsoft. DBforMariaDB/Servers/queryTexts/Action | Egy lekérdezés szövegének visszaadása |
> | Műveletek | Microsoft. DBforMariaDB/kiszolgálók/olvasás | A kiszolgálók listájának visszaadása vagy a megadott kiszolgáló tulajdonságainak beolvasása. |
> | Műveletek | Microsoft. DBforMariaDB/Servers/recoverableServers/READ | A helyreállítható MariaDB-kiszolgáló adatainak visszaadása |
> | Műveletek | Microsoft. DBforMariaDB/kiszolgálók/replikák/olvasás | MariaDB-kiszolgáló olvasási replikáinak beolvasása |
> | Műveletek | Microsoft. DBforMariaDB/kiszolgálók/újraindítás/művelet | Egy adott kiszolgáló újraindítása. |
> | Műveletek | Microsoft. DBforMariaDB/Servers/securityAlertPolicies/READ | Egy adott kiszolgálón konfigurált kiszolgálói veszélyforrások észlelési házirendjének részleteinek beolvasása |
> | Műveletek | Microsoft. DBforMariaDB/Servers/securityAlertPolicies/Write | A kiszolgálói veszélyforrások észlelési házirendjének módosítása egy adott kiszolgálóhoz |
> | Műveletek | Microsoft. DBforMariaDB/Servers/topQueryStatistics/READ | A lekérdezési statisztikák listájának visszaadása a leggyakoribb lekérdezésekhez. |
> | Műveletek | Microsoft. DBforMariaDB/Servers/topQueryStatistics/READ | Lekérdezési statisztika visszaadása |
> | Műveletek | Microsoft. DBforMariaDB/Servers/updateConfigurations/Action | A megadott kiszolgálóhoz tartozó konfigurációk frissítése |
> | Műveletek | Microsoft. DBforMariaDB/Servers/virtualNetworkRules/delete | Meglévő Virtual Network-szabály törlése |
> | Műveletek | Microsoft. DBforMariaDB/Servers/virtualNetworkRules/READ | Visszaadja a virtuális hálózati szabályok listáját, vagy lekéri a megadott virtuális hálózati szabály tulajdonságait. |
> | Műveletek | Microsoft. DBforMariaDB/Servers/virtualNetworkRules/Write | Létrehoz egy virtuális hálózati szabályt a megadott paraméterekkel, vagy frissíti a megadott virtuális hálózati szabály tulajdonságait vagy címkéit. |
> | Műveletek | Microsoft. DBforMariaDB/Servers/waitStatistics/READ | Példányra vonatkozó várakozási idő statisztikája |
> | Műveletek | Microsoft. DBforMariaDB/Servers/waitStatistics/READ | Várakozási statisztika visszaküldése |
> | Műveletek | Microsoft. DBforMariaDB/kiszolgálók/írás | Létrehoz egy kiszolgálót a megadott paraméterekkel, vagy frissíti a megadott kiszolgálóhoz tartozó tulajdonságokat vagy címkéket. |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. DBforMySQL/checkNameAvailability/művelet | Győződjön meg arról, hogy az adott kiszolgálónév elérhető-e a világ egy adott előfizetéshez való kiépítés céljából. |
> | Műveletek | Microsoft. DBforMySQL/Locations/azureAsyncOperation/READ | A MySQL-kiszolgáló műveleti eredményeinek visszaadása |
> | Műveletek | Microsoft. DBforMySQL/Locations/operationResults/READ | ResourceGroup-alapú MySQL-kiszolgáló műveleti eredményeinek visszaadása |
> | Műveletek | Microsoft. DBforMySQL/Locations/operationResults/READ | A MySQL-kiszolgáló műveleti eredményeinek visszaadása |
> | Műveletek | Microsoft. DBforMySQL/Locations/performanceTiers/READ | A rendelkezésre álló teljesítményi rétegek listáját adja vissza. |
> | Műveletek | Microsoft. DBforMySQL/Locations/privateEndpointConnectionAzureAsyncOperation/READ | Egy privát végponti kapcsolatok műveletének eredményét kapja meg |
> | Műveletek | Microsoft. DBforMySQL/Locations/privateEndpointConnectionOperationResults/READ | Egy privát végponti kapcsolatok műveletének eredményét kapja meg |
> | Műveletek | Microsoft. DBforMySQL/Locations/privateEndpointConnectionProxyAzureAsyncOperation/READ | A magánhálózati végponti kapcsolatok proxyjának eredményének beolvasása |
> | Műveletek | Microsoft. DBforMySQL/Locations/privateEndpointConnectionProxyOperationResults/READ | A magánhálózati végponti kapcsolatok proxyjának eredményének beolvasása |
> | Műveletek | Microsoft. DBforMySQL/Locations/securityAlertPoliciesAzureAsyncOperation/READ | A kiszolgáló fenyegetés-észlelési műveletének eredményét visszaadó lista. |
> | Műveletek | Microsoft. DBforMySQL/Locations/securityAlertPoliciesOperationResults/READ | A kiszolgáló fenyegetés-észlelési műveletének eredményét visszaadó lista. |
> | Műveletek | Microsoft. DBforMySQL/Locations/serverKeyAzureAsyncOperation/READ | Folyamatban lévő műveletek beolvasása transzparens adattitkosítási kiszolgáló kulcsain |
> | Műveletek | Microsoft. DBforMySQL/Locations/serverKeyOperationResults/READ | Folyamatban lévő műveletek beolvasása transzparens adattitkosítási kiszolgáló kulcsain |
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
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/kulcsok/törlés | Töröl egy meglévő kiszolgálói kulcsot. |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/kulcsok/olvasás | Visszaadja a kiszolgálói kulcsok listáját, vagy lekéri a megadott kiszolgálói kulcs tulajdonságait. |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/kulcsok/írás | Létrehoz egy kulcsot a megadott paraméterekkel, vagy frissíti a megadott kiszolgálói kulcs tulajdonságait vagy címkéit. |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/naplófájlok/olvasás | A MySQL-naplófájlok listájának visszaadása. |
> | Műveletek | Microsoft. DBforMySQL/Servers/privateEndpointConnectionProxies/delete | Meglévő privát végponti kapcsolatkezelő törlése |
> | Műveletek | Microsoft. DBforMySQL/Servers/privateEndpointConnectionProxies/READ | A privát végponti kapcsolatok proxyinak listáját adja vissza, vagy beolvassa a megadott privát végpont-összekötő tulajdonságait. |
> | Műveletek | Microsoft. DBforMySQL/Servers/privateEndpointConnectionProxies/validate/Action | Egy privát végponti kapcsolatok ellenőrzése az NRP oldalról |
> | Műveletek | Microsoft. DBforMySQL/Servers/privateEndpointConnectionProxies/Write | Létrehoz egy privát végponti ügyfélkapcsolati proxyt a megadott paraméterekkel, vagy frissíti a megadott magánhálózati végponthoz tartozó tulajdonságokat vagy címkéket. |
> | Műveletek | Microsoft. DBforMySQL/Servers/privateEndpointConnections/delete | Meglévő magánhálózati végponti kapcsolatok törlése |
> | Műveletek | Microsoft. DBforMySQL/Servers/privateEndpointConnections/READ | A privát végponti kapcsolatok listáját adja vissza, vagy lekéri a megadott privát végponti kapcsolat tulajdonságait. |
> | Műveletek | Microsoft. DBforMySQL/Servers/privateEndpointConnections/Write | Meglévő magánhálózati végponti kapcsolatok jóváhagyása vagy elutasítása |
> | Műveletek | Microsoft. DBforMySQL/Servers/privateLinkResources/READ | A kapcsolódó MySQL-kiszolgáló magánhálózati kapcsolati erőforrásainak beolvasása |
> | Műveletek | Microsoft. DBforMySQL/Servers/Providers/Microsoft. bepillantást/diagnosticSettings/READ | Az erőforráshoz tartozó nem független beállítás beolvasása |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/szolgáltatók/Microsoft. bepillantások/diagnosticSettings/írás | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft. DBforMySQL/Servers/Providers/Microsoft. bepillantást/logDefinitions/READ | A MySQL-kiszolgálókhoz elérhető naplók beolvasása |
> | Műveletek | Microsoft. DBforMySQL/Servers/Providers/Microsoft. bepillantást/metricDefinitions/READ | Az adatbázisokhoz elérhető metrikák visszatérési típusai |
> | Műveletek | Microsoft. DBforMySQL/Servers/queryTexts/Action | A lekérdezések listájához tartozó szövegek visszaküldése |
> | Műveletek | Microsoft. DBforMySQL/Servers/queryTexts/Action | Egy lekérdezés szövegének visszaadása |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/olvasás | A kiszolgálók listájának visszaadása vagy a megadott kiszolgáló tulajdonságainak beolvasása. |
> | Műveletek | Microsoft. DBforMySQL/Servers/recoverableServers/READ | A helyreállítható MySQL-kiszolgáló adatainak visszaadása |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/replikák/olvasás | MySQL-kiszolgáló olvasási replikáinak beolvasása |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/újraindítás/művelet | Egy adott kiszolgáló újraindítása. |
> | Műveletek | Microsoft. DBforMySQL/Servers/securityAlertPolicies/READ | Egy adott kiszolgálón konfigurált kiszolgálói veszélyforrások észlelési házirendjének részleteinek beolvasása |
> | Műveletek | Microsoft. DBforMySQL/Servers/securityAlertPolicies/Write | A kiszolgálói veszélyforrások észlelési házirendjének módosítása egy adott kiszolgálóhoz |
> | Műveletek | Microsoft. DBforMySQL/Servers/topQueryStatistics/READ | A lekérdezési statisztikák listájának visszaadása a leggyakoribb lekérdezésekhez. |
> | Műveletek | Microsoft. DBforMySQL/Servers/topQueryStatistics/READ | Lekérdezési statisztika visszaadása |
> | Műveletek | Microsoft. DBforMySQL/Servers/updateConfigurations/Action | A megadott kiszolgálóhoz tartozó konfigurációk frissítése |
> | Műveletek | Microsoft. DBforMySQL/Servers/virtualNetworkRules/delete | Meglévő Virtual Network-szabály törlése |
> | Műveletek | Microsoft. DBforMySQL/Servers/virtualNetworkRules/READ | Visszaadja a virtuális hálózati szabályok listáját, vagy lekéri a megadott virtuális hálózati szabály tulajdonságait. |
> | Műveletek | Microsoft. DBforMySQL/Servers/virtualNetworkRules/Write | Létrehoz egy virtuális hálózati szabályt a megadott paraméterekkel, vagy frissíti a megadott virtuális hálózati szabály tulajdonságait vagy címkéit. |
> | Műveletek | Microsoft. DBforMySQL/Servers/waitStatistics/READ | Példányra vonatkozó várakozási idő statisztikája |
> | Műveletek | Microsoft. DBforMySQL/Servers/waitStatistics/READ | Várakozási statisztika visszaküldése |
> | Műveletek | Microsoft. DBforMySQL/kiszolgálók/írás | Létrehoz egy kiszolgálót a megadott paraméterekkel, vagy frissíti a megadott kiszolgálóhoz tartozó tulajdonságokat vagy címkéket. |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. DBforPostgreSQL/checkNameAvailability/művelet | Győződjön meg arról, hogy az adott kiszolgálónév elérhető-e a világ egy adott előfizetéshez való kiépítés céljából. |
> | Műveletek | Microsoft. DBforPostgreSQL/Locations/azureAsyncOperation/READ | A PostgreSQL-kiszolgáló műveleti eredményeinek visszaadása |
> | Műveletek | Microsoft. DBforPostgreSQL/Locations/operationResults/READ | ResourceGroup-alapú PostgreSQL-kiszolgáló műveleti eredményeinek visszaadása |
> | Műveletek | Microsoft. DBforPostgreSQL/Locations/operationResults/READ | A PostgreSQL-kiszolgáló műveleti eredményeinek visszaadása |
> | Műveletek | Microsoft. DBforPostgreSQL/Locations/performanceTiers/READ | A rendelkezésre álló teljesítményi rétegek listáját adja vissza. |
> | Műveletek | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionAzureAsyncOperation/READ | Egy privát végponti kapcsolatok műveletének eredményét kapja meg |
> | Műveletek | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionOperationResults/READ | Egy privát végponti kapcsolatok műveletének eredményét kapja meg |
> | Műveletek | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionProxyAzureAsyncOperation/READ | A magánhálózati végponti kapcsolatok proxyjának eredményének beolvasása |
> | Műveletek | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionProxyOperationResults/READ | A magánhálózati végponti kapcsolatok proxyjának eredményének beolvasása |
> | Műveletek | Microsoft. DBforPostgreSQL/Locations/securityAlertPoliciesAzureAsyncOperation/READ | A kiszolgáló fenyegetés-észlelési műveletének eredményét visszaadó lista. |
> | Műveletek | Microsoft. DBforPostgreSQL/Locations/securityAlertPoliciesOperationResults/READ | A kiszolgáló fenyegetés-észlelési műveletének eredményét visszaadó lista. |
> | Műveletek | Microsoft. DBforPostgreSQL/Locations/serverKeyAzureAsyncOperation/READ | Folyamatban lévő műveletek beolvasása transzparens adattitkosítási kiszolgáló kulcsain |
> | Műveletek | Microsoft. DBforPostgreSQL/Locations/serverKeyOperationResults/READ | Folyamatban lévő műveletek beolvasása transzparens adattitkosítási kiszolgáló kulcsain |
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
> | Műveletek | Microsoft. DBforPostgreSQL/kiszolgálók/konfigurációk/írás | A megadott konfiguráció értékének frissítése |
> | Műveletek | Microsoft. DBforPostgreSQL/kiszolgálók/adatbázisok/törlés | Töröl egy meglévő PostgreSQL-adatbázist. |
> | Műveletek | Microsoft. DBforPostgreSQL/kiszolgálók/adatbázisok/olvasás | A PostgreSQL-adatbázisok listájának visszaadása vagy a megadott adatbázis tulajdonságainak beolvasása. |
> | Műveletek | Microsoft. DBforPostgreSQL/kiszolgálók/adatbázisok/írás | Létrehoz egy PostgreSQL-adatbázist a megadott paraméterekkel, vagy frissíti a megadott adatbázis tulajdonságait. |
> | Műveletek | Microsoft. DBforPostgreSQL/kiszolgálók/törlés | Töröl egy meglévő kiszolgálót. |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/firewallRules/delete | Töröl egy meglévő tűzfalszabály-szabályt. |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/firewallRules/READ | Visszaadja egy kiszolgáló tűzfalszabályok listáját, vagy lekéri a megadott tűzfalszabály tulajdonságait. |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/firewallRules/Write | Tűzfalszabály létrehozása a megadott paraméterekkel vagy egy meglévő szabály frissítése. |
> | Műveletek | Microsoft. DBforPostgreSQL/kiszolgálók/kulcsok/törlés | Töröl egy meglévő kiszolgálói kulcsot. |
> | Műveletek | Microsoft. DBforPostgreSQL/kiszolgálók/kulcsok/olvasás | Visszaadja a kiszolgálói kulcsok listáját, vagy lekéri a megadott kiszolgálói kulcs tulajdonságait. |
> | Műveletek | Microsoft. DBforPostgreSQL/kiszolgálók/kulcsok/írás | Létrehoz egy kulcsot a megadott paraméterekkel, vagy frissíti a megadott kiszolgálói kulcs tulajdonságait vagy címkéit. |
> | Műveletek | Microsoft. DBforPostgreSQL/kiszolgálók/naplófájlok/olvasás | A PostgreSQL-naplófájlok listájának visszaadása. |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnectionProxies/delete | Meglévő privát végponti kapcsolatkezelő törlése |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnectionProxies/READ | A privát végponti kapcsolatok proxyinak listáját adja vissza, vagy beolvassa a megadott privát végpont-összekötő tulajdonságait. |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnectionProxies/validate/Action | Egy privát végponti kapcsolatok ellenőrzése az NRP oldalról |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnectionProxies/Write | Létrehoz egy privát végponti ügyfélkapcsolati proxyt a megadott paraméterekkel, vagy frissíti a megadott magánhálózati végponthoz tartozó tulajdonságokat vagy címkéket. |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnections/delete | Meglévő magánhálózati végponti kapcsolatok törlése |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnections/READ | A privát végponti kapcsolatok listáját adja vissza, vagy lekéri a megadott privát végponti kapcsolat tulajdonságait. |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnections/Write | Meglévő magánhálózati végponti kapcsolatok jóváhagyása vagy elutasítása |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/privateLinkResources/READ | A kapcsolódó PostgreSQL-kiszolgáló magánhálózati kapcsolati erőforrásainak beolvasása |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/Providers/Microsoft. bepillantást/diagnosticSettings/READ | Az erőforráshoz tartozó nem független beállítás beolvasása |
> | Műveletek | Microsoft. DBforPostgreSQL/kiszolgálók/szolgáltatók/Microsoft. bepillantások/diagnosticSettings/írás | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/Providers/Microsoft. bepillantást/logDefinitions/READ | A postgres-kiszolgálók számára elérhető naplók beolvasása |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/Providers/Microsoft. bepillantást/metricDefinitions/READ | Az adatbázisokhoz elérhető metrikák visszatérési típusai |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/queryTexts/Action | Egy lekérdezés szövegének visszaadása |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/queryTexts/READ | A lekérdezések listájához tartozó szövegek visszaküldése |
> | Műveletek | Microsoft. DBforPostgreSQL/kiszolgálók/olvasás | A kiszolgálók listájának visszaadása vagy a megadott kiszolgáló tulajdonságainak beolvasása. |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/recoverableServers/READ | A helyreállítható PostgreSQL-kiszolgáló adatainak visszaadása |
> | Műveletek | Microsoft. DBforPostgreSQL/kiszolgálók/replikák/olvasás | PostgreSQL-kiszolgáló olvasási replikáinak beolvasása |
> | Műveletek | Microsoft. DBforPostgreSQL/kiszolgálók/újraindítás/művelet | Egy adott kiszolgáló újraindítása. |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/securityAlertPolicies/READ | Egy adott kiszolgálón konfigurált kiszolgálói veszélyforrások észlelési házirendjének részleteinek beolvasása |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/securityAlertPolicies/Write | A kiszolgálói veszélyforrások észlelési házirendjének módosítása egy adott kiszolgálóhoz |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/topQueryStatistics/READ | A lekérdezési statisztikák listájának visszaadása a leggyakoribb lekérdezésekhez. |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/updateConfigurations/Action | A megadott kiszolgálóhoz tartozó konfigurációk frissítése |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/virtualNetworkRules/delete | Meglévő Virtual Network-szabály törlése |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/virtualNetworkRules/READ | Visszaadja a virtuális hálózati szabályok listáját, vagy lekéri a megadott virtuális hálózati szabály tulajdonságait. |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/virtualNetworkRules/Write | Létrehoz egy virtuális hálózati szabályt a megadott paraméterekkel, vagy frissíti a megadott virtuális hálózati szabály tulajdonságait vagy címkéit. |
> | Műveletek | Microsoft. DBforPostgreSQL/Servers/waitStatistics/READ | Példányra vonatkozó várakozási idő statisztikája |
> | Műveletek | Microsoft. DBforPostgreSQL/kiszolgálók/írás | Létrehoz egy kiszolgálót a megadott paraméterekkel, vagy frissíti a megadott kiszolgálóhoz tartozó tulajdonságokat vagy címkéket. |
> | Műveletek | Microsoft. DBforPostgreSQL/serversv2/konfigurációk/olvasás | Visszaadja egy kiszolgáló konfigurációinak listáját, vagy lekéri a megadott konfiguráció tulajdonságait. |
> | Műveletek | Microsoft. DBforPostgreSQL/serversv2/konfigurációk/írás | A megadott konfiguráció értékének frissítése |
> | Műveletek | Microsoft. DBforPostgreSQL/serversv2/delete | Töröl egy meglévő kiszolgálót. |
> | Műveletek | Microsoft. DBforPostgreSQL/serversv2/firewallRules/delete | Töröl egy meglévő tűzfalszabály-szabályt. |
> | Műveletek | Microsoft. DBforPostgreSQL/serversv2/firewallRules/READ | Visszaadja egy kiszolgáló tűzfalszabályok listáját, vagy lekéri a megadott tűzfalszabály tulajdonságait. |
> | Műveletek | Microsoft. DBforPostgreSQL/serversv2/firewallRules/Write | Tűzfalszabály létrehozása a megadott paraméterekkel vagy egy meglévő szabály frissítése. |
> | Műveletek | Microsoft. DBforPostgreSQL/serversv2/Providers/Microsoft. bepillantást/diagnosticSettings/READ | Az erőforráshoz tartozó nem független beállítás beolvasása |
> | Műveletek | Microsoft. DBforPostgreSQL/serversv2/Providers/Microsoft. bepillantások/diagnosticSettings/írás | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft. DBforPostgreSQL/serversv2/Providers/Microsoft. bepillantást/logDefinitions/READ | A postgres-kiszolgálók számára elérhető naplók beolvasása |
> | Műveletek | Microsoft. DBforPostgreSQL/serversv2/Providers/Microsoft. bepillantást/metricDefinitions/READ | Az adatbázisokhoz elérhető metrikák visszatérési típusai |
> | Műveletek | Microsoft. DBforPostgreSQL/serversv2/READ | A kiszolgálók listájának visszaadása vagy a megadott kiszolgáló tulajdonságainak beolvasása. |
> | Műveletek | Microsoft. DBforPostgreSQL/serversv2/updateConfigurations/művelet | A megadott kiszolgálóhoz tartozó konfigurációk frissítése |
> | Műveletek | Microsoft. DBforPostgreSQL/serversv2/Write | Létrehoz egy kiszolgálót a megadott paraméterekkel, vagy frissíti a megadott kiszolgálóhoz tartozó tulajdonságokat vagy címkéket. |

## <a name="microsoftdevices"></a>Microsoft. Devices

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Devices/Account/diagnosticSettings/READ | Az erőforrás diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft. Devices/Account/diagnosticSettings/Write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft. Devices/Account/logDefinitions/READ | A IotHub szolgáltatás elérhető naplózási definícióinak beolvasása |
> | Műveletek | Microsoft. Devices/Account/metricDefinitions/READ | A IotHub szolgáltatás elérhető metrikáinak beolvasása |
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
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/Certificates/delete | Tanúsítvány törlése |
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/Certificates/generateVerificationCode/Action | Ellenőrző kód előállítása |
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/tanúsítványok/olvasás | A tanúsítvány beolvasása |
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/Certificates/ellenőrzés/művelet | Tanúsítvány erőforrásának ellenőrzése |
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/tanúsítványok/írás | Tanúsítvány létrehozása vagy frissítése |
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/delete | Az IotHub-bérlő erőforrásának törlése |
> | Műveletek | Microsoft. Devices/ElasticPools/IotHubTenants/diagnosticSettings/READ | Az erőforrás diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft. Devices/ElasticPools/IotHubTenants/diagnosticSettings/Write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/delete | EventHub-fogyasztói csoport törlése |
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/READ | EventHub-fogyasztói csoport (ok) beolvasása |
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | EventHub-fogyasztói csoport létrehozása |
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/exportDevices/Action | Eszközök exportálása |
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/getStats/READ | A IotHub bérlői statisztika erőforrásának beolvasása |
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/importDevices/Action | Eszközök importálása |
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys műveletének beolvasása/művelet | A IotHub-bérlő kulcsának beolvasása |
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/Jobs/READ | A megadott IotHub tartozó feladatok (k) beolvasása |
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/Listkeys műveletének beolvasása/Action | Lekéri a IotHub bérlői kulcsait |
> | Műveletek | Microsoft. Devices/ElasticPools/IotHubTenants/logDefinitions/READ | A IotHub szolgáltatás elérhető naplózási definícióinak beolvasása |
> | Műveletek | Microsoft. Devices/ElasticPools/IotHubTenants/metricDefinitions/READ | A IotHub szolgáltatás elérhető metrikáinak beolvasása |
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/quotaMetrics/READ | Kvóta-metrikák beolvasása |
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/READ | Az IotHub-bérlő erőforrásának beolvasása |
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/Routing/Routes/$testall/Action | Üzenet tesztelése az összes meglévő útvonalon |
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/Routing/Routes/$testnew/Action | Üzenet tesztelése egy megadott vizsgálati útvonalon |
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/routingEndpointsHealth/READ | Egy IotHub tartozó összes útválasztási végpont állapotának beolvasása |
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/securitySettings/operationResults/READ | Az aszinkron Put művelet eredményének beolvasása a IOT bérlői hub-SecuritySettings |
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/securitySettings/READ | A Azure Security Center beállításainak beolvasása a IOT bérlői központban |
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/securitySettings/Write | A IOT-bérlői központ Azure Security Center beállításainak frissítése |
> | Műveletek | Microsoft. Devices/elasticPools/iotHubTenants/Write | Az IotHub-bérlői erőforrás létrehozása vagy frissítése |
> | Műveletek | Microsoft. Devices/ElasticPools/metricDefinitions/READ | A IotHub szolgáltatás elérhető metrikáinak beolvasása |
> | Műveletek | Microsoft. Devices/iotHubs/Certificates/delete | Tanúsítvány törlése |
> | Műveletek | Microsoft. Devices/iotHubs/Certificates/generateVerificationCode/Action | Ellenőrző kód előállítása |
> | Műveletek | Microsoft. Devices/iotHubs/tanúsítványok/olvasás | A tanúsítvány beolvasása |
> | Műveletek | Microsoft. Devices/iotHubs/Certificates/ellenőrzés/művelet | Tanúsítvány erőforrásának ellenőrzése |
> | Műveletek | Microsoft. Devices/iotHubs/Certificates/Write | Tanúsítvány létrehozása vagy frissítése |
> | Műveletek | Microsoft. Devices/iotHubs/delete | IotHub-erőforrás törlése |
> | Műveletek | Microsoft. Devices/IotHubs/diagnosticSettings/READ | Az erőforrás diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft. Devices/IotHubs/diagnosticSettings/Write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft. Devices/iotHubs/eventGridFilters/delete | Törli a Event Grid szűrőt |
> | Műveletek | Microsoft. Devices/iotHubs/eventGridFilters/READ | A Event Grid szűrő beolvasása |
> | Műveletek | Microsoft. Devices/iotHubs/eventGridFilters/Write | Új vagy meglévő Event Grid-szűrő létrehozása vagy frissítése |
> | Műveletek | Microsoft. Devices/iotHubs/eventHubEndpoints/consumerGroups/delete | EventHub-fogyasztói csoport törlése |
> | Műveletek | Microsoft. Devices/iotHubs/eventHubEndpoints/consumerGroups/READ | EventHub-fogyasztói csoport (ok) beolvasása |
> | Műveletek | Microsoft. Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | EventHub-fogyasztói csoport létrehozása |
> | Műveletek | Microsoft. Devices/iotHubs/exportDevices/Action | Eszközök exportálása |
> | Műveletek | Microsoft. Devices/iotHubs/importDevices/Action | Eszközök importálása |
> | Műveletek | Microsoft. Devices/iotHubs/iotHubKeys/listkeys műveletének beolvasása/Action | A megadott név IotHub-kulcsának beolvasása |
> | Műveletek | Microsoft. Devices/iotHubs/iotHubStats/READ | IotHub statisztikájának beolvasása |
> | Műveletek | Microsoft. Devices/iotHubs/Jobs/olvasás | A megadott IotHub tartozó feladatok (k) beolvasása |
> | Műveletek | Microsoft. Devices/iotHubs/listkeys műveletének beolvasása/Action | Az összes IotHub kulcs lekérése |
> | Műveletek | Microsoft. Devices/IotHubs/logDefinitions/READ | A IotHub szolgáltatás elérhető naplózási definícióinak beolvasása |
> | Műveletek | Microsoft. Devices/IotHubs/metricDefinitions/READ | A IotHub szolgáltatás elérhető metrikáinak beolvasása |
> | Műveletek | Microsoft. Devices/iotHubs/operationresults/READ | Művelet eredményének beolvasása (elavult API) |
> | Műveletek | Microsoft. Devices/iotHubs/quotaMetrics/READ | Kvóta-metrikák beolvasása |
> | Műveletek | Microsoft. Devices/iotHubs/READ | A IotHub-erőforrás (ok) beolvasása |
> | Műveletek | Microsoft. Devices/iotHubs/Routing/$testall/Action | Üzenet tesztelése az összes meglévő útvonalon |
> | Műveletek | Microsoft. Devices/iotHubs/Routing/$testnew/Action | Üzenet tesztelése egy megadott vizsgálati útvonalon |
> | Műveletek | Microsoft. Devices/iotHubs/routingEndpointsHealth/READ | Egy IotHub tartozó összes útválasztási végpont állapotának beolvasása |
> | Műveletek | Microsoft. Devices/iotHubs/securitySettings/operationResults/READ | Az IOT hub-SecuritySettings aszinkron Put műveletének eredményének beolvasása |
> | Műveletek | Microsoft. Devices/iotHubs/securitySettings/READ | Az IOT hub Azure Security Center beállításainak beolvasása |
> | Műveletek | Microsoft. Devices/iotHubs/securitySettings/Write | A IOT hub Azure Security Center beállításainak frissítése |
> | Műveletek | Microsoft. Devices/iotHubs/SKU/READ | Érvényes IotHub-SKU beolvasása |
> | Műveletek | Microsoft. Devices/iotHubs/Write | IotHub-erőforrás létrehozása vagy frissítése |
> | Műveletek | Microsoft. Devices/Locations/operationresults/READ | Hely alapú művelet eredményének beolvasása |
> | Műveletek | Microsoft. Devices/operationresults/READ | Művelet eredményének beolvasása |
> | Műveletek | Microsoft. Devices/Operations/READ | Az összes ResourceProvider-művelet beolvasása |
> | Műveletek | Microsoft. Devices/provisioningServices/Certificates/delete | Tanúsítvány törlése |
> | Műveletek | Microsoft. Devices/provisioningServices/Certificates/generateVerificationCode/Action | Ellenőrző kód előállítása |
> | Műveletek | Microsoft. Devices/provisioningServices/tanúsítványok/olvasás | A tanúsítvány beolvasása |
> | Műveletek | Microsoft. Devices/provisioningServices/Certificates/ellenőrzés/művelet | Tanúsítvány erőforrásának ellenőrzése |
> | Műveletek | Microsoft. Devices/provisioningServices/Certificates/Write | Tanúsítvány létrehozása vagy frissítése |
> | Műveletek | Microsoft. Devices/provisioningServices/delete | IotDps-erőforrás törlése |
> | Műveletek | Microsoft. Devices/provisioningServices/diagnosticSettings/READ | Az erőforrás diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft. Devices/provisioningServices/diagnosticSettings/Write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft. Devices/provisioningServices/Keys/listkeys műveletének beolvasása/művelet | Kulcsnév IotDps kulcsainak beolvasása |
> | Műveletek | Microsoft. Devices/provisioningServices/listkeys műveletének beolvasása/Action | Az összes IotDps kulcs lekérése |
> | Műveletek | Microsoft. Devices/provisioningServices/logDefinitions/READ | A kiépítési szolgáltatáshoz elérhető naplózási definíciók beolvasása |
> | Műveletek | Microsoft. Devices/provisioningServices/metricDefinitions/READ | A kiépítési szolgáltatáshoz elérhető metrikák beolvasása |
> | Műveletek | Microsoft. Devices/provisioningServices/operationresults/READ | DPS-művelet eredményének beolvasása |
> | Műveletek | Microsoft. Devices/provisioningServices/READ | IotDps-erőforrás beolvasása |
> | Műveletek | Microsoft. Devices/provisioningServices/SKU/READ | Érvényes IotDps-SKU beolvasása |
> | Műveletek | Microsoft. Devices/provisioningServices/Write | IotDps-erőforrás létrehozása |
> | Műveletek | Microsoft. Devices/regisztráció/művelet | Regisztrálja az előfizetést a IotHub erőforrás-szolgáltatóhoz, és lehetővé teszi a IotHub-erőforrások létrehozását |
> | Műveletek | Microsoft. Devices/használati adatok/olvasás | A szolgáltató előfizetés-használati adatainak beolvasása. |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. DevSpaces/vezérlők/törlés | Az Azure dev Spaces Controller és a adatsík Services törlése |
> | Műveletek | Microsoft. DevSpaces/Controllers/listConnectionDetails/Action | Az Azure dev Spaces vezérlő infrastruktúrájának kapcsolati adatainak listázása |
> | Műveletek | Microsoft. DevSpaces/vezérlők/olvasás | Az Azure dev Spaces-vezérlő tulajdonságainak olvasása |
> | Műveletek | Microsoft. DevSpaces/vezérlők/írás | Azure dev Spaces-vezérlő tulajdonságainak létrehozása vagy frissítése |
> | Műveletek | Microsoft. DevSpaces/Locations/checkContainerHostMapping/Action | Tároló-gazdagép meglévő vezérlő-leképezésének megkeresése |
> | Műveletek | Microsoft. DevSpaces/Locations/operationresults/READ | Aszinkron művelet állapotának olvasása |
> | Műveletek | Microsoft. DevSpaces/regisztráció/művelet | A Microsoft dev Spaces erőforrás-szolgáltató regisztrálása előfizetéssel |

## <a name="microsoftdevtestlab"></a>Microsoft. segédösszetevője

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. segédösszetevője/labCenters/delete | Tesztkörnyezet-központok törlése. |
> | Műveletek | Microsoft. segédösszetevője/labCenters/READ | Laboratóriumi központok olvasása. |
> | Műveletek | Microsoft. segédösszetevője/labCenters/Write | Lab-központok hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/artifactSources/armTemplates/READ | Azure Resource Manager-sablonok beolvasása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/artifactSources/összetevők/GenerateArmTemplate/művelet | Létrehoz egy Azure Resource Manager sablont az adott összetevőhöz, feltölti a szükséges fájlokat egy Storage-fiókba, és érvényesíti a generált összetevőt. |
> | Műveletek | Microsoft. segédösszetevője/Labs/artifactSources/összetevők/olvasás | Összetevők olvasása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/artifactSources/delete | Az összetevő-források törlése. |
> | Műveletek | Microsoft. segédösszetevője/Labs/artifactSources/READ | Az összetevő-források olvasása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/artifactSources/Write | Összetevő-források hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/ClaimAnyVm/művelet | Véletlenszerű, igényelhető virtuális gép igénylése a laborban. |
> | Műveletek | Microsoft. segédösszetevője/Labs/költségek/olvasás | Olvasási költségek. |
> | Műveletek | Microsoft. segédösszetevője/Labs/költségek/írás | Költségek hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/CreateEnvironment/művelet | Virtuális gépek létrehozása tesztkörnyezetben. |
> | Műveletek | Microsoft. segédösszetevője/Labs/customImages/delete | Egyéni lemezképek törlése. |
> | Műveletek | Microsoft. segédösszetevője/Labs/customImages/READ | Egyéni lemezképek olvasása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/customImages/Write | Egyéni lemezképek hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/delete | Labs törlése. |
> | Műveletek | Microsoft. segédösszetevője/Labs/EnsureCurrentUserProfile/művelet | Ellenőrizze, hogy az aktuális felhasználó rendelkezik-e érvényes profillal a laborban. |
> | Műveletek | Microsoft. segédösszetevője/Labs/ExportResourceUsage/művelet | A tesztkörnyezet erőforrás-használatának exportálása egy Storage-fiókba |
> | Műveletek | Microsoft. segédösszetevője/Labs/képletek/törlés | Képletek törlése. |
> | Műveletek | Microsoft. segédösszetevője/Labs/képletek/olvasás | Képletek olvasása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/képletek/írás | Képletek hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/galleryImages/READ | Katalógus képeinek olvasása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/GenerateUploadUri/művelet | URI létrehozása egyéni lemezképek egy laborba való feltöltéséhez. |
> | Műveletek | Microsoft. segédösszetevője/Labs/ImportVirtualMachine/művelet | Virtuális gép importálása egy másik laborba. |
> | Műveletek | Microsoft. segédösszetevője/Labs/ListVhds/művelet | Az egyéni lemezképek létrehozásához elérhető lemezképek listázása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/notificationChannels/delete | Értesítési csatornák törlése. |
> | Műveletek | Microsoft. segédösszetevője/Labs/notificationChannels/értesítés/művelet | Értesítés küldése a megadott csatornának. |
> | Műveletek | Microsoft. segédösszetevője/Labs/notificationChannels/READ | Értesítési csatornák olvasása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/notificationChannels/Write | Értesítési csatornák hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/policySets/EvaluatePolicies/művelet | Kiértékeli a tesztkörnyezet házirendjét. |
> | Műveletek | Microsoft. segédösszetevője/Labs/policySets/házirendek/törlés | Szabályzatok törlése. |
> | Műveletek | Microsoft. segédösszetevője/Labs/policySets/házirendek/olvasás | Szabályzatok beolvasása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/policySets/házirendek/írás | Házirendek hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/policySets/READ | Házirend-készletek olvasása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/olvasás | A laborok beolvasása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/ütemtervek/törlés | Ütemtervek törlése. |
> | Műveletek | Microsoft. segédösszetevője/Labs/ütemtervek/végrehajtás/művelet | Ütemezett végrehajtás. |
> | Műveletek | Microsoft. segédösszetevője/Labs/menetrend/ListApplicable/művelet | Az összes vonatkozó ütemterv listája |
> | Műveletek | Microsoft. segédösszetevője/Labs/ütemtervek/olvasás | Ütemtervek olvasása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/ütemterv/írás | Ütemtervek hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/serviceRunners/delete | Szolgáltatásbeli futók törlése. |
> | Műveletek | Microsoft. segédösszetevője/Labs/serviceRunners/READ | Olvasási szolgáltatás futói |
> | Műveletek | Microsoft. segédösszetevője/Labs/serviceRunners/Write | Szolgáltatásbeli futók hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/sharedGalleries/delete | Megosztott galériákat törölhet. |
> | Műveletek | Microsoft. segédösszetevője/Labs/sharedGalleries/READ | Megosztott galériák olvasása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/sharedGalleries/sharedImages/delete | Megosztott lemezképek törlése. |
> | Műveletek | Microsoft. segédösszetevője/Labs/sharedGalleries/sharedImages/READ | Megosztott lemezképek olvasása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/sharedGalleries/sharedImages/Write | Megosztott lemezképek hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/sharedGalleries/Write | Megosztott galériákat adhat hozzá vagy módosíthat. |
> | Műveletek | Microsoft. segédösszetevője/Labs/felhasználók/törlés | Felhasználói profilok törlése. |
> | Műveletek | Microsoft. segédösszetevője/Labs/felhasználók/lemezek/csatolás/művelet | Csatlakoztassa és hozza létre a lemez bérletét a virtuális géphez. |
> | Műveletek | Microsoft. segédösszetevője/Labs/felhasználók/lemezek/törlés | Lemezek törlése. |
> | Műveletek | Microsoft. segédösszetevője/Labs/felhasználók/lemezek/leválasztás/művelet | Válassza le és szüntesse meg a virtuális géphez csatolt lemez bérletét. |
> | Műveletek | Microsoft. segédösszetevője/Labs/felhasználók/lemezek/olvasás | Lemezek olvasása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/felhasználók/lemezek/írás | Lemezek hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/felhasználók/környezetek/törlés | Környezetek törlése. |
> | Műveletek | Microsoft. segédösszetevője/Labs/felhasználók/környezetek/olvasás | Környezetek olvasása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/felhasználók/környezet/írás | Környezetek hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/felhasználók/olvasás | Felhasználói profilok olvasása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/Users/Secrets/delete | Törölje a titkos kulcsokat. |
> | Műveletek | Microsoft. segédösszetevője/Labs/Users/Secrets/READ | A titkok beolvasása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/Users/Secrets/Write | Titkos kódok hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/Users/serviceFabrics/delete | Törölje a Service Fabrics szolgáltatást. |
> | Műveletek | Microsoft. segédösszetevője/Labs/Users/serviceFabrics/ListApplicableSchedules/Action | Felsorolja a megfelelő indítási/leállítási ütemterveket, ha vannak ilyenek. |
> | Műveletek | Microsoft. segédösszetevője/Labs/Users/serviceFabrics/READ | A Service Fabric beolvasása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/felhasználók/serviceFabrics/ütemtervek/törlés | Ütemtervek törlése. |
> | Műveletek | Microsoft. segédösszetevője/Labs/felhasználók/serviceFabrics/ütemtervek/végrehajtás/művelet | Ütemezett végrehajtás. |
> | Műveletek | Microsoft. segédösszetevője/Labs/felhasználók/serviceFabrics/ütemezett/beolvasás | Ütemtervek olvasása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/felhasználók/serviceFabrics/ütemterv/írás | Ütemtervek hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/Users/serviceFabrics/Start/Action | Indítsa el a Service fabricet. |
> | Műveletek | Microsoft. segédösszetevője/Labs/Users/serviceFabrics/stop/Action | Service Fabric leállítása |
> | Műveletek | Microsoft. segédösszetevője/Labs/Users/serviceFabrics/Write | Service fabricek hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/felhasználók/írás | Felhasználói profilok hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/virtualMachines/AddDataDisk/művelet | Új vagy meglévő adatlemez csatolása a virtuális géphez. |
> | Műveletek | Microsoft. segédösszetevője/Labs/virtualMachines/ApplyArtifacts/művelet | Összetevők alkalmazása a virtuális gépre. |
> | Műveletek | Microsoft. segédösszetevője/Labs/virtualMachines/jogcím/művelet | Meglévő virtuális gép tulajdonjogának átvétele |
> | Műveletek | Microsoft. segédösszetevője/Labs/virtualMachines/ClearArtifactResults/művelet | Törli a virtuális gép tárgyi eredményeit. |
> | Műveletek | Microsoft. segédösszetevője/Labs/virtualMachines/delete | Virtuális gépek törlése. |
> | Műveletek | Microsoft. segédösszetevője/Labs/virtualMachines/DetachDataDisk/művelet | Válassza le a megadott lemezt a virtuális gépről. |
> | Műveletek | Microsoft. segédösszetevője/Labs/virtualMachines/GetRdpFileContents/művelet | Egy olyan karakterlánc beolvasása, amely a virtuális gép RDP-fájljának tartalmát jelöli |
> | Műveletek | Microsoft. segédösszetevője/Labs/virtualMachines/ListApplicableSchedules/művelet | Felsorolja a megfelelő indítási/leállítási ütemterveket, ha vannak ilyenek. |
> | Műveletek | Microsoft. segédösszetevője/Labs/virtualMachines/READ | Virtuális gépek olvasása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/virtualMachines/újratelepítés/művelet | Virtuális gép újbóli üzembe helyezése |
> | Műveletek | Microsoft. segédösszetevője/Labs/virtualMachines/átméretezés/művelet | Virtuális gép átméretezése. |
> | Műveletek | Microsoft. segédösszetevője/Labs/virtualMachines/újraindítás/művelet | Indítsa újra a virtuális gépet. |
> | Műveletek | Microsoft. segédösszetevője/Labs/virtualMachines/Schedules/delete | Ütemtervek törlése. |
> | Műveletek | Microsoft. segédösszetevője/Labs/virtualMachines/menetrendek/végrehajtás/művelet | Ütemezett végrehajtás. |
> | Műveletek | Microsoft. segédösszetevője/Labs/virtualMachines/ütemterv/olvasás | Ütemtervek olvasása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/virtualMachines/ütemterv/írás | Ütemtervek hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/virtualMachines/Start/művelet | Indítsa el a virtuális gépet. |
> | Műveletek | Microsoft. segédösszetevője/Labs/virtualMachines/leállítás/művelet | Virtuális gép leállítása |
> | Műveletek | Microsoft. segédösszetevője/Labs/virtualMachines/TransferDisks/művelet | Átviszi a virtuális géphez csatolt összes adatlemezt az aktuális felhasználó tulajdonában. |
> | Műveletek | Microsoft. segédösszetevője/Labs/virtualMachines/unjogcím/művelet | Meglévő virtuális gép tulajdonjogának felszabadítása |
> | Műveletek | Microsoft. segédösszetevője/Labs/virtualMachines/Write | Virtuális gépek hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/virtualNetworks/bastionHosts/delete | Bastionhosts törlése. |
> | Műveletek | Microsoft. segédösszetevője/Labs/virtualNetworks/bastionHosts/READ | Bastionhosts olvasása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/virtualNetworks/bastionHosts/Write | Bastionhosts hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/virtualNetworks/delete | Virtuális hálózatok törlése. |
> | Műveletek | Microsoft. segédösszetevője/Labs/virtualNetworks/READ | Virtuális hálózatok olvasása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/virtualNetworks/Write | Virtuális hálózatok hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/vmPools/delete | Virtuálisgép-készletek törlése. |
> | Műveletek | Microsoft. segédösszetevője/Labs/vmPools/READ | Virtuális gépek készletének olvasása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/vmPools/Write | Virtuálisgép-készletek hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. segédösszetevője/Labs/írás | Labs hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. segédösszetevője/Locations/Operations/READ | Olvasási műveletek. |
> | Műveletek | Microsoft. segédösszetevője/regisztráció/művelet | Regisztrálja az előfizetést |
> | Műveletek | Microsoft. segédösszetevője/ütemtervek/törlés | Ütemtervek törlése. |
> | Műveletek | Microsoft. segédösszetevője/ütemtervek/végrehajtás/művelet | Ütemezett végrehajtás. |
> | Műveletek | Microsoft. segédösszetevője/ütemezett/beolvasás | Ütemtervek olvasása. |
> | Műveletek | Microsoft. segédösszetevője/ütemtervek/újracélzás/művelet | Frissíti az ütemterv cél erőforrás-azonosítóját. |
> | Műveletek | Microsoft. segédösszetevője/ütemterv/írás | Ütemtervek hozzáadása vagy módosítása. |

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
> | Műveletek | Microsoft. EventHub/checkNameAvailability/művelet | Ellenőrzi a névtér elérhetőségét a megadott előfizetésben. |
> | Műveletek | Microsoft. EventHub/checkNamespaceAvailability/művelet | Ellenőrzi a névtér elérhetőségét a megadott előfizetésben. Ez az API elavult. használja helyette a CheckNameAvailability. |
> | Műveletek | Microsoft. EventHub/fürtök/törlés | Töröl egy meglévő fürterőforrás-erőforrást. |
> | Műveletek | Microsoft. EventHub/fürtök/névterek/olvasás | Névtér Azure Resource Manager azonosítóinak listázása a fürtön belüli névterekhez. |
> | Műveletek | Microsoft. EventHub/fürtök/operationresults/READ | Aszinkron fürt művelet állapotának beolvasása. |
> | Műveletek | Microsoft. EventHub/fürtök/szolgáltatók/Microsoft. bepillantást/metricDefinitions/READ | A fürt metrikáinak erőforrásaihoz tartozó leírások listájának beolvasása |
> | Műveletek | Microsoft. EventHub/fürtök/olvasás | A fürterőforrás leírásának beolvasása |
> | Műveletek | Microsoft. EventHub/fürtök/írás | Létrehoz vagy módosít egy meglévő fürterőforrás-erőforrást. |
> | Műveletek | Microsoft. EventHub/Locations/deleteVirtualNetworkOrSubnets/Action | A megadott VNet tartozó EventHub erőforrás-szolgáltató VNet-szabályainak törlése |
> | Műveletek | Microsoft. EventHub/névterek/engedélyezési szabályok/művelet | Frissíti a névtér-engedélyezési szabályt. Ez az API elavult. Ehelyett használjon egy PUT hívást a névtér-engedélyezési szabály frissítéséhez. Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. |
> | Műveletek | Microsoft. EventHub/névterek/engedélyezési szabályok/delete | Névtér-engedélyezési szabály törlése. Az alapértelmezett névtér-engedélyezési szabály nem törölhető.  |
> | Műveletek | Microsoft. EventHub/névterek/engedélyezési szabályok/listkeys műveletének beolvasása/művelet | A névtérhez tartozó kapcsolódási karakterlánc beolvasása |
> | Műveletek | Microsoft. EventHub/névterek/engedélyezési szabályok/olvasás | A névterek engedélyezési szabályai leírásának beolvasása. |
> | Műveletek | Microsoft. EventHub/névterek/engedélyezési szabályok/regenerateKeys/művelet | Az elsődleges vagy másodlagos kulcs újragenerálása az erőforráshoz |
> | Műveletek | Microsoft. EventHub/névterek/engedélyezési szabályok/írás | Hozzon létre egy névtér szintű engedélyezési szabályokat, és frissítse a tulajdonságait. Az engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és a másodlagos kulcsokat is frissítheti. |
> | Műveletek | Microsoft. EventHub/névterek/törlés | Névtér erőforrásának törlése |
> | Műveletek | Microsoft. EventHub/névterek/disasterRecoveryConfigs/engedélyezési szabályok/listkeys műveletének beolvasása/művelet | A vész-helyreállítási elsődleges névtérhez tartozó engedélyezési szabályok kulcsainak beolvasása |
> | Műveletek | Microsoft. EventHub/névterek/disasterRecoveryConfigs/engedélyezési szabályok/READ | Vész-helyreállítási elsődleges névtér engedélyezési szabályainak beolvasása |
> | Műveletek | Microsoft. EventHub/névterek/disasterRecoveryConfigs/breakPairing/művelet | Letiltja a vész-helyreállítást, és leállítja a módosítások elsődlegesről másodlagos névtérbe történő replikálását. |
> | Műveletek | Microsoft. EventHub/névterek/disasterrecoveryconfigs/checkNameAvailability/művelet | Ellenőrzi a névtér-alias elérhetőségét a megadott előfizetés alatt. |
> | Műveletek | Microsoft. EventHub/névterek/disasterRecoveryConfigs/delete | A névtérhez társított vész-helyreállítási konfiguráció törlése. Ez a művelet csak az elsődleges névtér használatával hívható meg. |
> | Műveletek | Microsoft. EventHub/névterek/disasterRecoveryConfigs/feladatátvétel/művelet | Egy GEO DR feladatátvételt hív meg, és újrakonfigurálja a névtér aliasát, hogy a másodlagos névtérre mutasson. |
> | Műveletek | Microsoft. EventHub/névterek/disasterRecoveryConfigs/olvasás | A névtérhez társított vész-helyreállítási konfiguráció beolvasása. |
> | Műveletek | Microsoft. EventHub/névterek/disasterRecoveryConfigs/írás | A névtérhez társított vész-helyreállítási konfiguráció létrehozása vagy frissítése. |
> | Műveletek | Microsoft. EventHub/névterek/eventhubs/engedélyezési szabályok/művelet | A EventHub frissítésének művelete. Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. Engedélyezési szabályok. Használjon egy PUT hívást az engedélyezési szabály frissítéséhez. |
> | Műveletek | Microsoft. EventHub/névterek/eventhubs/engedélyezési szabályok/delete | EventHub-engedélyezési szabályok törlésének művelete |
> | Műveletek | Microsoft. EventHub/névterek/eventhubs/engedélyezési szabályok/listkeys műveletének beolvasása/művelet | A EventHub tartozó kapcsolódási karakterlánc beolvasása |
> | Műveletek | Microsoft. EventHub/névterek/eventhubs/engedélyezési szabályok/READ |  A EventHub-engedélyezési szabályok listájának beolvasása |
> | Műveletek | Microsoft. EventHub/névterek/eventhubs/engedélyezési szabályok/regenerateKeys/művelet | Az elsődleges vagy másodlagos kulcs újragenerálása az erőforráshoz |
> | Műveletek | Microsoft. EventHub/névterek/eventhubs/engedélyezési szabályok/Write | Hozzon létre EventHub engedélyezési szabályokat, és frissítse annak tulajdonságait. Az engedélyezési szabályokhoz tartozó hozzáférési jogosultságok frissíthetők. |
> | Műveletek | Microsoft. EventHub/névterek/eventHubs/consumergroups/delete | ConsumerGroup-erőforrás törlésére szolgáló művelet |
> | Műveletek | Microsoft. EventHub/névterek/eventHubs/consumergroups/READ | ConsumerGroup-erőforrás leírásának beolvasása |
> | Műveletek | Microsoft. EventHub/névterek/eventHubs/consumergroups/Write | ConsumerGroup tulajdonságainak létrehozása vagy frissítése. |
> | Műveletek | Microsoft. EventHub/névterek/eventhubs/delete | EventHub-erőforrás törlésére szolgáló művelet |
> | Műveletek | Microsoft. EventHub/névterek/eventhubs/olvasás | EventHub-erőforrás leírásának beolvasása |
> | Műveletek | Microsoft. EventHub/névterek/eventhubs/írás | EventHub tulajdonságainak létrehozása vagy frissítése. |
> | Műveletek | Microsoft. EventHub/névterek/ipFilterRules/delete | IP-szűrő erőforrásának törlése |
> | Műveletek | Microsoft. EventHub/névterek/ipFilterRules/olvasás | IP-szűrő erőforrásának beolvasása |
> | Műveletek | Microsoft. EventHub/névterek/ipFilterRules/írás | IP-szűrő erőforrásának létrehozása |
> | DataAction | Microsoft. EventHub/névterek/üzenetek/fogadás/művelet | Üzenetek fogadása |
> | DataAction | Microsoft. EventHub/névterek/üzenetek/küldés/művelet | Üzenetek küldése |
> | Műveletek | Microsoft. EventHub/névterek/messagingPlan/olvasás | Lekéri egy névtér üzenetkezelési tervét.<br>Ez az API elavult.<br>A MessagingPlan-erőforráson keresztül elérhető tulajdonságok a (szülő) névtér-erőforrásba kerülnek a későbbi API-verziókban.<br>Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. |
> | Műveletek | Microsoft. EventHub/névterek/messagingPlan/írás | Frissíti egy névtér üzenetkezelési tervét.<br>Ez az API elavult.<br>A MessagingPlan-erőforráson keresztül elérhető tulajdonságok a (szülő) névtér-erőforrásba kerülnek a későbbi API-verziókban.<br>Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. |
> | Műveletek | Microsoft. EventHub/névterek/networkruleset/delete | VNET-szabály erőforrásának törlése |
> | Műveletek | Microsoft. EventHub/névterek/networkruleset/olvasás | NetworkRuleSet-erőforrás beolvasása |
> | Műveletek | Microsoft. EventHub/névterek/networkruleset/írás | VNET-szabály erőforrásának létrehozása |
> | Műveletek | Microsoft. EventHub/névterek/networkrulesets/delete | VNET-szabály erőforrásának törlése |
> | Műveletek | Microsoft. EventHub/névterek/networkrulesets/olvasás | NetworkRuleSet-erőforrás beolvasása |
> | Műveletek | Microsoft. EventHub/névterek/networkrulesets/írás | VNET-szabály erőforrásának létrehozása |
> | Műveletek | Microsoft. EventHub/névterek/operationresults/olvasás | Névtér-művelet állapotának beolvasása |
> | Műveletek | Microsoft. EventHub/névterek/szolgáltatók/Microsoft. bepillantást/diagnosticSettings/READ | Névtér diagnosztikai beállításainak erőforrás-leírásának beolvasása |
> | Műveletek | Microsoft. EventHub/névterek/szolgáltatók/Microsoft. bepillantások/diagnosticSettings/írás | Névtér diagnosztikai beállításainak erőforrás-leírásának beolvasása |
> | Műveletek | Microsoft. EventHub/névterek/szolgáltatók/Microsoft. bepillantást/logDefinitions/READ | Névtér-naplók erőforrás-leírások listájának beolvasása |
> | Műveletek | Microsoft. EventHub/névterek/szolgáltatók/Microsoft. bepillantást/metricDefinitions/READ | Névtér-metrikák erőforrás-leírásának beolvasása |
> | Műveletek | Microsoft. EventHub/névterek/olvasás | A névtér erőforrás-leírásának beolvasása |
> | Műveletek | Microsoft. EventHub/névterek/removeAcsNamepsace/művelet | ACS-névtér eltávolítása |
> | Műveletek | Microsoft. EventHub/névterek/virtualNetworkRules/delete | VNET-szabály erőforrásának törlése |
> | Műveletek | Microsoft. EventHub/névterek/virtualNetworkRules/olvasás | VNET-szabály erőforrásának beolvasása |
> | Műveletek | Microsoft. EventHub/névterek/virtualNetworkRules/írás | VNET-szabály erőforrásának létrehozása |
> | Műveletek | Microsoft. EventHub/névterek/írás | Hozzon létre egy névtér-erőforrást, és frissítse a tulajdonságait. A névtér címkéi és kapacitása a frissíthető tulajdonságok. |
> | Műveletek | Microsoft. EventHub/Operations/READ | Műveletek beolvasása |
> | Műveletek | Microsoft. EventHub/regisztráció/művelet | Regisztrálja az előfizetést a EventHub erőforrás-szolgáltatóhoz, és lehetővé teszi a EventHub-erőforrások létrehozását |
> | Műveletek | Microsoft. EventHub/SKU/READ | SKU-erőforrások leírásának beolvasása |
> | Műveletek | Microsoft. EventHub/SKU/régiók/olvasás | SkuRegions-erőforrás leírásának beolvasása |
> | Műveletek | Microsoft. EventHub/regisztráció/művelet | A EventHub erőforrás-szolgáltató regisztrálása |

## <a name="microsoftfeatures"></a>Microsoft. features

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. features/features/READ | Egy előfizetés funkcióinak beolvasása. |
> | Műveletek | Microsoft. features/Operations/READ | A műveletek listájának beolvasása. |
> | Műveletek | Microsoft. features/Providers/features/READ | Egy adott erőforrás-szolgáltatóhoz tartozó előfizetés funkciójának beolvasása. |
> | Műveletek | Microsoft. features/Providers/funkciók/regisztráció/művelet | Regisztrálja az előfizetéshez tartozó szolgáltatást egy adott erőforrás-szolgáltatón. |
> | Műveletek | Microsoft. features/Providers/features/Regisztráció törlése/művelet | Egy adott erőforrás-szolgáltató előfizetéséhez tartozó szolgáltatás regisztrációjának törlése. |
> | Műveletek | Microsoft. features/regisztráció/művelet | Egy előfizetés funkciójának regisztrálása. |

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. GuestConfiguration/guestConfigurationAssignments/delete | Vendég-konfiguráció hozzárendelésének törlése. |
> | Műveletek | Microsoft. GuestConfiguration/guestConfigurationAssignments/READ | Vendég-konfiguráció hozzárendelésének beolvasása. |
> | Műveletek | Microsoft. GuestConfiguration/guestConfigurationAssignments/jelentések/olvasás | A vendég-konfiguráció hozzárendelési jelentésének beolvasása. |
> | Műveletek | Microsoft. GuestConfiguration/guestConfigurationAssignments/Write | Új vendég-konfiguráció hozzárendelésének létrehozása. |
> | Műveletek | Microsoft. GuestConfiguration/Operations/READ | A Microsoft. GuestConfiguration erőforrás-szolgáltató műveleteinek beolvasása |
> | Műveletek | Microsoft. GuestConfiguration/regisztráció/művelet | Regisztrálja az előfizetést a Microsoft. GuestConfiguration erőforrás-szolgáltatóhoz. |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. HDInsight/fürtök/alkalmazások/törlés | HDInsight-fürt alkalmazásának törlése |
> | Műveletek | Microsoft. HDInsight/fürtök/alkalmazások/olvasás | HDInsight-fürt alkalmazásának beolvasása |
> | Műveletek | Microsoft. HDInsight/fürtök/alkalmazások/írás | Alkalmazás létrehozása vagy frissítése a HDInsight-fürthöz |
> | Műveletek | Microsoft. HDInsight/fürtök/changerdpsetting/művelet | HDInsight-fürt RDP-beállításainak módosítása |
> | Műveletek | Microsoft. HDInsight/fürtök/konfigurációk/művelet | HDInsight-fürt konfigurációjának frissítése |
> | Műveletek | Microsoft. HDInsight/fürtök/konfigurációk/művelet | HDInsight-fürt konfigurációjának beolvasása |
> | Műveletek | Microsoft. HDInsight/fürtök/konfigurációk/olvasás | HDInsight-fürt konfigurációjának beolvasása |
> | Műveletek | Microsoft. HDInsight/fürtök/törlés | HDInsight-fürt törlése |
> | Műveletek | Microsoft. HDInsight/fürtök/bővítmények/törlés | HDInsight-fürthöz tartozó fürt-bővítmény törlése |
> | Műveletek | Microsoft. HDInsight/fürtök/bővítmények/olvasás | Fürt kiterjesztésének beolvasása a HDInsight-fürthöz |
> | Műveletek | Microsoft. HDInsight/fürtök/bővítmények/írás | HDInsight-fürthöz tartozó fürt-bővítmény létrehozása |
> | Műveletek | Microsoft. HDInsight/fürtök/getGatewaySettings/művelet | Átjáró beállításainak beolvasása a HDInsight-fürthöz |
> | Műveletek | Microsoft. HDInsight/fürtök/szolgáltatók/Microsoft. bepillantást/diagnosticSettings/READ | Az erőforrás-HDInsight fürt diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft. HDInsight/fürtök/szolgáltatók/Microsoft. bepillantások/diagnosticSettings/írás | Az erőforrás-HDInsight fürt diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft. HDInsight/fürtök/szolgáltatók/Microsoft. bepillantást/metricDefinitions/READ | A HDInsight-fürthöz elérhető metrikák beolvasása |
> | Műveletek | Microsoft. HDInsight/fürtök/olvasás | A HDInsight-fürt részleteinek beolvasása |
> | Műveletek | Microsoft. HDInsight/fürtök/szerepkörök/átméretezés/művelet | HDInsight-fürt átméretezése |
> | Műveletek | Microsoft. HDInsight/fürtök/updateGatewaySettings/művelet | HDInsight-fürthöz tartozó átjáró beállításainak frissítése |
> | Műveletek | Microsoft. HDInsight/fürtök/írás | HDInsight-fürt létrehozása vagy frissítése |
> | Műveletek | Microsoft. HDInsight/Locations/képességek/olvasás | Előfizetési lehetőségek beszerzése |
> | Műveletek | Microsoft. HDInsight/Locations/checkNameAvailability/READ | Név rendelkezésre állásának keresése |
> | Műveletek | Microsoft. HDInsight/regisztráció/művelet | Az előfizetéshez tartozó HDInsight erőforrás-szolgáltató regisztrálása |
> | Műveletek | Microsoft. HDInsight/regisztráció/művelet | HDInsight erőforrás-szolgáltató regisztrációjának törlése az előfizetéshez |

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. ImportExport/feladatok/törlés | Töröl egy meglévő feladatot. |
> | Műveletek | Microsoft. ImportExport/Jobs/listBitLockerKeys/művelet | A megadott feladatokhoz tartozó BitLocker-kulcsok beolvasása. |
> | Műveletek | Microsoft. ImportExport/feladatok/olvasás | Lekéri a megadott feladat tulajdonságait, vagy visszaadja a feladatok listáját. |
> | Műveletek | Microsoft. ImportExport/feladatok/írás | Létrehoz egy feladatot a megadott paraméterekkel, vagy frissíti a megadott feladatokhoz tartozó tulajdonságokat vagy címkéket. |
> | Műveletek | Microsoft. ImportExport/Locations/READ | Lekéri a megadott hely tulajdonságait, vagy visszaadja a helyek listáját. |
> | Műveletek | Microsoft. ImportExport/Operations/READ | Lekéri az erőforrás-szolgáltató által támogatott műveleteket. |
> | Műveletek | Microsoft. ImportExport/regisztráció/művelet | Regisztrálja az előfizetést az importálási/exportálási erőforrás-szolgáltató számára, és lehetővé teszi az importálási/exportálási feladatok létrehozását. |

## <a name="microsoftinsights"></a>Microsoft. bepillantások

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. bepillantást/ActionGroups/delete | Műveleti csoport törlése |
> | Műveletek | Microsoft. bepillantások/ActionGroups/olvasás | Műveleti csoport beolvasása |
> | Műveletek | Microsoft. bepillantások/ActionGroups/írás | Műveleti csoport létrehozása vagy frissítése |
> | Műveletek | Microsoft. ininsights/ActivityLogAlerts/aktivált/művelet | Tevékenység naplójának riasztása aktiválva |
> | Műveletek | Microsoft. bepillantást/ActivityLogAlerts/delete | Műveletnapló riasztásának törlése |
> | Műveletek | Microsoft. bepillantások/ActivityLogAlerts/olvasás | Műveletnapló riasztásának beolvasása |
> | Műveletek | Microsoft. bepillantások/ActivityLogAlerts/írás | Műveletnapló riasztásának létrehozása vagy frissítése |
> | Műveletek | Microsoft. ininsights/AlertRules/aktivált/művelet | Klasszikus metrikus riasztás aktiválva |
> | Műveletek | Microsoft. bepillantást/AlertRules/delete | Klasszikus metrikai riasztás törlése |
> | Műveletek | Microsoft. ininsights/AlertRules/incidensek/olvasás | Klasszikus metrikai riasztási incidens beolvasása |
> | Műveletek | Microsoft. bepillantások/AlertRules/olvasás | Klasszikus metrikai riasztás beolvasása |
> | Műveletek | Microsoft. bepillantások/AlertRules/feloldott/művelet | Klasszikus metrikai riasztás megoldva |
> | Műveletek | Microsoft. elemzések/AlertRules/szabályozott/művelet | A klasszikus metrika riasztási szabálya szabályozva |
> | Műveletek | Microsoft. bepillantások/AlertRules/írás | Klasszikus metrikai riasztás létrehozása vagy frissítése |
> | Műveletek | Microsoft. bepillantást/AutoscaleSettings/delete | Egy autoskálázási beállítás törlése |
> | Műveletek | Microsoft. inviews/AutoscaleSettings/Providers/Microsoft. bepillantást/diagnosticSettings/READ | Erőforrás diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft. bepillantást/AutoscaleSettings/Providers/Microsoft. bepillantást/diagnosticSettings/Write | Erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft. inviews/AutoscaleSettings/Providers/Microsoft. bepillantást/logDefinitions/READ | Napló-definíciók olvasása |
> | Műveletek | Microsoft. inviews/AutoscaleSettings/Providers/Microsoft. bepillantást/MetricDefinitions/READ | Metrikus definíciók olvasása |
> | Műveletek | Microsoft. bepillantások/AutoscaleSettings/olvasás | Egy autoskálázási beállítás beolvasása |
> | Műveletek | Microsoft. bepillantások/AutoscaleSettings/scaledown/művelet | Az autoscale leskálázás kezdeményezve |
> | Műveletek | Microsoft. bepillantások/AutoscaleSettings/ScaledownResult/művelet | Az autoskálázás leskálázása befejeződött |
> | Műveletek | Microsoft. bepillantások/AutoscaleSettings/Scaleup/művelet | Vertikális felskálázás kezdeményezve |
> | Műveletek | Microsoft. bepillantások/AutoscaleSettings/ScaleupResult/művelet | Az autoskálázás felskálázása befejeződött |
> | Műveletek | Microsoft. bepillantások/AutoscaleSettings/írás | Egy autoskálázási beállítás létrehozása vagy frissítése |
> | Műveletek | Microsoft. bepillantások/alapkonfiguráció/olvasás | Metrika alaptervének beolvasása (előzetes verzió) |
> | Műveletek | Microsoft. bepillantások/CalculateBaseline/olvasás | Metrikai értékek alaptervének kiszámítása (előzetes verzió) |
> | Műveletek | Microsoft. bepillantások/összetevők/AnalyticsItems/törlés | Application Insights Analytics-elemek törlése |
> | Műveletek | Microsoft. bepillantások/összetevők/AnalyticsItems/olvasás | Application Insights Analytics-elemek olvasása |
> | Műveletek | Microsoft. bepillantások/összetevők/AnalyticsItems/írás | Application Insights Analytics-elemek írása |
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
> | Műveletek | Microsoft. betekintő/összetevők/szolgáltatók/Microsoft. bepillantást/MetricDefinitions/READ | Metrikus definíciók olvasása |
> | Műveletek | Microsoft. bepillantások/összetevők/kiürítés/művelet | Adatok törlése a Application Insightsból |
> | Műveletek | Microsoft. bepillantások/összetevők/lekérdezés/olvasás | Lekérdezések futtatása Application Insights naplókon |
> | Műveletek | Microsoft. bepillantások/összetevők/QuotaStatus/olvasás | Application Insights összetevő-kvóta állapotának olvasása |
> | Műveletek | Microsoft. bepillantások/összetevők/olvasás | Application betekintő összetevő konfigurációjának olvasása |
> | Műveletek | Microsoft. bepillantások/összetevők/SyntheticMonitorLocations/olvasás | Application Insights webteszt helyeinek olvasása |
> | Műveletek | Microsoft. bepillantások/összetevők/webtesztek/olvasás | Webteszt-konfiguráció olvasása |
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
> | Műveletek | Microsoft. bepillantások/naplók/ADAssessmentRecommendation/olvasás | Adatok beolvasása a ADAssessmentRecommendation táblából |
> | Műveletek | Microsoft. bepillantások/naplók/ADReplicationResult/olvasás | Adatok beolvasása a ADReplicationResult táblából |
> | Műveletek | Microsoft. bepillantások/naplók/ADSecurityAssessmentRecommendation/olvasás | Adatok beolvasása a ADSecurityAssessmentRecommendation táblából |
> | Műveletek | Microsoft. bepillantások/naplók/riasztás/olvasás | Adatok beolvasása a riasztási táblából |
> | Műveletek | Microsoft. bepillantások/naplók/AlertHistory/olvasás | Adatok beolvasása a AlertHistory táblából |
> | Műveletek | Microsoft. bepillantások/naplók/ApplicationInsights/olvasás | Adatok beolvasása a ApplicationInsights táblából |
> | Műveletek | Microsoft. bepillantások/naplók/AzureActivity/olvasás | Adatok beolvasása a AzureActivity táblából |
> | Műveletek | Microsoft. bepillantások/naplók/AzureMetrics/olvasás | Adatok beolvasása a AzureMetrics táblából |
> | Műveletek | Microsoft. bepillantások/naplók/BoundPort/olvasás | Adatok beolvasása a BoundPort táblából |
> | Műveletek | Microsoft. bepillantások/naplók/CommonSecurityLog/olvasás | Adatok beolvasása a CommonSecurityLog táblából |
> | Műveletek | Microsoft. bepillantások/naplók/ComputerGroup/olvasás | Adatok beolvasása a ComputerGroup táblából |
> | Műveletek | Microsoft. bepillantások/naplók/konfigurációváltozás/olvasás | Adatok beolvasása a konfigurációváltozás táblából |
> | Műveletek | Microsoft. bepillantások/naplók/ConfigurationData/olvasás | Adatok beolvasása a ConfigurationData táblából |
> | Műveletek | Microsoft. bepillantások/naplók/ContainerImageInventory/olvasás | Adatok beolvasása a ContainerImageInventory táblából |
> | Műveletek | Microsoft. bepillantások/naplók/ContainerInventory/olvasás | Adatok beolvasása a ContainerInventory táblából |
> | Műveletek | Microsoft. bepillantások/naplók/ContainerLog/olvasás | Adatok beolvasása a ContainerLog táblából |
> | Műveletek | Microsoft. bepillantások/naplók/ContainerServiceLog/olvasás | Adatok beolvasása a ContainerServiceLog táblából |
> | Műveletek | Microsoft. bepillantások/naplók/DeviceAppCrash/olvasás | Adatok beolvasása a DeviceAppCrash táblából |
> | Műveletek | Microsoft. bepillantások/naplók/DeviceAppLaunch/olvasás | Adatok beolvasása a DeviceAppLaunch táblából |
> | Műveletek | Microsoft. bepillantások/naplók/DeviceCalendar/olvasás | Adatok beolvasása a DeviceCalendar táblából |
> | Műveletek | Microsoft. bepillantások/naplók/DeviceCleanup/olvasás | Adatok beolvasása a DeviceCleanup táblából |
> | Műveletek | Microsoft. bepillantások/naplók/DeviceConnectSession/olvasás | Adatok beolvasása a DeviceConnectSession táblából |
> | Műveletek | Microsoft. bepillantások/naplók/DeviceEtw/olvasás | Adatok beolvasása a DeviceEtw táblából |
> | Műveletek | Microsoft. bepillantások/naplók/DeviceHardwareHealth/olvasás | Adatok beolvasása a DeviceHardwareHealth táblából |
> | Műveletek | Microsoft. bepillantások/naplók/DeviceHealth/olvasás | Adatok beolvasása a DeviceHealth táblából |
> | Műveletek | Microsoft. bepillantások/naplók/DeviceHeartbeat/olvasás | Adatok beolvasása a DeviceHeartbeat táblából |
> | Műveletek | Microsoft. bepillantások/naplók/DeviceSkypeHeartbeat/olvasás | Adatok beolvasása a DeviceSkypeHeartbeat táblából |
> | Műveletek | Microsoft. bepillantások/naplók/DeviceSkypeSignIn/olvasás | Adatok beolvasása a DeviceSkypeSignIn táblából |
> | Műveletek | Microsoft. bepillantások/naplók/DeviceSleepState/olvasás | Adatok beolvasása a DeviceSleepState táblából |
> | Műveletek | Microsoft. bepillantások/naplók/DHAppFailure/olvasás | Adatok beolvasása a DHAppFailure táblából |
> | Műveletek | Microsoft. bepillantások/naplók/DHAppReliability/olvasás | Adatok beolvasása a DHAppReliability táblából |
> | Műveletek | Microsoft. bepillantások/naplók/DHDriverReliability/olvasás | Adatok beolvasása a DHDriverReliability táblából |
> | Műveletek | Microsoft. bepillantások/naplók/DHLogonFailures/olvasás | Adatok beolvasása a DHLogonFailures táblából |
> | Műveletek | Microsoft. bepillantások/naplók/DHLogonMetrics/olvasás | Adatok beolvasása a DHLogonMetrics táblából |
> | Műveletek | Microsoft. bepillantások/naplók/DHOSCrashData/olvasás | Adatok beolvasása a DHOSCrashData táblából |
> | Műveletek | Microsoft. bepillantások/naplók/DHOSReliability/olvasás | Adatok beolvasása a DHOSReliability táblából |
> | Műveletek | Microsoft. bepillantások/naplók/DHWipAppLearning/olvasás | Adatok beolvasása a DHWipAppLearning táblából |
> | Műveletek | Microsoft. bepillantások/naplók/DnsEvents/olvasás | Adatok beolvasása a DnsEvents táblából |
> | Műveletek | Microsoft. bepillantások/naplók/DnsInventory/olvasás | Adatok beolvasása a DnsInventory táblából |
> | Műveletek | Microsoft. bepillantások/naplók/ETWEvent/olvasás | Adatok beolvasása a ETWEvent táblából |
> | Műveletek | Microsoft. elemzések/naplók/esemény/olvasás | Adatok beolvasása az Event táblából |
> | Műveletek | Microsoft. bepillantások/naplók/ExchangeAssessmentRecommendation/olvasás | Adatok beolvasása a ExchangeAssessmentRecommendation táblából |
> | Műveletek | Microsoft. bepillantások/naplók/ExchangeOnlineAssessmentRecommendation/olvasás | Adatok beolvasása a ExchangeOnlineAssessmentRecommendation táblából |
> | Műveletek | Microsoft. bepillantások/naplók/szívverés/olvasás | Adatok beolvasása a szívverési táblából |
> | Műveletek | Microsoft. bepillantások/naplók/IISAssessmentRecommendation/olvasás | Adatok beolvasása a IISAssessmentRecommendation táblából |
> | Műveletek | Microsoft. bepillantások/naplók/InboundConnection/olvasás | Adatok beolvasása a InboundConnection táblából |
> | Műveletek | Microsoft. bepillantások/naplók/KubeNodeInventory/olvasás | Adatok beolvasása a KubeNodeInventory táblából |
> | Műveletek | Microsoft. bepillantások/naplók/KubePodInventory/olvasás | Adatok beolvasása a KubePodInventory táblából |
> | Műveletek | Microsoft. bepillantások/naplók/LinuxAuditLog/olvasás | Adatok beolvasása a LinuxAuditLog táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAApplication/olvasás | Adatok beolvasása a MAApplication táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAApplicationHealth/olvasás | Adatok beolvasása a MAApplicationHealth táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAApplicationHealthAlternativeVersions/olvasás | Adatok beolvasása a MAApplicationHealthAlternativeVersions táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAApplicationHealthIssues/olvasás | Adatok beolvasása a MAApplicationHealthIssues táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAApplicationInstance/olvasás | Adatok beolvasása a MAApplicationInstance táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAApplicationInstanceReadiness/olvasás | Adatok beolvasása a MAApplicationInstanceReadiness táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAApplicationReadiness/olvasás | Adatok beolvasása a MAApplicationReadiness táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MADeploymentPlan/olvasás | Adatok beolvasása a MADeploymentPlan táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MADevice/olvasás | Adatok beolvasása a MADevice táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MADevicePnPHealth/olvasás | Adatok beolvasása a MADevicePnPHealth táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MADevicePnPHealthAlternativeVersions/olvasás | Adatok beolvasása a MADevicePnPHealthAlternativeVersions táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MADevicePnPHealthIssues/olvasás | Adatok beolvasása a MADevicePnPHealthIssues táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MADeviceReadiness/olvasás | Adatok beolvasása a MADeviceReadiness táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MADriverInstanceReadiness/olvasás | Adatok beolvasása a MADriverInstanceReadiness táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MADriverReadiness/olvasás | Adatok beolvasása a MADriverReadiness táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeAddin/olvasás | Adatok beolvasása a MAOfficeAddin táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeAddinHealth/olvasás | Adatok beolvasása a MAOfficeAddinHealth táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeAddinHealthIssues/olvasás | Adatok beolvasása a MAOfficeAddinHealthIssues táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeAddinInstance/olvasás | Adatok beolvasása a MAOfficeAddinInstance táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeAddinInstanceReadiness/olvasás | Adatok beolvasása a MAOfficeAddinInstanceReadiness táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeAddinReadiness/olvasás | Adatok beolvasása a MAOfficeAddinReadiness táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeApp/olvasás | Adatok beolvasása a MAOfficeApp táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeAppHealth/olvasás | Adatok beolvasása a MAOfficeAppHealth táblából |
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
> | Műveletek | Microsoft. bepillantások/naplók/MAOfficeSuiteInstance/olvasás | Adatok beolvasása a MAOfficeSuiteInstance táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAProposedPilotDevices/olvasás | Adatok beolvasása a MAProposedPilotDevices táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAWindowsBuildInfo/olvasás | Adatok beolvasása a MAWindowsBuildInfo táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAWindowsCurrencyAssessment/olvasás | Adatok beolvasása a MAWindowsCurrencyAssessment táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAWindowsCurrencyAssessmentDailyCounts/olvasás | Adatok beolvasása a MAWindowsCurrencyAssessmentDailyCounts táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAWindowsDeploymentStatus/olvasás | Adatok beolvasása a MAWindowsDeploymentStatus táblából |
> | Műveletek | Microsoft. bepillantások/naplók/MAWindowsSysReqInstanceReadiness/olvasás | Adatok beolvasása a MAWindowsSysReqInstanceReadiness táblából |
> | Műveletek | Microsoft. bepillantások/naplók/NetworkMonitoring/olvasás | Adatok beolvasása a NetworkMonitoring táblából |
> | Műveletek | Microsoft. bepillantások/naplók/OfficeActivity/olvasás | Adatok beolvasása a OfficeActivity táblából |
> | Műveletek | Microsoft. elemzések/naplók/művelet/olvasás | Adatok beolvasása a műveleti táblából |
> | Műveletek | Microsoft. bepillantások/naplók/OutboundConnection/olvasás | Adatok beolvasása a OutboundConnection táblából |
> | Műveletek | Microsoft. elemzések/naplók/Perf/olvasás | Adatok beolvasása a Perf táblából |
> | Műveletek | Microsoft. bepillantások/naplók/ProtectionStatus/olvasás | Adatok beolvasása a ProtectionStatus táblából |
> | Műveletek | Microsoft. elemzések/naplók/olvasás | Adatok olvasása az összes naplóból |
> | Műveletek | Microsoft. bepillantások/naplók/ReservedAzureCommonFields/olvasás | Adatok beolvasása a ReservedAzureCommonFields táblából |
> | Műveletek | Microsoft. bepillantások/naplók/ReservedCommonFields/olvasás | Adatok beolvasása a ReservedCommonFields táblából |
> | Műveletek | Microsoft. bepillantások/naplók/SCCMAssessmentRecommendation/olvasás | Adatok beolvasása a SCCMAssessmentRecommendation táblából |
> | Műveletek | Microsoft. bepillantások/naplók/SCOMAssessmentRecommendation/olvasás | Adatok beolvasása a SCOMAssessmentRecommendation táblából |
> | Műveletek | Microsoft. bepillantások/naplók/SecurityAlert/olvasás | Adatok beolvasása a SecurityAlert táblából |
> | Műveletek | Microsoft. bepillantások/naplók/SecurityBaseline/olvasás | Adatok beolvasása a SecurityBaseline táblából |
> | Műveletek | Microsoft. bepillantások/naplók/SecurityBaselineSummary/olvasás | Adatok beolvasása a SecurityBaselineSummary táblából |
> | Műveletek | Microsoft. bepillantások/naplók/SecurityDetection/olvasás | Adatok beolvasása a SecurityDetection táblából |
> | Műveletek | Microsoft. bepillantások/naplók/SecurityEvent/olvasás | Adatok beolvasása a SecurityEvent táblából |
> | Műveletek | Microsoft. bepillantások/naplók/ServiceFabricOperationalEvent/olvasás | Adatok beolvasása a ServiceFabricOperationalEvent táblából |
> | Műveletek | Microsoft. bepillantások/naplók/ServiceFabricReliableActorEvent/olvasás | Adatok beolvasása a ServiceFabricReliableActorEvent táblából |
> | Műveletek | Microsoft. bepillantások/naplók/ServiceFabricReliableServiceEvent/olvasás | Adatok beolvasása a ServiceFabricReliableServiceEvent táblából |
> | Műveletek | Microsoft. bepillantások/naplók/SfBAssessmentRecommendation/olvasás | Adatok beolvasása a SfBAssessmentRecommendation táblából |
> | Műveletek | Microsoft. bepillantások/naplók/SfBOnlineAssessmentRecommendation/olvasás | Adatok beolvasása a SfBOnlineAssessmentRecommendation táblából |
> | Műveletek | Microsoft. bepillantások/naplók/SharePointOnlineAssessmentRecommendation/olvasás | Adatok beolvasása a SharePointOnlineAssessmentRecommendation táblából |
> | Műveletek | Microsoft. bepillantások/naplók/SPAssessmentRecommendation/olvasás | Adatok beolvasása a SPAssessmentRecommendation táblából |
> | Műveletek | Microsoft. bepillantások/naplók/SQLAssessmentRecommendation/olvasás | Adatok beolvasása a SQLAssessmentRecommendation táblából |
> | Műveletek | Microsoft. bepillantások/naplók/SQLQueryPerformance/olvasás | Adatok beolvasása a SQLQueryPerformance táblából |
> | Műveletek | Microsoft. elemzések/naplók/syslog/READ | Adatok beolvasása a syslog-táblából |
> | Műveletek | Microsoft. bepillantások/naplók/SysmonEvent/olvasás | Adatok beolvasása a SysmonEvent táblából |
> | Műveletek | Microsoft. elemzések/naplók/táblák. egyéni/olvasási | Adatok olvasása bármely egyéni naplóból |
> | Műveletek | Microsoft. bepillantások/naplók/UAApp/olvasás | Adatok beolvasása a UAApp táblából |
> | Műveletek | Microsoft. bepillantások/naplók/UAComputer/olvasás | Adatok beolvasása a UAComputer táblából |
> | Műveletek | Microsoft. bepillantások/naplók/UAComputerRank/olvasás | Adatok beolvasása a UAComputerRank táblából |
> | Műveletek | Microsoft. bepillantások/naplók/UADriver/olvasás | Adatok beolvasása a UADriver táblából |
> | Műveletek | Microsoft. bepillantások/naplók/UADriverProblemCodes/olvasás | Adatok beolvasása a UADriverProblemCodes táblából |
> | Műveletek | Microsoft. bepillantások/naplók/UAFeedback/olvasás | Adatok beolvasása a UAFeedback táblából |
> | Műveletek | Microsoft. bepillantások/naplók/UAHardwareSecurity/olvasás | Adatok beolvasása a UAHardwareSecurity táblából |
> | Műveletek | Microsoft. bepillantások/naplók/UAIESiteDiscovery/olvasás | Adatok beolvasása a UAIESiteDiscovery táblából |
> | Műveletek | Microsoft. bepillantások/naplók/UAOfficeAddIn/olvasás | Adatok beolvasása a UAOfficeAddIn táblából |
> | Műveletek | Microsoft. bepillantások/naplók/UAProposedActionPlan/olvasás | Adatok beolvasása a UAProposedActionPlan táblából |
> | Műveletek | Microsoft. bepillantások/naplók/UASysReqIssue/olvasás | Adatok beolvasása a UASysReqIssue táblából |
> | Műveletek | Microsoft. bepillantások/naplók/UAUpgradedComputer/olvasás | Adatok beolvasása a UAUpgradedComputer táblából |
> | Műveletek | Microsoft. elemzések/naplók/frissítés/olvasás | Adatok beolvasása a frissítési táblából |
> | Műveletek | Microsoft. bepillantások/naplók/UpdateRunProgress/olvasás | Adatok beolvasása a UpdateRunProgress táblából |
> | Műveletek | Microsoft. bepillantások/naplók/updateSummary típusú/olvasás | Adatok beolvasása a updateSummary típusú táblából |
> | Műveletek | Microsoft. elemzések/naplók/használat/olvasás | Adatok beolvasása a használati táblából |
> | Műveletek | Microsoft. bepillantások/naplók/W3CIISLog/olvasás | Adatok beolvasása a W3CIISLog táblából |
> | Műveletek | Microsoft. bepillantások/naplók/WaaSDeploymentStatus/olvasás | Adatok beolvasása a WaaSDeploymentStatus táblából |
> | Műveletek | Microsoft. bepillantások/naplók/WaaSInsiderStatus/olvasás | Adatok beolvasása a WaaSInsiderStatus táblából |
> | Műveletek | Microsoft. bepillantások/naplók/WaaSUpdateStatus/olvasás | Adatok beolvasása a WaaSUpdateStatus táblából |
> | Műveletek | Microsoft. bepillantások/naplók/WDAVStatus/olvasás | Adatok beolvasása a WDAVStatus táblából |
> | Műveletek | Microsoft. bepillantások/naplók/WDAVThreat/olvasás | Adatok beolvasása a WDAVThreat táblából |
> | Műveletek | Microsoft. bepillantások/naplók/WindowsClientAssessmentRecommendation/olvasás | Adatok beolvasása a WindowsClientAssessmentRecommendation táblából |
> | Műveletek | Microsoft. bepillantások/naplók/WindowsFirewall/olvasás | Adatok beolvasása a WindowsFirewall táblából |
> | Műveletek | Microsoft. bepillantások/naplók/WindowsServerAssessmentRecommendation/olvasás | Adatok beolvasása a WindowsServerAssessmentRecommendation táblából |
> | Műveletek | Microsoft. bepillantások/naplók/WireData/olvasás | Adatok beolvasása a WireData táblából |
> | Műveletek | Microsoft. bepillantások/naplók/WUDOAggregatedStatus/olvasás | Adatok beolvasása a WUDOAggregatedStatus táblából |
> | Műveletek | Microsoft. bepillantások/naplók/WUDOStatus/olvasás | Adatok beolvasása a WUDOStatus táblából |
> | Műveletek | Microsoft. bepillantást/MetricAlerts/delete | Metrikai riasztás törlése |
> | Műveletek | Microsoft. bepillantások/MetricAlerts/olvasás | Metrika riasztásának beolvasása |
> | Műveletek | Microsoft. bepillantások/MetricAlerts/állapot/olvasás | Metrikus riasztás állapotának olvasása |
> | Műveletek | Microsoft. bepillantások/MetricAlerts/írás | Metrikai riasztás létrehozása vagy frissítése |
> | Műveletek | Microsoft. bepillantások/MetricBaselines/olvasás | Metrika alapkonfigurációinak olvasása |
> | Műveletek | Microsoft. inviews/MetricDefinitions/Microsoft. bepillantások/olvasás | Metrikus definíciók olvasása |
> | Műveletek | Microsoft. inviews/MetricDefinitions/Providers/Microsoft. bepillantások/olvasás | Metrikus definíciók olvasása |
> | Műveletek | Microsoft. bepillantások/MetricDefinitions/olvasás | Metrikus definíciók olvasása |
> | Műveletek | Microsoft. bepillantások/Metricnamespaces/olvasás | Metrikus névterek olvasása |
> | Műveletek | Microsoft. elemzések/mérőszámok/művelet | Metrika művelet |
> | Műveletek | Microsoft. inviews/mérőszámok/Microsoft. bepillantások/olvasás | Metrikák olvasása |
> | Műveletek | Microsoft. inviews/metrika/szolgáltatók/mérőszámok/olvasás | Metrikák olvasása |
> | Műveletek | Microsoft. bepillantások/mérőszámok/olvasás | Metrikák olvasása |
> | DataAction | Microsoft. bepillantások/mérőszámok/írás | Mérőszámok írása |
> | Műveletek | Microsoft. bepillantások/MigrateToNewpricingModel/művelet | Előfizetés áttelepíteni az új díjszabási modellbe |
> | Műveletek | Microsoft. bepillantások/műveletek/olvasás | Olvasási műveletek |
> | Műveletek | Microsoft. elemzések/regisztráció/művelet | A Microsoft bepillantást nyújtó szolgáltató regisztrálása |
> | Műveletek | Microsoft. bepillantások/RollbackToLegacyPricingModel/művelet | Előfizetés visszaállítása a régi díjszabási modellre |
> | Műveletek | Microsoft. bepillantást/ScheduledQueryRules/delete | Ütemezett lekérdezési szabály törlése |
> | Műveletek | Microsoft. bepillantások/ScheduledQueryRules/olvasás | Ütemezett lekérdezési szabály olvasása |
> | Műveletek | Microsoft. bepillantások/ScheduledQueryRules/írás | Ütemezett lekérdezési szabály írása |
> | Műveletek | Microsoft. bepillantások/bérlők/regisztráció/művelet | A Microsoft bepillantást nyújtó szolgáltató inicializálása |
> | Műveletek | Microsoft. elemzések/Regisztráció törlése/művelet | A Microsoft bepillantást nyújtó szolgáltató regisztrálása |
> | Műveletek | Microsoft. bepillantások/webtesztek/törlés | Webteszt-konfiguráció törlése |
> | Műveletek | Microsoft. bepillantások/webtesztek/GetToken/olvasás | Webteszt-jogkivonat beolvasása |
> | Műveletek | Microsoft. bepillantások/webtesztek/MetricDefinitions/olvasás | Webteszt metrikájának definícióinak olvasása |
> | Műveletek | Microsoft. bepillantások/webtesztek/mérőszámok/olvasás | Webteszt-metrikák olvasása |
> | Műveletek | Microsoft. bepillantások/webtesztek/olvasás | Webteszt-konfiguráció olvasása |
> | Műveletek | Microsoft. bepillantások/webtesztek/írás | Egy webteszt-konfigurációba való írás |
> | Műveletek | Microsoft. bepillantások/munkafüzetek/törlés | Munkafüzet törlése |
> | Műveletek | Microsoft. bepillantások/munkafüzetek/olvasás | Munkafüzet beolvasása |
> | Műveletek | Microsoft. bepillantások/munkafüzetek/írás | Munkafüzet létrehozása vagy frissítése |

## <a name="microsoftintune"></a>Microsoft. Intune

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Intune/diagnosticsettings/delete | Diagnosztikai beállítások törlése |
> | Műveletek | Microsoft. Intune/diagnosticsettings/READ | Diagnosztikai beállítások olvasása |
> | Műveletek | Microsoft. Intune/diagnosticsettings/írás | Diagnosztikai beállítások írása |
> | Műveletek | Microsoft. Intune/diagnosticsettingscategories/READ | Diagnosztikai beállítások kategóriáinak olvasása |

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. IoTCentral/appTemplates/művelet | Az Azure IoT Central összes elérhető alkalmazási sablonjának beolvasása |
> | Műveletek | Microsoft. IoTCentral/checkNameAvailability/művelet | Ellenőrzi, hogy elérhető-e IoT Central alkalmazás neve |
> | Műveletek | Microsoft. IoTCentral/checkSubdomainAvailability/művelet | Ellenőrzi, hogy elérhető-e IoT Central alkalmazás altartománya |
> | Műveletek | Microsoft. IoTCentral/IoTApps/delete | IoT Central-alkalmazások törlése |
> | Műveletek | Microsoft. IoTCentral/IoTApps/READ | Egyetlen IoT Central alkalmazást kap |
> | Műveletek | Microsoft. IoTCentral/IoTApps/Write | IoT Central-alkalmazások létrehozása vagy frissítése |
> | Műveletek | Microsoft. IoTCentral/Operations/READ | Az összes elérhető művelet beolvasása IoT Central alkalmazásokban |
> | Műveletek | Microsoft. IoTCentral/regisztráció/művelet | Az Azure IoT Central erőforrás-szolgáltatóhoz tartozó előfizetés regisztrálása |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. IoTSpaces/Graph/delete | Törli a Microsoft. IoTSpaces Graph-erőforrást |
> | Műveletek | Microsoft. IoTSpaces/gráf/olvasás | A Microsoft. IoTSpaces Graph-erőforrás (ok) beolvasása |
> | Műveletek | Microsoft. IoTSpaces/gráf/írás | Microsoft. IoTSpaces Graph-erőforrás létrehozása |
> | Műveletek | Microsoft. IoTSpaces/regisztráció/művelet | A Microsoft. IoTSpaces Graph erőforrás-szolgáltatóhoz tartozó előfizetés regisztrálása az erőforrások létrehozásának engedélyezéséhez |

## <a name="microsoftkeyvault"></a>Microsoft. kulcstartó

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. kulcstartó/checkNameAvailability/olvasás | Ellenőrzi, hogy a kulcstároló neve érvényes-e, és nincs-e használatban |
> | Műveletek | Microsoft. kulcstartó/deletedVaults/olvasás | A Soft Deleted Key vaultok tulajdonságainak megtekintése |
> | Műveletek | Microsoft. Key Vault/hsmPools/delete | HSM-készlet törlése |
> | Műveletek | Microsoft. kulcstartó/hsmPools/joinVault/művelet | Kulcstartó csatlakoztatása HSM-készlethez |
> | Műveletek | Microsoft. kulcstartó/hsmPools/olvasás | HSM-készlet tulajdonságainak megtekintése |
> | Műveletek | Microsoft. kulcstartó/hsmPools/írás | Új HSM-készlet létrehozása egy meglévő HSM-készlet tulajdonságainak frissítéséhez |
> | Műveletek | Microsoft. kulcstartó/Locations/deletedVaults/Purge/művelet | Helyreállítható törölhető kulcstartó törlése |
> | Műveletek | Microsoft. Key Vault/Locations/deletedVaults/READ | A törölt kulcstartó tulajdonságainak megtekintése |
> | Műveletek | Microsoft. kulcstartó/Locations/deleteVirtualNetworkOrSubnets/Action | Értesíti a Microsoft. kulcstartót arról, hogy egy virtuális hálózat vagy alhálózat törlése folyamatban van |
> | Műveletek | Microsoft. Key Vault/Locations/operationResults/READ | Hosszú futtatású művelet eredményének ellenőrzéséhez |
> | Műveletek | Microsoft. kulcstartó/műveletek/olvasás | A Microsoft. kulcstartó erőforrás-szolgáltatóján elérhető műveletek listája |
> | Műveletek | Microsoft. kulcstartó/regisztráció/művelet | Előfizetés regisztrálása |
> | Műveletek | Microsoft. kulcstartó/regisztráció/művelet | Előfizetés regisztrációjának törlése |
> | Műveletek | Microsoft. kulcstartó/tárolók/accessPolicies/írás | Meglévő hozzáférési szabályzat frissítése az egyesítéssel vagy a lecseréléssel, vagy új hozzáférési szabályzat hozzáadásával egy tárba. |
> | Műveletek | Microsoft. kulcstartó/tárolók/törlés | Kulcstároló törlése |
> | Műveletek | Microsoft. kulcstartó/tárolók/üzembe helyezés/művelet | Lehetővé teszi az Azure-erőforrások üzembe helyezése során a kulcstartó titkos kulcsaihoz való hozzáférését |
> | Műveletek | Microsoft. kulcstartó/tárolók/eventGridFilters/törlés | Értesíti a Microsoft. Key Vaultot arról, hogy az Key Vault EventGrid-előfizetése törölve lett |
> | Műveletek | Microsoft. kulcstartó/tárolók/eventGridFilters/olvasás | Értesíti a Microsoft. Key Vaultot arról, hogy az Key Vault EventGrid-előfizetését tekinti meg |
> | Műveletek | Microsoft. kulcstartó/tárolók/eventGridFilters/írás | Értesíti a Microsoft. Key Vaultot arról, hogy a Key Vault új EventGrid-előfizetést hoz létre |
> | Műveletek | Microsoft. kulcstartó/tárolók/olvasás | Key Vault tulajdonságainak megtekintése |
> | Műveletek | Microsoft. kulcstartó/tárolók/titkok/olvasás | Megtekintheti egy titkos kulcs tulajdonságait, de nem az értékét. |
> | Műveletek | Microsoft. kulcstartó/tárolók/titkok/írás | Hozzon létre egy új titkot, vagy frissítse egy meglévő titok értékét. |
> | Műveletek | Microsoft. kulcstartó/tárolók/írás | Új kulcstartó létrehozása vagy egy meglévő kulcstartó tulajdonságainak frissítése |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Kusto/fürtök/aktiválás/művelet | Elindítja a fürtöt. |
> | Műveletek | Microsoft. Kusto/fürtök/AttachedDatabaseConfigurations/delete | Töröl egy csatolt adatbázis-konfigurációs resourceCopy. |
> | Műveletek | Microsoft. Kusto/fürtök/AttachedDatabaseConfigurations/READ | Beolvas egy csatolt adatbázis-konfigurációs resourceCopy. |
> | Műveletek | Microsoft. Kusto/fürtök/AttachedDatabaseConfigurations/írás | Egy csatolt adatbázis-konfigurációs resourceCopy ír. |
> | Műveletek | Microsoft. Kusto/fürtök/CheckNameAvailability/művelet | A fürt nevének rendelkezésre állását ellenőrzi. |
> | Műveletek | Microsoft. Kusto/fürtök/adatbázisok/AddPrincipals/művelet | Adatbázis-rendszerbiztonsági tag hozzáadására szolgál. |
> | Műveletek | Microsoft. Kusto/fürtök/adatbázisok/CheckNameAvailability/művelet | Egy adott típushoz tartozó név rendelkezésre állásának ellenőrzése. |
> | Műveletek | Microsoft. Kusto/fürtök/adatbázisok/DataConnections/delete | Törli az adatkapcsolatok resourceCopy. |
> | Műveletek | Microsoft. Kusto/fürtök/adatbázisok/DataConnections/olvasás | Adatkapcsolatok resourceCopy beolvasása. |
> | Műveletek | Microsoft. Kusto/fürtök/adatbázisok/DataConnections/írás | Az adatkapcsolatok resourceCopy ír. |
> | Műveletek | Microsoft. Kusto/fürtök/adatbázisok/DataConnectionValidation/művelet | Az adatbázis-adatkapcsolatok ellenőrzése. |
> | Műveletek | Microsoft. Kusto/fürtök/adatbázisok/törlés | Töröl egy adatbázis-resourceCopy. |
> | Műveletek | Microsoft. Kusto/fürtök/adatbázisok/EventHubConnections/delete | Az Event hub-kapcsolatok resourceCopy törlése. |
> | Műveletek | Microsoft. Kusto/fürtök/adatbázisok/EventHubConnections/olvasás | Az Event hub-kapcsolatok resourceCopy olvasása. |
> | Műveletek | Microsoft. Kusto/fürtök/adatbázisok/EventHubConnections/írás | Az Event hub-kapcsolatok resourceCopy írja. |
> | Műveletek | Microsoft. Kusto/fürtök/adatbázisok/EventHubConnectionValidation/művelet | Ellenőrzi az adatbázis-esemény hub-kapcsolatát. |
> | Műveletek | Microsoft. Kusto/fürtök/adatbázisok/ListPrincipals/művelet | Az adatbázis-rendszerbiztonsági tag felsorolása. |
> | Műveletek | Microsoft. Kusto/fürtök/adatbázisok/PrincipalAssignments/delete | Törli az adatbázis egyszerű hozzárendeléseinek resourceCopy. |
> | Műveletek | Microsoft. Kusto/fürtök/adatbázisok/PrincipalAssignments/olvasás | Egy adatbázis-hozzárendelési resourceCopy beolvasása. |
> | Műveletek | Microsoft. Kusto/fürtök/adatbázisok/PrincipalAssignments/írás | Az adatbázis egyszerű hozzárendeléseinek resourceCopy írja. |
> | Műveletek | Microsoft. Kusto/fürtök/adatbázisok/olvasás | Egy adatbázis resourceCopy beolvasása. |
> | Műveletek | Microsoft. Kusto/fürtök/adatbázisok/RemovePrincipals/művelet | Az adatbázis-rendszerbiztonsági tag eltávolítása. |
> | Műveletek | Microsoft. Kusto/fürtök/adatbázisok/írás | Egy adatbázis-resourceCopy ír. |
> | Műveletek | Microsoft. Kusto/fürtök/inaktiválás/művelet | Leállítja a fürtöt. |
> | Műveletek | Microsoft. Kusto/fürtök/törlés | Egy fürt resourceCopy törlése. |
> | Műveletek | Microsoft. Kusto/fürtök/DetachFollowerDatabases/művelet | Leválasztja a követők adatbázisait. |
> | Műveletek | Microsoft. Kusto/fürtök/DiagnoseVirtualNetwork/művelet | A hálózati kapcsolat állapotának diagnosztizálása azon külső erőforrások esetében, amelyeken a szolgáltatás depedent. |
> | Műveletek | Microsoft. Kusto/fürtök/ListFollowerDatabases/művelet | A követő adatbázisait sorolja fel. |
> | Műveletek | Microsoft. Kusto/fürtök/olvasás | Egy fürt resourceCopy beolvasása. |
> | Műveletek | Microsoft. Kusto/fürtök/SKU/READ | Egy fürt SKU-resourceCopy beolvasása. |
> | Műveletek | Microsoft. Kusto/fürtök/indítás/művelet | Elindítja a fürtöt. |
> | Műveletek | Microsoft. Kusto/fürtök/leállítás/művelet | Leállítja a fürtöt. |
> | Műveletek | Microsoft. Kusto/fürtök/írás | Egy fürt resourceCopy ír. |
> | Műveletek | Microsoft. Kusto/Locations/CheckNameAvailability/Action | A resourceCopy nevének rendelkezésre állását ellenőrzi. |
> | Műveletek | Microsoft. Kusto/Locations/GetNetworkPolicies/Action | Hálózati leképezési szabályzatok beolvasása |
> | Műveletek | Microsoft. Kusto/Locations/operationresults/READ | Olvasási műveletek resourceCopys |
> | Műveletek | Microsoft. Kusto/Operations/READ | Olvasási műveletek resourceCopys |
> | Műveletek | Microsoft. Kusto/regisztráció/művelet | Előfizetés-regisztrációs művelet |
> | Műveletek | Microsoft. Kusto/regisztráció/művelet | Regisztrálja az előfizetést a Kusto erőforrás-szolgáltatón. |
> | Műveletek | Microsoft. Kusto/SKU/READ | SKU-resourceCopy olvasása. |
> | Műveletek | Microsoft. Kusto/regisztráció/művelet | Az előfizetés regisztrációjának törlése a Kusto erőforrás-szolgáltatóra. |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. LabServices/labAccounts/CreateLab/művelet | Tesztkörnyezet létrehozása labor-fiókban. |
> | Műveletek | Microsoft. LabServices/labAccounts/delete | Tesztkörnyezet-fiókok törlése. |
> | Műveletek | Microsoft. LabServices/labAccounts/galleryImages/delete | Katalógus lemezképének törlése. |
> | Műveletek | Microsoft. LabServices/labAccounts/galleryImages/READ | Katalógus képeinek olvasása. |
> | Műveletek | Microsoft. LabServices/labAccounts/galleryImages/Write | Katalógusbeli rendszerképek hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. LabServices/labAccounts/GetRegionalAvailability/művelet | Regionális elérhetőségi információk beolvasása a labor-fiókban konfigurált egyes méretek kategóriához |
> | Műveletek | Microsoft. LabServices/labAccounts/Labs/AddUsers/művelet | Felhasználók hozzáadása laborhoz |
> | Műveletek | Microsoft. LabServices/labAccounts/Labs/delete | Labs törlése. |
> | Műveletek | Microsoft. LabServices/labAccounts/Labs/environmentSettings/delete | Környezeti beállítás törlése. |
> | Műveletek | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Environments/delete | Környezetek törlése. |
> | Műveletek | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Environments/READ | Környezetek olvasása. |
> | Műveletek | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Environments/ResetPassword metódusát/Action | A felhasználó jelszavának alaphelyzetbe állítása egy adott környezetben |
> | Műveletek | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Environments/Start/Action | A környezeten belüli összes erőforrás indításával elindítja a környezetet. |
> | Műveletek | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Environments/leállítás/művelet | Egy környezet leállítása a környezetben lévő összes erőforrás leállításával |
> | Műveletek | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Environments/Write | Környezetek hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. LabServices/labAccounts/Labs/environmentSettings/közzétételi/művelet | A környezet beállításához szükséges erőforrások kiosztása/kiosztása a tesztkörnyezet/környezet beállítása aktuális állapotán alapul. |
> | Műveletek | Microsoft. LabServices/labAccounts/Labs/environmentSettings/READ | Környezeti beállítások olvasása. |
> | Műveletek | Microsoft. LabServices/labAccounts/Labs/environmentSettings/ResetPassword metódusát/művelet | Visszaállítja a jelszót a sablon virtuális gépén. |
> | Műveletek | Microsoft. LabServices/labAccounts/Labs/environmentSettings/SaveImage/művelet | A sablon aktuális képét menti a megosztott galériába a labor-fiókban |
> | Műveletek | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Schedules/delete | Ütemtervek törlése. |
> | Műveletek | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Schedules/READ | Ütemtervek olvasása. |
> | Műveletek | Microsoft. LabServices/labAccounts/Labs/environmentSettings/ütemterv/írás | Ütemtervek hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Start/művelet | A sablonon belüli összes erőforrás indításával elindítja a sablont. |
> | Műveletek | Microsoft. LabServices/labAccounts/Labs/environmentSettings/leállítás/művelet | Leállít egy sablont a sablonban lévő összes erőforrás leállításával. |
> | Műveletek | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Write | Környezeti beállítás hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. LabServices/labAccounts/Labs/olvasás | A laborok beolvasása. |
> | Műveletek | Microsoft. LabServices/labAccounts/Labs/SendEmail/művelet | E-mail-cím küldése a laborhoz tartozó regisztrációs hivatkozással |
> | Műveletek | Microsoft. LabServices/labAccounts/Labs/felhasználók/törlés | Felhasználók törlése. |
> | Műveletek | Microsoft. LabServices/labAccounts/Labs/felhasználók/olvasás | Felhasználók beolvasása. |
> | Műveletek | Microsoft. LabServices/labAccounts/Labs/felhasználók/írás | Felhasználók hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. LabServices/labAccounts/Labs/írás | Labs hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. LabServices/labAccounts/READ | Laboratóriumi fiókok olvasása. |
> | Műveletek | Microsoft. LabServices/labAccounts/sharedGalleries/delete | Sharedgalleries törlése. |
> | Műveletek | Microsoft. LabServices/labAccounts/sharedGalleries/READ | Sharedgalleries olvasása. |
> | Műveletek | Microsoft. LabServices/labAccounts/sharedGalleries/Write | Sharedgalleries hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. LabServices/labAccounts/sharedImages/delete | Sharedimages törlése. |
> | Műveletek | Microsoft. LabServices/labAccounts/sharedImages/READ | Sharedimages olvasása. |
> | Műveletek | Microsoft. LabServices/labAccounts/sharedImages/Write | Sharedimages hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. LabServices/labAccounts/Write | Lab-fiókok hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. LabServices/Locations/Operations/READ | Olvasási műveletek. |
> | Műveletek | Microsoft. LabServices/regisztráció/művelet | Regisztrálja az előfizetést |
> | Műveletek | Microsoft. LabServices/Users/ListAllEnvironments/Action | A felhasználó összes környezetének listázása |
> | Műveletek | Microsoft. LabServices/felhasználók/regisztráció/művelet | Felhasználó regisztrálása felügyelt laborban |
> | Műveletek | Microsoft. LabServices/Users/ResetPassword metódusát/Action | A felhasználó jelszavának alaphelyzetbe állítása egy adott környezetben |
> | Műveletek | Microsoft. LabServices/Users/StartEnvironment/Action | A környezeten belüli összes erőforrás indításával elindítja a környezetet. |
> | Műveletek | Microsoft. LabServices/Users/StopEnvironment/Action | Egy környezet leállítása a környezetben lévő összes erőforrás leállításával |
> | Műveletek | Microsoft. LabServices/Users/UserSettings/Action | Frissíti és visszaadja a személyes felhasználói beállításokat. |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Logic/integrationAccounts/szerződések/törlés | A szerződés törlése az integrációs fiókban. |
> | Műveletek | Microsoft. Logic/integrationAccounts/szerződések/listContentCallbackUrl/művelet | Beolvassa a szerződés tartalmának visszahívási URL-címét az integrációs fiókban. |
> | Műveletek | Microsoft. Logic/integrationAccounts/szerződések/olvasás | Beolvassa a szerződést az integrációs fiókban. |
> | Műveletek | Microsoft. Logic/integrationAccounts/szerződések/írás | Létrehozza vagy frissíti a szerződést az integrációs fiókban. |
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
> | Műveletek | Microsoft. Logic/integrationAccounts/regenerateAccessKey/művelet | Újragenerálta a hozzáférési kulcs titkait. |
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
> | Műveletek | Microsoft. Logic/integrationServiceEnvironments/operationStatuses/READ | Az integrációs szolgáltatás környezeti műveleti állapotának beolvasása. |
> | Műveletek | Microsoft. Logic/integrationServiceEnvironments/Providers/Microsoft. bepillantást/metricDefinitions/READ | Beolvassa az integrációs szolgáltatás környezeti metrikájának definícióit. |
> | Műveletek | Microsoft. Logic/integrationServiceEnvironments/READ | Beolvassa az integrációs szolgáltatási környezetet. |
> | Műveletek | Microsoft. Logic/integrationServiceEnvironments/Write | Az integrációs szolgáltatási környezet létrehozása vagy frissítése. |
> | Műveletek | Microsoft. Logic/Locations/munkafolyamatok/recommendOperationGroups/művelet | Beolvassa a munkafolyamat-javaslat műveleti csoportjait. |
> | Műveletek | Microsoft. Logic/Locations/munkafolyamatok/érvényesítés/művelet | Ellenőrzi a munkafolyamatot. |
> | Műveletek | Microsoft. Logic/Operations/READ | Lekéri a műveletet. |
> | Műveletek | Microsoft. Logic/regisztráció/művelet | Regisztrálja a Microsoft. Logic erőforrás-szolgáltatót egy adott előfizetéshez. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/accessKeys/törlés | Törli a hozzáférési kulcsot. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/accessKeys/lista/művelet | Felsorolja a hozzáférési kulcs titkait. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/accessKeys/olvasás | A hozzáférési kulcs beolvasása. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/accessKeys/újragenerált/művelet | Újragenerálta a hozzáférési kulcs titkait. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/accessKeys/írás | Létrehozza vagy frissíti a hozzáférési kulcsot. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/törlés | Törli a munkafolyamatot. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/érzékelők/olvasás | A munkafolyamat-detektor beolvasása. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/letiltás/művelet | Letiltja a munkafolyamatot. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/engedélyezés/művelet | A munkafolyamat engedélyezése. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/listCallbackUrl/művelet | A munkafolyamat visszahívási URL-címének beolvasása. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/listSwagger/művelet | A munkafolyamat hencegő definícióinak beolvasása. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/áthelyezés/művelet | Áthelyezi a munkafolyamatot a meglévő előfizetés-azonosítóból, erőforráscsoporthoz és/vagy névre egy másik előfizetés-azonosítóra, erőforráscsoporthoz és/vagy névre. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/szolgáltatók/Microsoft. bepillantást/diagnosticSettings/READ | A munkafolyamat diagnosztikai beállításainak beolvasása. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/szolgáltatók/Microsoft. bepillantások/diagnosticSettings/írás | Létrehozza vagy frissíti a munkafolyamat diagnosztikai beállításait. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/szolgáltatók/Microsoft. bepillantást/logDefinitions/READ | A munkafolyamat-napló definícióinak beolvasása. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/szolgáltatók/Microsoft. bepillantást/metricDefinitions/READ | A munkafolyamat metrika-definícióinak beolvasása. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/olvasás | Beolvassa a munkafolyamatot. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/regenerateAccessKey/művelet | Újragenerálta a hozzáférési kulcs titkait. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/Futtatás/művelet | Elindítja a munkafolyamat futtatását. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/futtatások/műveletek/listExpressionTraces/művelet | Lekéri a munkafolyamat-futtatási művelet kifejezésének nyomkövetését. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/futtatások/műveletek/olvasás | Beolvassa a munkafolyamat-futtatási műveletet. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/futtatások/műveletek/ismétlődések/listExpressionTraces/művelet | Lekéri a munkafolyamat-futtatási művelet ismétlődési kifejezésének nyomkövetését. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/futtatások/műveletek/ismétlődések/olvasás | A munkafolyamat-futtatási művelet ismétlődésének beolvasása. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/futtatások/műveletek/ismétlődések/requestHistories/olvasás | Beolvassa a munkafolyamat-futtatási műveletre vonatkozó kérelmek előzményeit. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/futtatások/műveletek/requestHistories/olvasás | A munkafolyamat-futtatási műveleti kérelmek előzményeinek beolvasása. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/futtatások/műveletek/scoperepetitions/olvasás | Beolvassa a munkafolyamat-futtatási művelet hatókörének ismétlődését. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/futtatások/megszakítás/művelet | Egy munkafolyamat futtatásának megszakítása. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/futtatások/törlés | Egy munkafolyamat futtatását törli. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/futtatások/műveletek/olvasás | A munkafolyamat-futtatási művelet állapotának beolvasása. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/futtatások/olvasás | A munkafolyamat futtatásának beolvasása. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/felfüggesztés/művelet | Felfüggeszti a munkafolyamatot. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/triggerek/előzmények/olvasás | Az trigger előzményeinek beolvasása. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/triggerek/előzmények/újraküldése/művelet | A munkafolyamat-trigger újraküldése. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/eseményindítók/listCallbackUrl/művelet | Az trigger visszahívási URL-címének beolvasása. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/triggerek/olvasás | A trigger beolvasása. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/eseményindítók/alaphelyzetbe állítás/művelet | A trigger alaphelyzetbe állítása. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/eseményindítók/Futtatás/művelet | Végrehajtja az triggert. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/eseményindítók/setState/művelet | A trigger állapotának beállítása. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/érvényesítés/művelet | Ellenőrzi a munkafolyamatot. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/verziók/olvasás | A munkafolyamat verziójának beolvasása. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/verziók/eseményindítók/listCallbackUrl/művelet | Az trigger visszahívási URL-címének beolvasása. |
> | Műveletek | Microsoft. Logic/munkafolyamatok/írás | Létrehozza vagy frissíti a munkafolyamatot. |

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. MachineLearning/commitmentPlans/commitmentAssociations/Move/Action | Bármely Machine Learning kötelezettségvállalási terv társításának áthelyezése |
> | Műveletek | Microsoft. MachineLearning/commitmentPlans/commitmentAssociations/READ | Bármely Machine Learning kötelezettségvállalási terv társításának beolvasása |
> | Műveletek | Microsoft. MachineLearning/commitmentPlans/delete | Machine Learning kötelezettségvállalási terv törlése |
> | Műveletek | Microsoft. MachineLearning/commitmentPlans/JOIN/Action | Csatlakozás bármely Machine Learning kötelezettségvállalási tervhez |
> | Műveletek | Microsoft. MachineLearning/commitmentPlans/READ | Machine Learning kötelezettségvállalási terv beolvasása |
> | Műveletek | Microsoft. MachineLearning/commitmentPlans/Write | Machine Learning kötelezettségvállalási terv létrehozása vagy frissítése |
> | Műveletek | Microsoft. MachineLearning/Locations/operationresults/READ | Machine Learning művelet eredményének beolvasása |
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
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/szolgáltatások/ACI/listkeys műveletének beolvasása/művelet | A Machine Learning Services munkaterület (ek) ACI szolgáltatásaihoz tartozó kulcsok listázása |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/szolgáltatások/ACI/írás | ACI-szolgáltatások létrehozása vagy frissítése Machine Learning Services munkaterületen |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/szolgáltatások/Kaba/devtest/delete | Törli a Machine Learning Services munkaterületen található devtest AK-szolgáltatásait |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/szolgáltatások/AK/devtest/listkeys műveletének beolvasása/művelet | A (z) Machine Learning Services munkaterületen található devtest AK-szolgáltatások kulcsainak listázása |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/szolgáltatások/AK/devtest/pontszám/művelet | A Machine Learning Services munkaterület (ek) ben található devtest |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/szolgáltatások/AK/devtest/írás | Devtest AK-szolgáltatások létrehozása vagy frissítése Machine Learning Services munkaterületen |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/szolgáltatások/AK/gyártási/törlés | A (z) Machine Learning Services munkaterület |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/szolgáltatások/AK/Prod/listkeys műveletének beolvasása/művelet | A (z) Machine Learning Services munkaterület |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/szolgáltatások/AK/Prod/pontszám/művelet | A Machine Learning Services munkaterületen |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/szolgáltatások/AK/gyártási/írás | A Machine Learning Services munkaterület (ek) ben létrehoz vagy frissíti a gyártási alrendszeri szolgáltatásokat |
> | DataAction | Microsoft. MachineLearningServices/munkaterületek/szolgáltatások/olvasás | Machine Learning Services munkaterület (ek) szolgáltatásainak beolvasása |
> | Műveletek | Microsoft. MachineLearningServices/munkaterületek/írás | Machine Learning Services munkaterület (ok) létrehozása vagy frissítése |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. ManagedIdentity/identitások/olvasás | Meglévő rendszerhez rendelt identitás beolvasása |
> | Műveletek | Microsoft. ManagedIdentity/Operations/READ | A Microsoft. ManagedIdentity erőforrás-szolgáltatón elérhető műveletek felsorolása |
> | Műveletek | Microsoft. ManagedIdentity/regisztráció/művelet | Regisztrálja az előfizetést a felügyelt identitás erőforrás-szolgáltatója számára |
> | Műveletek | Microsoft. ManagedIdentity/userAssignedIdentities/hozzárendelés/művelet | Meglévő felhasználóhoz rendelt identitás erőforráshoz való hozzárendelésének RBAC művelete |
> | Műveletek | Microsoft. ManagedIdentity/userAssignedIdentities/delete | Meglévő felhasználó által hozzárendelt identitás törlése |
> | Műveletek | Microsoft. ManagedIdentity/userAssignedIdentities/READ | Meglévő felhasználóhoz rendelt identitás beolvasása |
> | Műveletek | Microsoft. ManagedIdentity/userAssignedIdentities/Write | Új felhasználóhoz rendelt identitás létrehozása vagy egy meglévő felhasználóhoz rendelt identitáshoz társított címkék frissítése |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. ManagedServices/marketplaceRegistrationDefinitions/READ | A felügyelt szolgáltatások regisztrációs definícióinak listáját kéri le. |
> | Műveletek | Microsoft. ManagedServices/Operations/READ | A felügyelt szolgáltatások műveleteinek listáját kéri le. |
> | Műveletek | Microsoft. ManagedServices/operationStatuses/READ | Az erőforrás műveleti állapotának beolvasása. |
> | Műveletek | Microsoft. ManagedServices/regisztráció/művelet | Regisztráljon a felügyelt szolgáltatásokra. |
> | Műveletek | Microsoft. ManagedServices/registrationAssignments/delete | Eltávolítja a felügyelt szolgáltatások regisztrációjának hozzárendelését. |
> | Műveletek | Microsoft. ManagedServices/registrationAssignments/READ | A felügyelt szolgáltatások regisztrációs hozzárendeléseinek listáját kéri le. |
> | Műveletek | Microsoft. ManagedServices/registrationAssignments/Write | Felügyelt szolgáltatások regisztrációs hozzárendelésének hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. ManagedServices/registrationDefinitions/delete | Eltávolítja a felügyelt szolgáltatások regisztrációjának definícióját. |
> | Műveletek | Microsoft. ManagedServices/registrationDefinitions/READ | A felügyelt szolgáltatások regisztrációs definícióinak listáját kéri le. |
> | Műveletek | Microsoft. ManagedServices/registrationDefinitions/Write | Felügyelt szolgáltatások regisztrációs definíciójának hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. ManagedServices/regisztráció/művelet | A felügyelt szolgáltatások regisztrációjának törlése. |

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Management/checkNameAvailability/művelet | Ellenőrzi, hogy a felügyeleti csoport megadott neve érvényes-e és egyedi-e. |
> | Műveletek | Microsoft. Management/getEntities/művelet | A hitelesített felhasználó összes entitásának (Management Groups, előfizetések stb.) listázása. |
> | Műveletek | Microsoft. Management/managementGroups/delete | Felügyeleti csoport törlése. |
> | Műveletek | Microsoft. Management/managementGroups/leszármazottak/olvasás | Lekéri egy felügyeleti csoport összes leszármazottját (Management Groups, előfizetését). |
> | Műveletek | Microsoft. Management/managementGroups/olvasás | Felügyeleti csoportok listázása a hitelesített felhasználó számára. |
> | Műveletek | Microsoft. Management/managementGroups/előfizetések/törlés | Az előfizetés társítása a felügyeleti csoportból. |
> | Műveletek | Microsoft. Management/managementGroups/előfizetések/írás | Meglévő előfizetés társítása a felügyeleti csoporttal. |
> | Műveletek | Microsoft. Management/managementGroups/írás | Hozzon létre vagy frissítsen egy felügyeleti csoportot. |
> | Műveletek | Microsoft. Management/regisztráció/művelet | A megadott előfizetés regisztrálása a Microsoft. Management szolgáltatásban |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | DataAction | Microsoft. Maps/fiókok/adatgyűjtés/olvasás | Olvasási hozzáférést biztosít a Maps-fiókhoz. |
> | Műveletek | Microsoft. Maps/fiókok/törlés | Maps-fiók törlése. |
> | Műveletek | Microsoft. Maps/fiókok/eventGridFilters/delete | Event Grid szűrő törlése |
> | Műveletek | Microsoft. Maps/accounts/eventGridFilters/READ | Event Grid szűrő beszerzése |
> | Műveletek | Microsoft. Maps/fiókok/eventGridFilters/írás | Event Grid szűrő létrehozása vagy frissítése |
> | Műveletek | Microsoft. Maps/accounts/Listkeys műveletének beolvasása/Action | Maps-fiók kulcsainak listázása |
> | Műveletek | Microsoft. Maps/fiókok/olvasás | Maps-fiók beszerzése. |
> | Műveletek | Microsoft. Maps/accounts/regenerateKey/Action | Új Maps-fiók elsődleges vagy másodlagos kulcsának létrehozása |
> | Műveletek | Microsoft. Maps/fiókok/írás | Maps-fiók létrehozása vagy frissítése. |
> | Műveletek | Microsoft. Maps/Operations/READ | Szolgáltatói műveletek beolvasása |
> | Műveletek | Microsoft. Maps/regisztráció/művelet | A szolgáltató regisztrálása |

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Marketplace/offerTypes/kiadók/ajánlatok/csomagok/szerződések/olvasás | Egy szerződést ad vissza. |
> | Műveletek | Microsoft. Marketplace/offerTypes/kiadók/ajánlatok/csomagok/szerződések/írás | Elfogad egy aláírt szerződést. |
> | Műveletek | Microsoft. Marketplace/offerTypes/kiadók/ajánlatok/csomagok/konfigurációk/importImage/Action | Rendszerkép importálása a végfelhasználói ACR-be. |
> | Műveletek | Microsoft. Marketplace/offerTypes/kiadók/ajánlatok/csomagok/konfigurációk/olvasás | Egy konfigurációt ad vissza. |
> | Műveletek | Microsoft. Marketplace/offerTypes/kiadók/ajánlatok/csomagok/konfigurációk/írás | Egy konfiguráció mentése. |
> | Műveletek | Microsoft. Marketplace/regisztráció/művelet | Regisztrálja a Microsoft. Marketplace erőforrás-szolgáltatót az előfizetésben. |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. MarketplaceApps/ClassicDevServices/delete | A TÖRLÉSi művelet egy klasszikus fejlesztői szolgáltatási erőforráson van. |
> | Műveletek | Microsoft. MarketplaceApps/ClassicDevServices/listSecrets/művelet | Lekéri a klasszikus fejlesztői szolgáltatások erőforrás-kezelési kulcsait. |
> | Műveletek | Microsoft. MarketplaceApps/ClassicDevServices/listSingleSignOnToken/művelet | Lekéri egy klasszikus fejlesztői szolgáltatás egyszeri bejelentkezési URL-címét. |
> | Műveletek | Microsoft. MarketplaceApps/ClassicDevServices/READ | A GET művelet egy klasszikus fejlesztői szolgáltatáson keresztül történik. |
> | Műveletek | Microsoft. MarketplaceApps/ClassicDevServices/regenerateKey/művelet | Létrehoz egy klasszikus fejlesztői szolgáltatások erőforrás-kezelési kulcsait. |
> | Műveletek | Microsoft. MarketplaceApps/Operations/READ | Olvassa el az összes erőforrástípus műveleteit. |

## <a name="microsoftmarketplaceordering"></a>Microsoft. MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. MarketplaceOrdering/szerződések/ajánlatok/csomagok/megszakítás/művelet | Egy szerződés megszakítása egy adott piactér-elemmel kapcsolatban |
> | Műveletek | Microsoft. MarketplaceOrdering/szerződések/ajánlatok/csomagok/olvasás | Szerződés visszaadása egy adott piactér-elemmel kapcsolatban |
> | Műveletek | Microsoft. MarketplaceOrdering/szerződések/ajánlatok/csomagok/aláírás/művelet | Szerződés aláírása egy adott piactér-elemmel kapcsolatban |
> | Műveletek | Microsoft. MarketplaceOrdering/szerződések/olvasás | Az adott előfizetéshez tartozó összes szerződés visszaküldése |
> | Műveletek | Microsoft. MarketplaceOrdering/offertypes/kiadók/ajánlatok/csomagok/szerződések/olvasás | Szerződés beszerzése egy adott Piactéri virtuálisgép-elemmel kapcsolatban |
> | Műveletek | Microsoft. MarketplaceOrdering/offertypes/kiadók/ajánlatok/csomagok/szerződések/írás | Egy adott Piactéri virtuálisgép-elemmel kapcsolatos szerződés aláírása vagy megszakítása |
> | Műveletek | Microsoft. MarketplaceOrdering/Operations/READ | Az API összes lehetséges műveletének listázása |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Media/checknameavailability/művelet | Ellenőrzi, hogy elérhető-e Media Services fiók neve |
> | Műveletek | Microsoft. Media/Mediaservices/accountfilters/delete | Bármely fiók szűrő törlése |
> | Műveletek | Microsoft. Media/Mediaservices/accountfilters/READ | Bármely fiók szűrő beolvasása |
> | Műveletek | Microsoft. Media/Mediaservices/accountfilters/Write | Bármely fiók szűrő létrehozása vagy frissítése |
> | Műveletek | Microsoft. Media/Mediaservices/assets/assetfilters/delete | Bármely eszköz-szűrő törlése |
> | Műveletek | Microsoft. Media/Mediaservices/assets/assetfilters/READ | Bármely eszköz szűrő beolvasása |
> | Műveletek | Microsoft. Media/Mediaservices/assets/assetfilters/Write | Bármely eszköz-szűrő létrehozása vagy frissítése |
> | Műveletek | Microsoft. Media/Mediaservices/assets/delete | Bármely eszköz törlése |
> | Műveletek | Microsoft. Media/Mediaservices/assets/getEncryptionKey/Action | Eszköz titkosítási kulcsának beolvasása |
> | Műveletek | Microsoft. Media/Mediaservices/assets/listContainerSas/Action | Asset Container SAS URL-címek listázása |
> | Műveletek | Microsoft. Media/Mediaservices/assets/listStreamingLocators/Action | Eszköz streaming-lokátorok listázása |
> | Műveletek | Microsoft. Media/Mediaservices/assets/READ | Bármilyen eszköz beolvasása |
> | Műveletek | Microsoft. Media/Mediaservices/assets/Write | Bármely eszköz létrehozása vagy frissítése |
> | Műveletek | Microsoft. Media/Mediaservices/contentKeyPolicies/delete | A tartalmi kulcsokra vonatkozó szabályzat törlése |
> | Műveletek | Microsoft. Media/Mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/művelet | Szabályzat tulajdonságainak beolvasása titkokkal |
> | Műveletek | Microsoft. Media/Mediaservices/contentKeyPolicies/READ | A tartalmi kulcsokra vonatkozó szabályzat beolvasása |
> | Műveletek | Microsoft. Media/Mediaservices/contentKeyPolicies/Write | A tartalmi kulcsokra vonatkozó szabályzatok létrehozása vagy frissítése |
> | Műveletek | Microsoft. Media/Mediaservices/delete | Bármely Media Services fiók törlése |
> | Műveletek | Microsoft. Media/Mediaservices/eventGridFilters/delete | Bármely Event Grid szűrő törlése |
> | Műveletek | Microsoft. Media/Mediaservices/eventGridFilters/READ | Bármilyen Event Grid szűrő olvasása |
> | Műveletek | Microsoft. Media/Mediaservices/eventGridFilters/Write | Tetszőleges Event Grid szűrő létrehozása vagy frissítése |
> | Műveletek | Microsoft. Media/Mediaservices/liveEventOperations/READ | Bármely élő esemény műveletének beolvasása |
> | Műveletek | Microsoft. Media/Mediaservices/liveEvents/delete | Bármely élő esemény törlése |
> | Műveletek | Microsoft. Media/Mediaservices/liveEvents/liveOutputs/delete | Minden élő kimenet törlése |
> | Műveletek | Microsoft. Media/Mediaservices/liveEvents/liveOutputs/READ | Bármilyen élő kimenet olvasása |
> | Műveletek | Microsoft. Media/Mediaservices/liveEvents/liveOutputs/Write | Bármilyen élő kimenet létrehozása vagy frissítése |
> | Műveletek | Microsoft. Media/Mediaservices/liveEvents/READ | Bármely élő esemény beolvasása |
> | Műveletek | Microsoft. Media/Mediaservices/liveEvents/alaphelyzetbe állítás/művelet | Minden élő esemény műveletének alaphelyzetbe állítása |
> | Műveletek | Microsoft. Media/Mediaservices/liveEvents/Start/művelet | Minden élő esemény művelet elindítása |
> | Műveletek | Microsoft. Media/Mediaservices/liveEvents/leállítás/művelet | Az élő események működésének leállítása |
> | Műveletek | Microsoft. Media/Mediaservices/liveEvents/Write | Élő esemény létrehozása vagy frissítése |
> | Műveletek | Microsoft. Media/Mediaservices/liveOutputOperations/READ | Bármilyen élő kimeneti művelet beolvasása |
> | Műveletek | Microsoft. Media/Mediaservices/READ | Bármely Media Services fiók beolvasása |
> | Műveletek | Microsoft. Media/Mediaservices/streamingEndpointOperations/READ | Bármely streaming Endpoint művelet olvasása |
> | Műveletek | Microsoft. Media/Mediaservices/streamingEndpoints/delete | Bármely streaming végpont törlése |
> | Műveletek | Microsoft. Media/Mediaservices/streamingEndpoints/READ | Bármely folyamatos átviteli végpont olvasása |
> | Műveletek | Microsoft. Media/Mediaservices/streamingEndpoints/Scale/Action | A streaming Endpoint művelet méretezése |
> | Műveletek | Microsoft. Media/Mediaservices/streamingEndpoints/Start/művelet | Bármely streaming Endpoint művelet elindítása |
> | Műveletek | Microsoft. Media/Mediaservices/streamingEndpoints/leállítás/művelet | A streaming Endpoint művelet leállítása |
> | Műveletek | Microsoft. Media/Mediaservices/streamingEndpoints/Write | Adatfolyam-végpont létrehozása vagy frissítése |
> | Műveletek | Microsoft. Media/Mediaservices/streamingLocators/delete | Adatfolyam-kereső törlése |
> | Műveletek | Microsoft. Media/Mediaservices/streamingLocators/listContentKeys/művelet | Tartalmi kulcsok listázása |
> | Műveletek | Microsoft. Media/Mediaservices/streamingLocators/listPaths/művelet | Útvonalak listázása |
> | Műveletek | Microsoft. Media/Mediaservices/streamingLocators/READ | Bármely adatfolyam-kereső olvasása |
> | Műveletek | Microsoft. Media/Mediaservices/streamingLocators/Write | Adatfolyam-kereső létrehozása vagy frissítése |
> | Műveletek | Microsoft. Media/Mediaservices/streamingPolicies/delete | Bármilyen folyamatos átviteli szabályzat törlése |
> | Műveletek | Microsoft. Media/Mediaservices/streamingPolicies/READ | Bármely folyamatos átviteli szabályzat beolvasása |
> | Műveletek | Microsoft. Media/Mediaservices/streamingPolicies/Write | Adatfolyam-szabályzat létrehozása vagy frissítése |
> | Műveletek | Microsoft. Media/Mediaservices/syncStorageKeys/Action | Csatolt Azure Storage-fiókhoz tartozó tárolási kulcsok szinkronizálása |
> | Műveletek | Microsoft. Media/Mediaservices/átalakítás/törlés | Bármely átalakító törlése |
> | Műveletek | Microsoft. Media/Mediaservices/átalakítások/feladatok/cancelJob/művelet | Feladat megszakítása |
> | Műveletek | Microsoft. Media/Mediaservices/átalakítások/feladatok/törlés | Bármilyen feladatot törölhet |
> | Műveletek | Microsoft. Media/Mediaservices/átalakítások/feladatok/olvasás | Bármilyen feladattípus beolvasása |
> | Műveletek | Microsoft. Media/Mediaservices/átalakítások/feladatok/írás | Tetszőleges feladatok létrehozása vagy frissítése |
> | Műveletek | Microsoft. Media/Mediaservices/átalakítás/olvasás | Bármely átalakítás olvasása |
> | Műveletek | Microsoft. Media/Mediaservices/átalakítás/írás | Tetszőleges átalakítás létrehozása vagy frissítése |
> | Műveletek | Microsoft. Media/Mediaservices/Write | Bármely Media Services fiók létrehozása vagy frissítése |
> | Műveletek | Microsoft. Media/Operations/READ | Elérhető műveletek beolvasása |
> | Műveletek | Microsoft. Media/regisztráció/művelet | Regisztrálja az előfizetést a Media Services erőforrás-szolgáltatóhoz, és lehetővé teszi Media Services fiókok létrehozását |
> | Műveletek | Microsoft. Media/regisztráció/művelet | A Media Services erőforrás-szolgáltatóhoz tartozó előfizetés regisztrációjának törlése |

## <a name="microsoftmigrate"></a>Microsoft. Migrálás

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/assessmentOptions/READ | Az adott helyen elérhető értékelési lehetőségek beolvasása |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/értékelések/olvasás | Egy projekten belüli felmérések felsorolása |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/delete | Az értékelési projekt törlése |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/csoportok/értékelések/assessedmachines/olvasás | Egy felmért gép tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/csoportok/értékelések/törlés | Értékelés törlése |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/csoportok/értékelések/downloadurl/művelet | Egy értékelési jelentés URL-címének letöltése |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/csoportok/értékelések/olvasás | Az értékelés tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/csoportok/értékelések/írás | Új Értékelés létrehozása vagy egy meglévő értékelés frissítése |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/csoportok/törlés | Csoport törlése |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/csoportok/olvasás | Csoport tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/groups/updateMachines/Action | Frissítési csoport gépek hozzáadásával vagy eltávolításával |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/csoportok/írás | Új csoport létrehozása vagy meglévő csoport frissítése |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/hypervcollectors/delete | A HyperV-gyűjtő törlése |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/hypervcollectors/READ | A HyperV-gyűjtő tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/hypervcollectors/írás | Új HyperV-gyűjtő létrehozása vagy egy meglévő HyperV-gyűjtő frissítése |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/importcollectors/delete | Az importálási gyűjtő törlése |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/importcollectors/READ | Az importálási gyűjtő tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/importcollectors/írás | Új importálási gyűjtő létrehozása vagy meglévő importálási gyűjtő frissítése |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/gépek/olvasás | Egy gép tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/olvasás | Az értékelési projekt tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/servercollectors/READ | A kiszolgáló-gyűjtő tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/servercollectors/írás | Új kiszolgáló-gyűjtő létrehozása vagy meglévő kiszolgáló-gyűjtő frissítése |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/vmwarecollectors/delete | A VMware-gyűjtő törlése |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/vmwarecollectors/READ | A VMware Collector tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/vmwarecollectors/írás | Létrehoz egy új VMware-gyűjtőt, vagy frissít egy meglévő VMware-gyűjtőt. |
> | Műveletek | Microsoft. Migrálás/assessmentprojects/írás | Új értékelési projekt létrehozása vagy egy meglévő felmérési projekt frissítése |
> | Műveletek | Microsoft. Migrálás/Locations/assessmentOptions/READ | Az adott helyen elérhető értékelési lehetőségek beolvasása |
> | Műveletek | Microsoft. Migrálás/Locations/checknameavailability/Action | Ellenőrzi, hogy az adott előfizetéshez tartozó erőforrás neve elérhető-e a megadott helyen |
> | Műveletek | Microsoft. Migrálás/migrateprojects/DatabaseInstances/READ | Egy adatbázis-példány tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Migrálás/migrateprojects/adatbázisok/olvasás | Egy adatbázis tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Migrálás/migrateprojects/delete | Áttelepítő projekt törlése |
> | Műveletek | Microsoft. Migrálás/migrateprojects/gépek/olvasás | Egy gép tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Migrálás/migrateprojects/MigrateEvents/delete | Egy áttelepítési eseményt töröl |
> | Műveletek | Microsoft. Migrálás/migrateprojects/MigrateEvents/READ | Beolvassa az áttelepítés eseményeinek tulajdonságait. |
> | Műveletek | Microsoft. Migrálás/migrateprojects/olvasás | Az áttelepíteni kívánt projekt tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Migrálás/migrateprojects/RefreshSummary/művelet | Frissíti az áttelepíteni kívánt projekt összegzését |
> | Műveletek | Microsoft. Migrálás/migrateprojects/registerTool/művelet | Eszköz regisztrálása Migrálás projektbe |
> | Műveletek | Microsoft. Migrálás/migrateprojects/Solutions/cleanupData/művelet | A Project-megoldási adatáttelepítés törlése |
> | Műveletek | Microsoft. Migrálás/migrateprojects/megoldások/törlés | Egy Áttelepítő projekt-megoldás törlése |
> | Műveletek | Microsoft. Migrálás/migrateprojects/Solutions/getconfig/művelet | A projekt átmigrálása megoldás konfigurációjának beolvasása |
> | Műveletek | Microsoft. Migrálás/migrateprojects/megoldások/olvasás | Beolvassa a projekt áttelepíthető megoldásának tulajdonságait |
> | Műveletek | Microsoft. Migrálás/migrateprojects/megoldások/írás | Új projekt-Áttelepítő megoldás létrehozása vagy egy meglévő Áttelepítő projekt frissítése |
> | Műveletek | Microsoft. Migrálás/migrateprojects/írás | Új Migrálás projekt létrehozása vagy meglévő Migrálás projekt frissítése |
> | Műveletek | Microsoft. Migrálás/műveletek/olvasás | A Microsoft. erőforrás-szolgáltató áttelepíthető műveleteit sorolja fel. |
> | Műveletek | Microsoft. Migrálás/projektek/értékelések/olvasás | Egy projekten belüli felmérések felsorolása |
> | Műveletek | Microsoft. Migrálás/projektek/törlés | A projekt törlése |
> | Műveletek | Microsoft. Migrálás/projektek/csoportok/értékelések/assessedmachines/olvasás | Egy felmért gép tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Migrálás/projektek/csoportok/értékelések/törlés | Értékelés törlése |
> | Műveletek | Microsoft. Migrálás/projektek/csoportok/értékelések/downloadurl/művelet | Egy értékelési jelentés URL-címének letöltése |
> | Műveletek | Microsoft. Migrálás/projektek/csoportok/értékelések/olvasás | Az értékelés tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Migrálás/projektek/csoportok/értékelések/írás | Új Értékelés létrehozása vagy egy meglévő értékelés frissítése |
> | Műveletek | Microsoft. Migrálás/projektek/csoportok/törlés | Csoport törlése |
> | Műveletek | Microsoft. Migrálás/projektek/csoportok/olvasás | Csoport tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Migrálás/projektek/csoportok/írás | Új csoport létrehozása vagy meglévő csoport frissítése |
> | Műveletek | Microsoft. Migrálás/projektek/kulcsok/művelet | A projekthez tartozó megosztott kulcsok beolvasása |
> | Műveletek | Microsoft. Migrálás/projektek/gépek/olvasás | Egy gép tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Migrálás/projektek/olvasás | Egy projekt tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Migrálás/projektek/írás | Új projekt létrehozása vagy meglévő projekt frissítése |
> | Műveletek | Microsoft. Migrálás/regisztráció/művelet | Előfizetés regisztrálása a Microsoftnál. erőforrás-szolgáltató átmigrálása |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. MixedReality/regisztráció/művelet | Regisztrálja az előfizetést a vegyes valóság erőforrás-szolgáltatóhoz. |
> | DataAction | Microsoft. MixedReality/SpatialAnchorsAccounts/létrehozás/művelet | Térbeli horgonyok létrehozása |
> | DataAction | Microsoft. MixedReality/SpatialAnchorsAccounts/delete | Térbeli horgonyok törlése |
> | DataAction | Microsoft. MixedReality/SpatialAnchorsAccounts/Discovery/READ | Közeli térbeli horgonyok felderítése |
> | DataAction | Microsoft. MixedReality/SpatialAnchorsAccounts/tulajdonságok/olvasás | Térbeli horgonyok tulajdonságainak beolvasása |
> | Műveletek | Microsoft. MixedReality/spatialAnchorsAccounts/Providers/Microsoft. bepillantást/diagnosticSettings/READ | A Microsoft. MixedReality/spatialAnchorsAccounts diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft. MixedReality/spatialAnchorsAccounts/Providers/Microsoft. bepillantások/diagnosticSettings/írás | A Microsoft. MixedReality/spatialAnchorsAccounts diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft. MixedReality/spatialAnchorsAccounts/Providers/Microsoft. bepillantást/metricDefinitions/READ | A Microsoft. MixedReality/spatialAnchorsAccounts elérhető metrikáinak beolvasása |
> | DataAction | Microsoft. MixedReality/SpatialAnchorsAccounts/Query/READ | Térbeli horgonyok keresése |
> | DataAction | Microsoft. MixedReality/SpatialAnchorsAccounts/submitdiag/READ | Diagnosztikai adatainak beküldése az Azure térbeli horgonyok szolgáltatás minőségének javítása érdekében |
> | DataAction | Microsoft. MixedReality/SpatialAnchorsAccounts/Write | Térbeli horgonyok tulajdonságainak frissítése |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. NetApp/Locations/checkfilepathavailability/Action | Ellenőrizze, hogy elérhető-e a fájl elérési útja |
> | Műveletek | Microsoft. NetApp/Locations/checknameavailability/Action | Ellenőrizze, hogy elérhető-e az erőforrás neve |
> | Műveletek | Microsoft. NetApp/Locations/operationresults/READ | Egy műveleti eredmény erőforrásának beolvasása. |
> | Műveletek | Microsoft. NetApp/Locations/READ | Egy rendelkezésre állási ellenőrzési erőforrás beolvasása. |
> | Műveletek | Microsoft. NetApp/netAppAccounts/backupPolicies/delete | Törli a biztonsági mentési házirend erőforrását. |
> | Műveletek | Microsoft. NetApp/netAppAccounts/backupPolicies/READ | A biztonsági mentési házirend erőforrásának beolvasása. |
> | Műveletek | Microsoft. NetApp/netAppAccounts/backupPolicies/írás | A biztonsági mentési házirend erőforrásának írása. |
> | Műveletek | Microsoft. NetApp/netAppAccounts/capacityPools/delete | Egy készlet-erőforrás törlése. |
> | Műveletek | Microsoft. NetApp/netAppAccounts/capacityPools/READ | Egy készlet erőforrásának beolvasása. |
> | Műveletek | Microsoft. NetApp/netAppAccounts/capacityPools/kötetek/biztonsági másolatok/törlés | Törli a biztonsági mentési erőforrást. |
> | Műveletek | Microsoft. NetApp/netAppAccounts/capacityPools/kötetek/biztonsági másolatok/olvasás | Egy biztonsági mentési erőforrás beolvasása. |
> | Műveletek | Microsoft. NetApp/netAppAccounts/capacityPools/kötetek/biztonsági másolatok/írás | Egy biztonsági mentési erőforrást ír. |
> | Műveletek | Microsoft. NetApp/netAppAccounts/capacityPools/kötetek/megszakítás/művelet | Kötet replikációs kapcsolatainak megszakítása |
> | Műveletek | Microsoft. NetApp/netAppAccounts/capacityPools/kötetek/törlés | Kötet-erőforrás törlése. |
> | Műveletek | Microsoft. NetApp/netAppAccounts/capacityPools/Volumes/mountTargets/READ | A csatlakoztatási cél erőforrásának beolvasása. |
> | Műveletek | Microsoft. NetApp/netAppAccounts/capacityPools/kötetek/olvasás | Egy mennyiségi erőforrás beolvasása. |
> | Műveletek | Microsoft. NetApp/netAppAccounts/capacityPools/Volumes/ReplicationStatus/Action | A kötet replikálási állapotának beolvasása. |
> | Műveletek | Microsoft. NetApp/netAppAccounts/capacityPools/kötetek/visszaállítás/művelet | Kötet visszaállítása adott pillanatképre |
> | Műveletek | Microsoft. NetApp/netAppAccounts/capacityPools/kötetek/Pillanatképek/törlés | Egy pillanatkép-erőforrás törlése. |
> | Műveletek | Microsoft. NetApp/netAppAccounts/capacityPools/kötetek/Pillanatképek/olvasás | Pillanatkép-erőforrás beolvasása. |
> | Műveletek | Microsoft. NetApp/netAppAccounts/capacityPools/kötetek/Pillanatképek/írás | Egy pillanatkép-erőforrást ír. |
> | Műveletek | Microsoft. NetApp/netAppAccounts/capacityPools/kötetek/írás | Mennyiségi erőforrást ír. |
> | Műveletek | Microsoft. NetApp/netAppAccounts/capacityPools/írás | Egy készlet erőforrást ír. |
> | Műveletek | Microsoft. NetApp/netAppAccounts/delete | Egy fiók erőforrásának törlése. |
> | Műveletek | Microsoft. NetApp/netAppAccounts/READ | Egy fiók erőforrásának beolvasása. |
> | Műveletek | Microsoft. NetApp/netAppAccounts/tárolók/olvasás | A tár erőforrásának beolvasása. |
> | Műveletek | Microsoft. NetApp/netAppAccounts/írás | Egy fiók erőforrásának írása. |
> | Műveletek | Microsoft. NetApp/Operations/READ | Művelet-erőforrások beolvasása. |
> | Műveletek | Microsoft. NetApp/regisztráció/művelet | Előfizetés regisztrálása a Microsoft. NetApp erőforrás-szolgáltatónál |
> | Műveletek | Microsoft. NetApp/regisztráció/művelet | Előfizetés regisztrációjának törlése a Microsoft. NetApp erőforrás-szolgáltatóval |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Network/applicationGatewayAvailableRequestHeaders/READ | Application Gateway elérhető kérelmek fejlécének beolvasása |
> | Műveletek | Microsoft. Network/applicationGatewayAvailableResponseHeaders/READ | Application Gateway elérhető válasz fejlécének beolvasása |
> | Műveletek | Microsoft. Network/applicationGatewayAvailableServerVariables/READ | Application Gateway elérhető kiszolgálói változók beolvasása |
> | Műveletek | Microsoft. Network/applicationGatewayAvailableSslOptions/predefinedPolicies/READ | Application Gateway SSL-re előre definiált házirend |
> | Műveletek | Microsoft. Network/applicationGatewayAvailableSslOptions/READ | Application Gateway elérhető SSL-beállítások |
> | Műveletek | Microsoft. Network/applicationGatewayAvailableWafRuleSets/READ | Beolvas Application Gateway elérhető WAF-szabálykészlet |
> | Műveletek | Microsoft. Network/applicationGateways/backendAddressPools/JOIN/Action | Egy Application Gateway háttérbeli címkészlet illesztése. Nem Riasztásos. |
> | Műveletek | Microsoft. Network/applicationGateways/backendhealth/Action | Egy Application Gateway-háttér állapotának beolvasása |
> | Műveletek | Microsoft. Network/applicationGateways/delete | Application Gateway törlése |
> | Műveletek | Microsoft. Network/applicationGateways/getBackendHealthOnDemand/Action | Az Application Gateway-háttér állapotának beolvasása igény szerint az adott http-beállítás és a háttérrendszer-készlet esetében |
> | Műveletek | Microsoft. Network/applicationGateways/READ | Egy Application Gateway beolvasása |
> | Műveletek | Microsoft. Network/applicationGateways/Start/művelet | Egy Application Gateway elindítása |
> | Műveletek | Microsoft. Network/applicationGateways/leállítás/művelet | Egy Application Gateway leállítása |
> | Műveletek | Microsoft. Network/applicationGateways/Write | Application Gateway létrehozása vagy egy Application Gateway frissítése |
> | Műveletek | Microsoft. Network/ApplicationGatewayWebApplicationFirewallPolicies/delete | Application Gateway WAF szabályzat törlése |
> | Műveletek | Microsoft. Network/ApplicationGatewayWebApplicationFirewallPolicies/READ | Application Gateway WAF szabályzat beolvasása |
> | Műveletek | Microsoft. Network/ApplicationGatewayWebApplicationFirewallPolicies/Write | Létrehoz egy Application Gateway WAF szabályzatot, vagy frissít egy Application Gateway WAF szabályzatot |
> | Műveletek | Microsoft. Network/applicationSecurityGroups/delete | Alkalmazás biztonsági csoportjának törlése |
> | Műveletek | Microsoft. Network/applicationSecurityGroups/joinIpConfiguration/Action | Összekapcsolja az IP-konfigurációt az alkalmazás biztonsági csoportjaival. Nem riasztásos. |
> | Műveletek | Microsoft. Network/applicationSecurityGroups/joinNetworkSecurityRule/Action | Egy biztonsági szabály csatlakoztatása az alkalmazás biztonsági csoportjaihoz. Nem riasztásos. |
> | Műveletek | Microsoft. Network/applicationSecurityGroups/listIpConfigurations/Action | A új választható applicationsecuritygroup lévő IP-konfigurációk felsorolása |
> | Műveletek | Microsoft. Network/applicationSecurityGroups/READ | Egy alkalmazás biztonsági csoportjának AZONOSÍTÓját kapja meg. |
> | Műveletek | Microsoft. Network/applicationSecurityGroups/Write | Létrehoz egy alkalmazás biztonsági csoportot, vagy frissít egy meglévő alkalmazás biztonsági csoportot. |
> | Műveletek | Microsoft. Network/azureFirewallFqdnTags/READ | Azure Firewall FQDN-címkék beolvasása |
> | Műveletek | Microsoft. Network/azurefirewalls/delete | Azure Firewall törlése |
> | Műveletek | Microsoft. Network/azurefirewalls/READ | Azure Firewall beolvasása |
> | Műveletek | Microsoft. Network/azurefirewalls/Write | Létrehoz vagy frissít egy Azure Firewall |
> | Műveletek | Microsoft. Network/bastionHosts/createbsl/Action | Megosztható URL-címeket hoz létre a virtuális gépek számára, és visszaadja az URL-címeket |
> | Műveletek | Microsoft. Network/bastionHosts/delete | Megerősített gazdagép törlése |
> | Műveletek | Microsoft. Network/bastionHosts/deletebsl/Action | Törli a megosztható URL-címeket a megadott virtuális gépek számára a megerősített |
> | Műveletek | Microsoft. Network/bastionHosts/disconnectactivesessions/Action | Adott aktív munkamenetek leválasztása a megerősített gazdagépen |
> | Műveletek | Microsoft. Network/bastionHosts/getactivesessions/Action | Aktív munkamenetek beolvasása a megerősített gazdagépen |
> | Műveletek | Microsoft. Network/bastionHosts/getbsl/Action | A megadott virtuális gépek megosztható URL-címeinek visszaadása egy megerősített alhálózatban, ha az URL-címek létrejöttek. |
> | Műveletek | Microsoft. Network/bastionHosts/READ | Megerősített gazdagép beolvasása |
> | Műveletek | Microsoft. Network/bastionHosts/Write | Megerősített gazdagép létrehozása vagy frissítése |
> | Műveletek | Microsoft. Network/bgpServiceCommunities/READ | BGP szolgáltatás-Közösségek beszerzése |
> | Műveletek | Microsoft. Network/checkFrontDoorNameAvailability/Action | Ellenőrzi, hogy elérhető-e a bejárati ajtó neve |
> | Műveletek | Microsoft. Network/checkTrafficManagerNameAvailability/Action | Ellenőrzi egy Traffic Manager relatív DNS-név rendelkezésre állását. |
> | Műveletek | Microsoft. Network/Connections/delete | VirtualNetworkGatewayConnection törlése |
> | Műveletek | Microsoft. Network/Connections/READ | VirtualNetworkGatewayConnection beolvasása |
> | Műveletek | Microsoft. Network/Connections/visszavonás/művelet | Express Route-kapcsolatok állapotának megjelölése visszavont értékként |
> | Műveletek | Microsoft. Network/Connections/sharedkey/Action | VirtualNetworkGatewayConnection-SharedKey beolvasása |
> | Műveletek | Microsoft. Network/Connections/sharedKey/READ | VirtualNetworkGatewayConnection SharedKey beolvasása |
> | Műveletek | Microsoft. Network/Connections/sharedKey/Write | Meglévő VirtualNetworkGatewayConnection-SharedKey létrehozása vagy frissítése |
> | Műveletek | Microsoft. Network/Connections/startpacketcapture/Action | Elindítja a Virtual Network átjáró kapcsolati csomagjainak rögzítését. |
> | Műveletek | Microsoft. Network/Connections/stoppacketcapture/Action | Leállítja egy Virtual Network átjáró kapcsolati csomagjainak rögzítését. |
> | Műveletek | Microsoft. Network/Connections/vpndeviceconfigurationscript/Action | A VirtualNetworkGatewayConnection VPN-eszköz konfigurációjának beolvasása |
> | Műveletek | Microsoft. Network/Connections/Write | Meglévő VirtualNetworkGatewayConnection létrehozása vagy frissítése |
> | Műveletek | Microsoft. Network/ddosCustomPolicies/delete | A DDoS-re szabott szabályzat törlése |
> | Műveletek | Microsoft. Network/ddosCustomPolicies/READ | A DDoS-szabályzatok testreszabott definíciójának beolvasása |
> | Műveletek | Microsoft. Network/ddosCustomPolicies/Write | Létrehoz egy DDoS testreszabott szabályzatot, vagy frissít egy meglévő DDoS testreszabott szabályzatot |
> | Műveletek | Microsoft. Network/ddosProtectionPlans/delete | DDoS Protection csomag törlése |
> | Műveletek | Microsoft. Network/ddosProtectionPlans/csatlakozás/művelet | Egy DDoS Protection-csomaghoz csatlakozik. Nem riasztásos. |
> | Műveletek | Microsoft. Network/ddosProtectionPlans/READ | DDoS Protection csomag beolvasása |
> | Műveletek | Microsoft. Network/ddosProtectionPlans/Write | Létrehoz egy DDoS Protection csomagot vagy frissíti a DDoS Protection tervet  |
> | Műveletek | Microsoft. Network/dnsoperationresults/READ | DNS-művelet eredményeinek beolvasása |
> | Műveletek | Microsoft. Network/dnsoperationstatuses/READ | DNS-művelet állapotának beolvasása  |
> | Műveletek | Microsoft. Network/dnszones/A/Delete | Távolítsa el egy adott név rekordját, és írja be a "A" értéket egy DNS-zónából. |
> | Műveletek | Microsoft. Network/dnszones/A/READ | Adja meg a "A" típusú rekordot JSON formátumban. A rekordhalmaz a rekordok listáját, valamint az ÉLETTARTAMot, a címkéket és a ETAG tartalmazza. |
> | Műveletek | Microsoft. Network/dnszones/A/Write | Egy "A" típusú rekordhalmaz létrehozása vagy frissítése egy DNS-zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Műveletek | Microsoft. Network/dnszones/AAAA/delete | Távolítsa el egy adott név rekordját, és írja be az "AAAA" értéket egy DNS-zónából. |
> | Műveletek | Microsoft. Network/dnszones/AAAA/olvasás | Adja meg az "AAAA" típusú rekordot JSON formátumban. A rekordhalmaz a rekordok listáját, valamint az ÉLETTARTAMot, a címkéket és a ETAG tartalmazza. |
> | Műveletek | Microsoft. Network/dnszones/AAAA/írás | Hozzon létre vagy frissítsen egy "AAAA" típusú rekordhalmazt egy DNS-zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Műveletek | Microsoft. Network/dnszones/mind/olvasás | DNS-rekordhalmazok beolvasása típusok között |
> | Műveletek | Microsoft. Network/dnszones/CAA/delete | Távolítsa el egy adott név rekordját, és írja be a "CAA" típust egy DNS-zónából. |
> | Műveletek | Microsoft. Network/dnszones/CAA/READ | Adja meg a "CAA" típusú rekordot JSON formátumban. A rekordazonosító tartalmazza az ÉLETTARTAMot, a címkéket és a ETAG. |
> | Műveletek | Microsoft. Network/dnszones/CAA/Write | Hozzon létre vagy frissítsen egy "CAA" típusú rekordhalmazt egy DNS-zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Műveletek | Microsoft. Network/dnszones/CNAME/delete | Távolítsa el egy adott név rekordját, és írja be a "CNAME" típust egy DNS-zónából. |
> | Műveletek | Microsoft. Network/dnszones/CNAME/READ | Adja meg a "CNAME" típusú rekordot JSON formátumban. A rekordazonosító tartalmazza az ÉLETTARTAMot, a címkéket és a ETAG. |
> | Műveletek | Microsoft. Network/dnszones/CNAME/Write | CNAME típusú rekordhalmaz létrehozása vagy frissítése egy DNS-zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Műveletek | Microsoft. Network/dnszones/delete | Törölje a DNS-zónát JSON formátumban. A zóna tulajdonságai közé tartoznak a címkék, a ETAG, a numberOfRecordSets és a maxNumberOfRecordSets. |
> | Műveletek | Microsoft. Network/dnszones/MX/delete | Távolítsa el egy adott név rekordját, és írja be az "MX" típust egy DNS-zónából. |
> | Műveletek | Microsoft. Network/dnszones/MX/READ | Adja meg a "MX" típusú rekordot JSON formátumban. A rekordhalmaz a rekordok listáját, valamint az ÉLETTARTAMot, a címkéket és a ETAG tartalmazza. |
> | Műveletek | Microsoft. Network/dnszones/MX/Write | Hozzon létre vagy frissítsen egy MX típusú rekordhalmazt egy DNS-zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Műveletek | Microsoft. Network/dnszones/NS/delete | Az NS típusú DNS-rekord törlése |
> | Műveletek | Microsoft. Network/dnszones/NS/READ | Az NS típusú DNS-rekordtípus beolvasása |
> | Műveletek | Microsoft. Network/dnszones/NS/Write | NS típusú DNS-rekord létrehozása vagy frissítése |
> | Műveletek | Microsoft. Network/dnszones/PTR/delete | Távolítsa el egy adott név rekordját, és írja be a "PTR" értéket egy DNS-zónából. |
> | Műveletek | Microsoft. Network/dnszones/PTR/READ | Adja meg a "PTR" típusú rekordot JSON formátumban. A rekordhalmaz a rekordok listáját, valamint az ÉLETTARTAMot, a címkéket és a ETAG tartalmazza. |
> | Műveletek | Microsoft. Network/dnszones/PTR/Write | Hozzon létre vagy frissítsen egy PTR típusú rekordhalmazt egy DNS-zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Műveletek | Microsoft. Network/dnszones/READ | Kérje le a DNS-zónát JSON formátumban. A zóna tulajdonságai közé tartoznak a címkék, a ETAG, a numberOfRecordSets és a maxNumberOfRecordSets. Vegye figyelembe, hogy a parancs nem kéri le a zónán belül található rekordhalmazokat. |
> | Műveletek | Microsoft. Network/dnszones/rekordhalmazok/olvasás | DNS-rekordhalmazok beolvasása típusok között |
> | Műveletek | Microsoft. Network/dnszones/SOA/READ | SOA típusú DNS-rekordtípus beolvasása |
> | Műveletek | Microsoft. Network/dnszones/SOA/Write | SOA típusú DNS-rekord létrehozása vagy frissítése |
> | Műveletek | Microsoft. Network/dnszones/SRV/delete | Távolítsa el egy adott név rekordját, és írja be a "SRV" értéket egy DNS-zónából. |
> | Műveletek | Microsoft. Network/dnszones/SRV/READ | A "SRV" típusú rekordtípusok beolvasása JSON formátumban. A rekordhalmaz a rekordok listáját, valamint az ÉLETTARTAMot, a címkéket és a ETAG tartalmazza. |
> | Műveletek | Microsoft. Network/dnszones/SRV/Write | SRV típusú rekordtípusok létrehozása vagy frissítése |
> | Műveletek | Microsoft. Network/dnszones/TXT/delete | Távolítsa el egy adott név bejegyzéstípusát, és írja be a "TXT" típust egy DNS-zónából. |
> | Műveletek | Microsoft. Network/dnszones/TXT/READ | Hozzon létre egy "TXT" típusú rekordot JSON formátumban. A rekordhalmaz a rekordok listáját, valamint az ÉLETTARTAMot, a címkéket és a ETAG tartalmazza. |
> | Műveletek | Microsoft. Network/dnszones/TXT/Write | Hozzon létre vagy frissítsen egy "TXT" típusú rekordhalmazt egy DNS-zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Műveletek | Microsoft. Network/dnszones/Write | Hozzon létre vagy frissítsen egy DNS-zónát az erőforráscsoporthoz.  A DNS-zóna erőforrásaihoz tartozó címkék frissítésére szolgál. Vegye figyelembe, hogy ez a parancs nem használható rekordhalmazok létrehozásához vagy frissítéséhez a zónán belül. |
> | Műveletek | Microsoft. Network/expressRouteCircuits/engedélyek/törlés | ExpressRouteCircuit-engedély törlése |
> | Műveletek | Microsoft. Network/expressRouteCircuits/engedélyek/olvasás | ExpressRouteCircuit-hitelesítés beolvasása |
> | Műveletek | Microsoft. Network/expressRouteCircuits/engedélyek/írás | Meglévő ExpressRouteCircuit-hitelesítés létrehozása vagy frissítése |
> | Műveletek | Microsoft. Network/expressRouteCircuits/delete | ExpressRouteCircuit törlése |
> | Műveletek | Microsoft. Network/expressRouteCircuits/csatlakozás/művelet | Egy Express Route-áramkörhöz csatlakozik. Nem riasztásos. |
> | Műveletek | Microsoft. Network/expressRouteCircuits/peers/arpTables/READ | Beolvas egy ExpressRouteCircuit-társat |
> | Műveletek | Microsoft. Network/expressRouteCircuits/társak/kapcsolatok/törlés | ExpressRouteCircuit-kapcsolatok törlése |
> | Műveletek | Microsoft. Network/expressRouteCircuits/társak/kapcsolatok/olvasás | ExpressRouteCircuit-kapcsolatok beolvasása |
> | Műveletek | Microsoft. Network/expressRouteCircuits/társak/kapcsolatok/írás | Meglévő ExpressRouteCircuit-kapcsolatok erőforrásának létrehozása vagy frissítése |
> | Műveletek | Microsoft. Network/expressRouteCircuits/társak/törlés | ExpressRouteCircuit-társítás törlése |
> | Műveletek | Microsoft. Network/expressRouteCircuits/peers/peerConnections/READ | Peer Express Route Circuit-kapcsolat beolvasása |
> | Műveletek | Microsoft. Network/expressRouteCircuits/társak/olvasás | ExpressRouteCircuit-társítás beolvasása |
> | Műveletek | Microsoft. Network/expressRouteCircuits/peers/routeTables/READ | ExpressRouteCircuit-társítási útvonaltábla beolvasása |
> | Műveletek | Microsoft. Network/expressRouteCircuits/peers/routeTablesSummary/READ | ExpressRouteCircuit-társítási útvonaltábla összegzésének beolvasása |
> | Műveletek | Microsoft. Network/expressRouteCircuits/társak/statisztika/olvasás | ExpressRouteCircuit-társítási stat beolvasása |
> | Műveletek | Microsoft. Network/expressRouteCircuits/társak/írás | Meglévő ExpressRouteCircuit-társítás létrehozása vagy frissítése |
> | Műveletek | Microsoft. Network/expressRouteCircuits/READ | ExpressRouteCircuit beszerzése |
> | Műveletek | Microsoft. Network/expressRouteCircuits/stats/READ | ExpressRouteCircuit-stat beolvasása |
> | Műveletek | Microsoft. Network/expressRouteCircuits/Write | Meglévő ExpressRouteCircuit létrehozása vagy frissítése |
> | Műveletek | Microsoft. Network/expressRouteCrossConnections/csatlakozás/művelet | Egy expressz útvonalon keresztüli összekötő csatlakoztatása. Nem riasztásos. |
> | Műveletek | Microsoft. Network/expressRouteCrossConnections/peers/arpTables/READ | Expressz útvonal közötti kapcsolatok összekapcsolásának ARP-táblájának beolvasása |
> | Műveletek | Microsoft. Network/expressRouteCrossConnections/társak/törlés | Expressz útvonalon keresztüli kapcsolatok társításának törlése |
> | Műveletek | Microsoft. Network/expressRouteCrossConnections/társak/olvasás | Expressz útvonal közötti kapcsolatok társításának beolvasása |
> | Műveletek | Microsoft. Network/expressRouteCrossConnections/peers/routeTables/READ | Expressz útvonal közötti kapcsolatok összekapcsolási útválasztási táblázatának beolvasása |
> | Műveletek | Microsoft. Network/expressRouteCrossConnections/peers/routeTableSummary/READ | Expressz útvonal keresztben való összekapcsolási útválasztási táblázatának összefoglalása |
> | Műveletek | Microsoft. Network/expressRouteCrossConnections/társak/írás | Létrehoz egy expressz útvonalon keresztüli kapcsolatok összekapcsolását, vagy frissíti a meglévő Express Route-kapcsolatok közötti kapcsolatot |
> | Műveletek | Microsoft. Network/expressRouteCrossConnections/READ | Expressz útvonalon keresztüli kapcsolatok lekérdezése |
> | Műveletek | Microsoft. Network/expressRouteGateways/expressRouteConnections/delete | Express Route-kapcsolatok törlése |
> | Műveletek | Microsoft. Network/expressRouteGateways/expressRouteConnections/READ | Express Route-kapcsolatok beolvasása |
> | Műveletek | Microsoft. Network/expressRouteGateways/expressRouteConnections/Write | Express Route-kapcsolatok létrehozása vagy egy meglévő Express Route-kapcsolatok frissítése |
> | Műveletek | Microsoft. Network/expressRouteGateways/csatlakozás/művelet | Egy Express Route-átjáróhoz csatlakozik. Nem riasztásos. |
> | Műveletek | Microsoft. Network/expressRouteGateways/READ | Express Route-átjáró beolvasása |
> | Műveletek | Microsoft. Network/expressRoutePorts/delete | ExpressRoutePorts törlése |
> | Műveletek | Microsoft. Network/expressRoutePorts/csatlakozás/művelet | Az Express Route-portok illesztése. Nem riasztásos. |
> | Műveletek | Microsoft. Network/expressRoutePorts/hivatkozások/olvasás | ExpressRouteLink beolvasása |
> | Műveletek | Microsoft. Network/expressRoutePorts/READ | ExpressRoutePorts beolvasása |
> | Műveletek | Microsoft. Network/expressRoutePorts/Write | ExpressRoutePorts létrehozása vagy frissítése |
> | Műveletek | Microsoft. Network/expressRoutePortsLocations/READ | Express Route-portok helyeinek beolvasása |
> | Műveletek | Microsoft. Network/expressRouteServiceProviders/READ | Expressz Route Service szolgáltatók beolvasása |
> | Műveletek | Microsoft. Network/firewallPolicies/delete | Tűzfal-házirend törlése |
> | Műveletek | Microsoft. Network/firewallPolicies/csatlakozás/művelet | Csatlakozik egy tűzfal-házirendhez. Nem riasztásos. |
> | Műveletek | Microsoft. Network/firewallPolicies/READ | Tűzfal-házirend beolvasása |
> | Műveletek | Microsoft. Network/firewallPolicies/ruleGroups/delete | Egy tűzfalszabály-szabálytípus törlése |
> | Műveletek | Microsoft. Network/firewallPolicies/ruleGroups/READ | Tűzfal házirend-szabály csoportjának beolvasása |
> | Műveletek | Microsoft. Network/firewallPolicies/ruleGroups/Write | Létrehoz egy tűzfalszabály-szabályt, vagy frissíti egy meglévő tűzfalszabály-szabály csoportját. |
> | Műveletek | Microsoft. Network/firewallPolicies/Write | Létrehoz egy tűzfal-házirendet, vagy frissíti a meglévő tűzfal-házirendet. |
> | Műveletek | Microsoft. Network/frontDoors/backendPools/delete | Háttér-készlet törlése |
> | Műveletek | Microsoft. Network/frontDoors/backendPools/READ | Háttér-készlet beolvasása |
> | Műveletek | Microsoft. Network/frontDoors/backendPools/Write | Háttér-készlet létrehozása vagy frissítése |
> | Műveletek | Microsoft. Network/frontDoors/delete | Bejárati ajtó törlése |
> | Műveletek | Microsoft. Network/frontDoors/frontendEndpoints/delete | Előtér-végpont törlése |
> | Műveletek | Microsoft. Network/frontDoors/frontendEndpoints/disableHttps/művelet | A HTTPS letiltása egy előtér-végponton |
> | Műveletek | Microsoft. Network/frontDoors/frontendEndpoints/enableHttps/művelet | HTTPS engedélyezése előtér-végponton |
> | Műveletek | Microsoft. Network/frontDoors/frontendEndpoints/READ | Előtér-végpont beolvasása |
> | Műveletek | Microsoft. Network/frontDoors/frontendEndpoints/Write | Előtér-végpont létrehozása vagy frissítése |
> | Műveletek | Microsoft. Network/frontDoors/healthProbeSettings/delete | Állapot-mintavételi beállítások törlése |
> | Műveletek | Microsoft. Network/frontDoors/healthProbeSettings/READ | Állapot mintavételi beállításainak beolvasása |
> | Műveletek | Microsoft. Network/frontDoors/healthProbeSettings/Write | Állapot mintavételi beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft. Network/frontDoors/loadBalancingSettings/delete | Terheléselosztási beállítások létrehozása vagy frissítése |
> | Műveletek | Microsoft. Network/frontDoors/loadBalancingSettings/READ | Terheléselosztási beállítások beolvasása |
> | Műveletek | Microsoft. Network/frontDoors/loadBalancingSettings/Write | Terheléselosztási beállítások létrehozása vagy frissítése |
> | Műveletek | Microsoft. Network/frontDoors/Purge/művelet | Gyorsítótárazott tartalom kiürítése egy bejárati ajtóról |
> | Műveletek | Microsoft. Network/frontDoors/READ | Bejárati ajtó beolvasása |
> | Műveletek | Microsoft. Network/frontDoors/routingRules/delete | Útválasztási szabály törlése |
> | Műveletek | Microsoft. Network/frontDoors/routingRules/READ | Útválasztási szabály beolvasása |
> | Műveletek | Microsoft. Network/frontDoors/routingRules/Write | Útválasztási szabály létrehozása vagy frissítése |
> | Műveletek | Microsoft. Network/frontDoors/validateCustomDomain/Action | Előtérbeli végpont ellenőrzése egy bejárati ajtónál |
> | Műveletek | Microsoft. Network/frontDoors/Write | Bejárati ajtó létrehozása vagy frissítése |
> | Műveletek | Microsoft. Network/frontDoorWebApplicationFirewallManagedRuleSets/READ | Webalkalmazási tűzfal által felügyelt szabálykészlet beolvasása |
> | Műveletek | Microsoft. Network/frontDoorWebApplicationFirewallPolicies/delete | Egy webalkalmazási tűzfal házirend törlése |
> | Műveletek | Microsoft. Network/frontDoorWebApplicationFirewallPolicies/csatlakozás/művelet | Csatlakozik egy webalkalmazási tűzfal házirendjéhez. Nem Riasztásos. |
> | Műveletek | Microsoft. Network/frontDoorWebApplicationFirewallPolicies/READ | Webalkalmazási tűzfal házirend beolvasása |
> | Műveletek | Microsoft. Network/frontDoorWebApplicationFirewallPolicies/Write | Webalkalmazási tűzfal házirendjének létrehozása vagy frissítése |
> | Műveletek | Microsoft. Network/ipGroups/delete | IpGroup törlése |
> | Műveletek | Microsoft. Network/ipGroups/csatlakozás/művelet | Egy IpGroup illesztése. Nem riasztásos. |
> | Műveletek | Microsoft. Network/ipGroups/READ | IpGroup beolvasása |
> | Műveletek | Microsoft. Network/ipGroups/Write | IpGroup létrehozása vagy meglévő IpGroups frissítése |
> | Műveletek | Microsoft. Network/loadBalancers/backendAddressPools/JOIN/Action | A terheléselosztó háttérbeli címkészlet csatlakoztatása. Nem Riasztásos. |
> | Műveletek | Microsoft. Network/loadBalancers/backendAddressPools/READ | Terheléselosztó háttérbeli címkészlet-definíciójának beolvasása |
> | Műveletek | Microsoft. Network/loadBalancers/delete | Terheléselosztó törlése |
> | Műveletek | Microsoft. Network/loadBalancers/frontendIPConfigurations/JOIN/Action | Egy Load Balancer előtér-IP-konfigurációhoz csatlakozik. Nem riasztásos. |
> | Műveletek | Microsoft. Network/loadBalancers/frontendIPConfigurations/READ | Beolvassa a terheléselosztó előtérbeli IP-konfigurációjának definícióját |
> | Műveletek | Microsoft. Network/loadBalancers/inboundNatPools/JOIN/Action | A terheléselosztó bejövő NAT-készletéhez csatlakozik. Nem riasztásos. |
> | Műveletek | Microsoft. Network/loadBalancers/inboundNatPools/READ | Beolvassa a terheléselosztó bejövő NAT-készletének definícióját |
> | Műveletek | Microsoft. Network/loadBalancers/inboundNatRules/delete | A terheléselosztó bejövő NAT-szabályának törlése |
> | Műveletek | Microsoft. Network/loadBalancers/inboundNatRules/JOIN/Action | A terheléselosztó bejövő NAT-szabályához csatlakozik. Nem Riasztásos. |
> | Műveletek | Microsoft. Network/loadBalancers/inboundNatRules/READ | Beolvassa a terheléselosztó bejövő NAT-szabályának definícióját |
> | Műveletek | Microsoft. Network/loadBalancers/inboundNatRules/Write | Létrehoz egy terheléselosztó bejövő NAT-szabályát, vagy frissíti a meglévő terheléselosztó bejövő NAT-szabályát |
> | Műveletek | Microsoft. Network/loadBalancers/loadBalancingRules/READ | Terheléselosztó terheléselosztási szabály definíciójának beolvasása |
> | Műveletek | Microsoft. Network/loadBalancers/networkInterfaces/READ | A terheléselosztó alatt lévő összes hálózati adapterre mutató hivatkozásokat kap |
> | Műveletek | Microsoft. Network/loadBalancers/outboundRules/READ | A terheléselosztó kimenő szabályának definíciójának beolvasása |
> | Műveletek | Microsoft. Network/loadBalancers/Szondák/csatlakozás/művelet | Lehetővé teszi a terheléselosztó mintavételi funkcióinak használatát. A virtuálisgép-méretezési csoport ezen engedély healthProbe tulajdonsága például hivatkozhat a mintavételre. Nem riasztásos. |
> | Műveletek | Microsoft. Network/loadBalancers/Szondák/olvasás | Terheléselosztó mintavételének beolvasása |
> | Műveletek | Microsoft. Network/loadBalancers/READ | Terheléselosztó definíciójának beolvasása |
> | Műveletek | Microsoft. Network/loadBalancers/virtualMachines/READ | A terheléselosztó alatt lévő összes virtuális gépre mutató hivatkozásokat kap |
> | Műveletek | Microsoft. Network/loadBalancers/Write | Terheléselosztó létrehozása vagy egy meglévő terheléselosztó frissítése |
> | Műveletek | Microsoft. Network/localnetworkgateways/delete | LocalNetworkGateway törlése |
> | Műveletek | Microsoft. Network/localnetworkgateways/READ | LocalNetworkGateway beolvasása |
> | Műveletek | Microsoft. Network/localnetworkgateways/Write | Meglévő LocalNetworkGateway létrehozása vagy frissítése |
> | Műveletek | Microsoft. Network/Locations/autoApprovedPrivateLinkServices/READ | Automatikusan jóváhagyott privát kapcsolati szolgáltatások beolvasása |
> | Műveletek | Microsoft. Network/Locations/availableDelegations/READ | Elérhető delegálások beolvasása |
> | Műveletek | Microsoft. Network/Locations/availablePrivateEndpointTypes/READ | Elérhető privát végponti erőforrások beolvasása |
> | Műveletek | Microsoft. Network/Locations/availableServiceAliases/READ | Elérhető szolgáltatás-aliasok beolvasása |
> | Műveletek | Microsoft. Network/Locations/bareMetalTenants/Action | Operációs rendszer nélküli bérlő kiosztása vagy ellenőrzése |
> | Műveletek | Microsoft. Network/Locations/checkAcceleratedNetworkingSupport/Action | A gyorsított hálózatkezelés támogatásának ellenőrzése |
> | Műveletek | Microsoft. Network/Locations/checkDnsNameAvailability/READ | Ellenőrzi, hogy a DNS-címke elérhető-e a megadott helyen |
> | Műveletek | Microsoft. Network/Locations/checkPrivateLinkServiceVisibility/Action | A privát hivatkozás szolgáltatás láthatóságának ellenőrzése |
> | Műveletek | Microsoft. Network/Locations/operationResults/READ | Aszinkron POST-vagy TÖRLÉSi művelet működési eredményének beolvasása |
> | Műveletek | Microsoft. Network/Locations/Operations/READ | Egy aszinkron művelet állapotát jelképező műveleti erőforrás beolvasása |
> | Műveletek | Microsoft. Network/Locations/serviceTags/READ | Szolgáltatás-címkék beolvasása |
> | Műveletek | Microsoft. Network/Locations/supportedVirtualMachineSizes/READ | A virtuális gépek támogatott méretének beolvasása |
> | Műveletek | Microsoft. Network/Locations/használat/olvasás | Az erőforrások használati metrikáinak beolvasása |
> | Műveletek | Microsoft. Network/Locations/virtualNetworkAvailableEndpointServices/READ | Lekéri az elérhető Virtual Network Endpoint Services listáját |
> | Műveletek | Microsoft. Network/networkIntentPolicies/delete | Hálózati leképezési házirend törlése |
> | Műveletek | Microsoft. Network/networkIntentPolicies/READ | Hálózati leképezési szabályzat lekérése – Leírás |
> | Műveletek | Microsoft. Network/networkIntentPolicies/Write | Létrehoz egy hálózati leképezési házirendet, vagy frissíti a meglévő hálózati leképezési házirendet. |
> | Műveletek | Microsoft. Network/networkInterfaces/delete | Hálózati adapter törlése |
> | Műveletek | Microsoft. Network/networkInterfaces/effectiveNetworkSecurityGroups/Action | A virtuális gép hálózati adapterén konfigurált hálózati biztonsági csoportok beolvasása |
> | Műveletek | Microsoft. Network/networkInterfaces/effectiveRouteTable/Action | A virtuális gép hálózati adapterén konfigurált útválasztási táblázat beolvasása |
> | Műveletek | Microsoft. Network/networkInterfaces/ipconfigurations/JOIN/Action | Csatlakozik egy hálózati adapter IP-konfigurációjához. Nem riasztásos. |
> | Műveletek | Microsoft. Network/networkInterfaces/ipconfigurations/READ | Hálózati adapter IP-konfigurációjának meghatározását kéri le.  |
> | Műveletek | Microsoft. Network/networkInterfaces/csatlakozás/művelet | Csatlakoztat egy virtuális gépet egy hálózati adapterhez. Nem Riasztásos. |
> | Műveletek | Microsoft. Network/networkInterfaces/loadBalancers/READ | Az összes olyan terheléselosztó beolvasása, amelyhez a hálózati adapter tartozik |
> | Műveletek | Microsoft. Network/networkInterfaces/READ | Hálózati adapter definíciójának beolvasása.  |
> | Műveletek | Microsoft. Network/networkInterfaces/tapConfigurations/delete | Töröl egy hálózati adaptert, koppintson a konfiguráció elemre. |
> | Műveletek | Microsoft. Network/networkInterfaces/tapConfigurations/READ | Beolvas egy hálózati adaptert a konfiguráció elemre koppintva. |
> | Műveletek | Microsoft. Network/networkInterfaces/tapConfigurations/Write | Hálózati adaptert hoz létre, koppintson a konfiguráció lehetőségre, vagy frissítsen egy meglévő hálózati adaptert a konfiguráció gombra. |
> | Műveletek | Microsoft. Network/networkInterfaces/Write | Hálózati adaptert hoz létre, vagy frissít egy meglévő hálózati adaptert.  |
> | Műveletek | Microsoft. Network/networkProfiles/delete | Hálózati profil törlése |
> | Műveletek | Microsoft. Network/networkProfiles/READ | Hálózati profil beolvasása |
> | Műveletek | Microsoft. Network/networkProfiles/removeContainers/Action | Tárolók eltávolítása |
> | Műveletek | Microsoft. Network/networkProfiles/setContainers/Action | Tárolók beállítása |
> | Műveletek | Microsoft. Network/networkProfiles/setNetworkInterfaces/Action | Tároló hálózati adapterek beállítása |
> | Műveletek | Microsoft. Network/networkProfiles/Write | Hálózati profil létrehozása vagy frissítése |
> | Műveletek | Microsoft. Network/networkSecurityGroups/defaultSecurityRules/READ | Alapértelmezett biztonsági szabály definíciójának beolvasása |
> | Műveletek | Microsoft. Network/networkSecurityGroups/delete | Hálózati biztonsági csoport törlése |
> | Műveletek | Microsoft. Network/networkSecurityGroups/csatlakozás/művelet | Csatlakozik egy hálózati biztonsági csoporthoz. Nem Riasztásos. |
> | Műveletek | Microsoft. Network/networkSecurityGroups/READ | Hálózati biztonsági csoport definíciójának beolvasása |
> | Műveletek | Microsoft. Network/networkSecurityGroups/securityRules/delete | Biztonsági szabály törlése |
> | Műveletek | Microsoft. Network/networkSecurityGroups/securityRules/READ | Biztonsági szabály definíciójának beolvasása |
> | Műveletek | Microsoft. Network/networkSecurityGroups/securityRules/Write | Biztonsági szabály létrehozása vagy meglévő biztonsági szabály frissítése |
> | Műveletek | Microsoft. Network/networkSecurityGroups/Write | Hálózati biztonsági csoport létrehozása vagy egy meglévő hálózati biztonsági csoport frissítése |
> | Műveletek | Microsoft. Network/networkWatchers/availableProvidersList/Action | Egy adott Azure-régió összes elérhető internetszolgáltatóját adja vissza. |
> | Műveletek | Microsoft. Network/networkWatchers/azureReachabilityReport/Action | Egy adott hely és az Azure-régiók közötti relatív késési pontszámot adja vissza az internetszolgáltatók számára. |
> | Műveletek | Microsoft. Network/networkWatchers/configureFlowLog/Action | A folyamat naplózását konfigurálja a cél erőforráshoz. |
> | Műveletek | Microsoft. Network/networkWatchers/connectionMonitors/delete | Egy figyelő törlése |
> | Műveletek | Microsoft. Network/networkWatchers/connectionMonitors/Query/Action | A megadott végpontok közötti figyelési kapcsolat lekérdezése |
> | Műveletek | Microsoft. Network/networkWatchers/connectionMonitors/READ | Kapcsolati figyelő részleteinek beolvasása |
> | Műveletek | Microsoft. Network/networkWatchers/connectionMonitors/Start/művelet | A megadott végpontok közötti kapcsolat figyelésének megkezdése |
> | Műveletek | Microsoft. Network/networkWatchers/connectionMonitors/leállítás/művelet | Megadott végpontok közötti figyelési kapcsolat leállítása/szüneteltetése |
> | Műveletek | Microsoft. Network/networkWatchers/connectionMonitors/Write | Létrehoz egy összekötő-figyelőt |
> | Műveletek | Microsoft. Network/networkWatchers/connectivityCheck/Action | Ellenőrzi, hogy lehetséges-e közvetlen TCP-kapcsolatok létesítése egy virtuális gépről egy adott végpontra, beleértve egy másik virtuális gépet vagy egy tetszőleges távoli kiszolgálót. |
> | Műveletek | Microsoft. Network/networkWatchers/delete | Hálózati figyelő törlése |
> | Műveletek | Microsoft. Network/networkWatchers/flowLogs/delete | Egy folyamat naplójának törlése |
> | Műveletek | Microsoft. Network/networkWatchers/flowLogs/READ | Folyamat naplójának beolvasása – részletek |
> | Műveletek | Microsoft. Network/networkWatchers/flowLogs/Write | Folyamat naplójának létrehozása |
> | Műveletek | Microsoft. Network/networkWatchers/ipFlowVerify/Action | Visszaadja, hogy a csomag engedélyezett-e vagy le van tiltva egy adott célhelyről. |
> | Műveletek | Microsoft. Network/networkWatchers/objektívek/delete | Egy lencsét töröl |
> | Műveletek | Microsoft. Network/networkWatchers/objektívek/lekérdezés/művelet | Egy megadott végponton figyelt hálózati forgalom lekérdezése |
> | Műveletek | Microsoft. Network/networkWatchers/objektívek/olvasás | A lencse részleteinek beolvasása |
> | Műveletek | Microsoft. Network/networkWatchers/objektívek/indítás/művelet | A hálózati forgalom figyelésének megkezdése egy megadott végponton |
> | Műveletek | Microsoft. Network/networkWatchers/objektívek/leállítás/művelet | A hálózati forgalom figyelésének leállítása/szüneteltetése egy megadott végponton |
> | Műveletek | Microsoft. Network/networkWatchers/objektívek/írás | Létrehoz egy lencsét |
> | Műveletek | Microsoft. Network/networkWatchers/networkConfigurationDiagnostic/Action | A hálózati konfiguráció diagnosztika. |
> | Műveletek | Microsoft. Network/networkWatchers/nextHop/Action | Egy adott cél és cél IP-cím esetében adja vissza a következő ugrás típusát és a következő remény IP-címét. |
> | Műveletek | Microsoft. Network/networkWatchers/packetCaptures/delete | Csomagok rögzítésének törlése |
> | Műveletek | Microsoft. Network/networkWatchers/packetCaptures/queryStatus/művelet | Információk beolvasása a csomag rögzítési erőforrásának tulajdonságairól és állapotáról. |
> | Műveletek | Microsoft. Network/networkWatchers/packetCaptures/READ | A csomag rögzítési definíciójának beolvasása |
> | Műveletek | Microsoft. Network/networkWatchers/packetCaptures/leállítás/művelet | Állítsa le a futó csomag rögzítési munkamenetét. |
> | Műveletek | Microsoft. Network/networkWatchers/packetCaptures/Write | Létrehoz egy csomagot. |
> | Műveletek | Microsoft. Network/networkWatchers/pingMeshes/delete | PingMesh törlése |
> | Műveletek | Microsoft. Network/networkWatchers/pingMeshes/READ | PingMesh részleteinek beolvasása |
> | Műveletek | Microsoft. Network/networkWatchers/pingMeshes/Start/művelet | PingMesh elindítása a megadott virtuális gépek között |
> | Műveletek | Microsoft. Network/networkWatchers/pingMeshes/leállítás/művelet | A megadott virtuális gépek közötti PingMesh leállítása |
> | Műveletek | Microsoft. Network/networkWatchers/pingMeshes/Write | Létrehoz egy PingMesh |
> | Műveletek | Microsoft. Network/networkWatchers/queryConnectionMonitors/Action | Kötegelt lekérdezés figyelési kapcsolata a megadott végpontok között |
> | Műveletek | Microsoft. Network/networkWatchers/queryFlowLogStatus/Action | Lekérdezi a folyamat naplózási állapotát egy erőforráson. |
> | Műveletek | Microsoft. Network/networkWatchers/queryTroubleshootResult/Action | Lekérdezi a korábban futtatott vagy éppen futó hibaelhárítási művelet hibaelhárítási eredményét. |
> | Műveletek | Microsoft. Network/networkWatchers/READ | A Network Watcher definíciójának beolvasása |
> | Műveletek | Microsoft. Network/networkWatchers/securityGroupView/Action | Megtekintheti a virtuális gépen alkalmazott konfigurált és érvényes hálózati biztonsági csoportra vonatkozó szabályokat. |
> | Műveletek | Microsoft. Network/networkWatchers/topológia/művelet | Lekérdezi az erőforrások hálózati szintű nézetét és azok kapcsolatait egy erőforráscsoporthoz. |
> | Műveletek | Microsoft. Network/networkWatchers/hibakeresés/művelet | Hibaelhárítást indít egy hálózati erőforráson az Azure-ban. |
> | Műveletek | Microsoft. Network/networkWatchers/Write | Hálózati figyelő létrehozása vagy meglévő hálózati figyelő frissítése |
> | Műveletek | Microsoft. Network/Operations/READ | Elérhető műveletek beolvasása |
> | Műveletek | Microsoft. Network/p2sVpnGateways/delete | Töröl egy P2SVpnGateway. |
> | Műveletek | Microsoft. Network/p2sVpnGateways/disconnectp2svpnconnections/Action | P2s VPN-kapcsolatok leválasztása |
> | Műveletek | Microsoft. Network/p2sVpnGateways/generatevpnprofile/Action | VPN-profil előállítása a P2SVpnGateway számára |
> | Műveletek | Microsoft. Network/p2sVpnGateways/getp2svpnconnectionhealth/Action | P2S VPN-kapcsolati állapotának beolvasása a P2SVpnGateway |
> | Műveletek | Microsoft. Network/p2sVpnGateways/getp2svpnconnectionhealthdetailed/Action | P2S VPN-kapcsolat állapotának beolvasása a P2SVpnGateway |
> | Műveletek | Microsoft. Network/p2sVpnGateways/READ | P2SVpnGateway beolvasása. |
> | Műveletek | Microsoft. Network/p2sVpnGateways/Write | Egy P2SVpnGateway helyez el. |
> | Műveletek | Microsoft. Network/privateDnsOperationResults/READ | Egy saját DNS művelet eredményének beolvasása |
> | Műveletek | Microsoft. Network/privateDnsOperationStatuses/READ | Egy saját DNS művelet állapotának beolvasása |
> | Műveletek | Microsoft. Network/privateDnsZones/A/Delete | Távolítsa el egy adott név bejegyzéstípusát, és írja be az "A" értéket egy saját DNS zónából. |
> | Műveletek | Microsoft. Network/privateDnsZones/A/READ | Az "A" típusú rekordhalmaz beolvasása egy saját DNS zónába, JSON formátumban megadva. A rekordhalmaz a rekordok listáját, valamint az ÉLETTARTAMot, a címkéket és a ETAG tartalmazza. |
> | Műveletek | Microsoft. Network/privateDnsZones/A/Write | Hozzon létre vagy frissítsen egy "A" típusú rekordhalmazt egy saját DNS zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Műveletek | Microsoft. Network/privateDnsZones/AAAA/delete | Távolítsa el egy adott név rekordját, és írja be az "AAAA" értéket egy saját DNS zónából. |
> | Műveletek | Microsoft. Network/privateDnsZones/AAAA/olvasás | Az "AAAA" típusú rekordhalmaz beolvasása egy saját DNS zónába, JSON formátumban megadva. A rekordhalmaz a rekordok listáját, valamint az ÉLETTARTAMot, a címkéket és a ETAG tartalmazza. |
> | Műveletek | Microsoft. Network/privateDnsZones/AAAA/írás | Hozzon létre vagy frissítsen egy "AAAA" típusú rekordhalmazt egy saját DNS zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Műveletek | Microsoft. Network/privateDnsZones/mind/olvasás | saját DNS-rekordhalmaz beolvasása típusok között |
> | Műveletek | Microsoft. Network/privateDnsZones/CNAME/delete | Távolítsa el egy adott név rekordját, és írja be a "CNAME" típust egy saját DNS zónából. |
> | Műveletek | Microsoft. Network/privateDnsZones/CNAME/READ | A "CNAME" típusú rekordhalmaz beolvasása egy saját DNS zónában, JSON formátumban megadva. |
> | Műveletek | Microsoft. Network/privateDnsZones/CNAME/Write | CNAME típusú rekordhalmaz létrehozása vagy frissítése egy saját DNS zónán belül. |
> | Műveletek | Microsoft. Network/privateDnsZones/delete | Saját DNS zóna törlése. |
> | Műveletek | Microsoft. Network/privateDnsZones/MX/delete | Távolítsa el egy adott név rekordját, és írja be az "MX" típust egy saját DNS zónából. |
> | Műveletek | Microsoft. Network/privateDnsZones/MX/READ | Az "MX" típusú rekordhalmaz beolvasása egy saját DNS zónába, JSON formátumban megadva. A rekordhalmaz a rekordok listáját, valamint az ÉLETTARTAMot, a címkéket és a ETAG tartalmazza. |
> | Műveletek | Microsoft. Network/privateDnsZones/MX/Write | Hozzon létre vagy frissítsen egy MX típusú rekordhalmazt egy saját DNS zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Műveletek | Microsoft. Network/privateDnsZones/PTR/delete | Távolítsa el egy adott név rekordját, és írja be a "PTR" értéket egy saját DNS zónából. |
> | Műveletek | Microsoft. Network/privateDnsZones/PTR/READ | A "PTR" típusú rekordhalmaz beolvasása egy saját DNS zónában JSON formátumban. A rekordhalmaz a rekordok listáját, valamint az ÉLETTARTAMot, a címkéket és a ETAG tartalmazza. |
> | Műveletek | Microsoft. Network/privateDnsZones/PTR/Write | Hozzon létre vagy frissítsen egy PTR típusú rekordhalmazt egy saját DNS zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Műveletek | Microsoft. Network/privateDnsZones/READ | A saját DNS zóna tulajdonságainak beolvasása JSON formátumban. Vegye figyelembe, hogy a parancs nem kéri le azokat a virtuális hálózatokat, amelyekhez a saját DNS zóna csatolva van, vagy a zónán belül található rekordhalmazok. |
> | Műveletek | Microsoft. Network/privateDnsZones/rekordhalmazok/olvasás | saját DNS-rekordhalmaz beolvasása típusok között |
> | Műveletek | Microsoft. Network/privateDnsZones/SOA/READ | A "SOA" típusú rekordhalmaz beszerzése egy saját DNS zónában, JSON formátumban megadva. |
> | Műveletek | Microsoft. Network/privateDnsZones/SOA/Write | Egy "SOA" típusú rekordhalmaz frissítése egy saját DNS zónán belül. |
> | Műveletek | Microsoft. Network/privateDnsZones/SRV/delete | Távolítsa el az adott név és a "SRV" típusú rekordot egy saját DNS zónából. |
> | Műveletek | Microsoft. Network/privateDnsZones/SRV/READ | A "SRV" típusú rekordhalmaz beolvasása egy saját DNS zónában JSON formátumban. A rekordhalmaz a rekordok listáját, valamint az ÉLETTARTAMot, a címkéket és a ETAG tartalmazza. |
> | Műveletek | Microsoft. Network/privateDnsZones/SRV/Write | Hozzon létre vagy frissítsen egy "SRV" típusú rekordhalmazt egy saját DNS zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Műveletek | Microsoft. Network/privateDnsZones/TXT/delete | Távolítsa el egy adott név bejegyzéstípusát, és írja be a "TXT" típust egy saját DNS zónából. |
> | Műveletek | Microsoft. Network/privateDnsZones/TXT/READ | A "TXT" típusú rekordhalmaz beolvasása egy saját DNS zónában JSON formátumban. A rekordhalmaz a rekordok listáját, valamint az ÉLETTARTAMot, a címkéket és a ETAG tartalmazza. |
> | Műveletek | Microsoft. Network/privateDnsZones/TXT/Write | Hozzon létre vagy frissítsen egy "TXT" típusú rekordhalmazt egy saját DNS zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Műveletek | Microsoft. Network/privateDnsZones/virtualNetworkLinks/delete | Saját DNS zóna virtuális hálózatra mutató hivatkozásának törlése. |
> | Műveletek | Microsoft. Network/privateDnsZones/virtualNetworkLinks/READ | A saját DNS zóna hivatkozásának beolvasása a virtuális hálózat tulajdonságaihoz JSON formátumban. |
> | Műveletek | Microsoft. Network/privateDnsZones/virtualNetworkLinks/Write | Saját DNS zóna virtuális hálózatra mutató hivatkozásának létrehozása vagy frissítése. |
> | Műveletek | Microsoft. Network/privateDnsZones/Write | Hozzon létre vagy frissítsen egy saját DNS zónát az erőforráscsoporthoz. Vegye figyelembe, hogy ez a parancs nem használható virtuális hálózati kapcsolatok vagy rekordhalmazok zónán belüli létrehozására vagy frissítésére. |
> | Műveletek | Microsoft. Network/privateEndpointRedirectMaps/READ | Privát végpont RedirectMap beolvasása |
> | Műveletek | Microsoft. Network/privateEndpointRedirectMaps/Write | Privát végponti RedirectMap hoz létre vagy frissít egy meglévő privát végpont RedirectMap |
> | Műveletek | Microsoft. Network/privateEndpoints/delete | Egy privát végponti erőforrás törlése. |
> | Műveletek | Microsoft. Network/privateEndpoints/READ | Egy privát végpont erőforrásának beolvasása. |
> | Műveletek | Microsoft. Network/privateEndpoints/Write | Létrehoz egy új privát végpontot, vagy frissít egy meglévő privát végpontot. |
> | Műveletek | Microsoft. Network/privateLinkServices/delete | Egy privát link Service-erőforrás törlése. |
> | Műveletek | Microsoft. Network/privateLinkServices/privateEndpointConnections/delete | Privát végponti kapcsolatok törlése. |
> | Műveletek | Microsoft. Network/privateLinkServices/privateEndpointConnections/READ | Egy privát végponti kapcsolat definíciójának beolvasása. |
> | Műveletek | Microsoft. Network/privateLinkServices/privateEndpointConnections/Write | Létrehoz egy új magánhálózati végponti kapcsolatokat, vagy frissíti a meglévő magánhálózati végponti kapcsolatokat. |
> | Műveletek | Microsoft. Network/privateLinkServices/READ | Egy privát kapcsolati szolgáltatás erőforrásának beolvasása. |
> | Műveletek | Microsoft. Network/privateLinkServices/Write | Létrehoz egy új privát kapcsolati szolgáltatást, vagy frissíti a meglévő Private link Service-t. |
> | Műveletek | Microsoft. Network/nyilvános IP/delete | Töröl egy nyilvános IP-címet. |
> | Műveletek | Microsoft. Network/nyilvános IP/csatlakozás/művelet | Csatlakozik egy nyilvános IP-címhez. Nem Riasztásos. |
> | Műveletek | Microsoft. Network/nyilvános IP/READ | Egy nyilvános IP-cím definíciójának beolvasása. |
> | Műveletek | Microsoft. Network/nyilvános IP/Write | Létrehoz egy nyilvános IP-címet, vagy frissíti a meglévő nyilvános IP-címet.  |
> | Műveletek | Microsoft. Network/publicIPPrefixes/delete | Nyilvános IP-előtag törlése |
> | Műveletek | Microsoft. Network/publicIPPrefixes/csatlakozás/művelet | Egy PublicIPPrefix illesztése. Nem riasztásos. |
> | Műveletek | Microsoft. Network/publicIPPrefixes/READ | Nyilvános IP-előtag definíciójának beolvasása |
> | Műveletek | Microsoft. Network/publicIPPrefixes/Write | Létrehoz egy nyilvános IP-előtagot, vagy frissíti a meglévő nyilvános IP-előtagot. |
> | Műveletek | Microsoft. Network/regisztráció/művelet | Regisztrálja az előfizetést |
> | Műveletek | Microsoft. Network/routeFilters/delete | Egy útvonal-szűrő definíciójának törlése |
> | Műveletek | Microsoft. Network/routeFilters/csatlakozás/művelet | Egy útvonal-szűrőhöz csatlakozik. Nem Riasztásos. |
> | Műveletek | Microsoft. Network/routeFilters/READ | Egy útvonal-szűrő definíciójának beolvasása |
> | Műveletek | Microsoft. Network/routeFilters/routeFilterRules/delete | Egy útvonal-szűrő szabály definíciójának törlése |
> | Műveletek | Microsoft. Network/routeFilters/routeFilterRules/READ | Egy útvonal-szűrő szabály definíciójának beolvasása |
> | Műveletek | Microsoft. Network/routeFilters/routeFilterRules/Write | Egy útvonal-szűrő szabály létrehozása vagy egy meglévő útvonal-szűrési szabály frissítése |
> | Műveletek | Microsoft. Network/routeFilters/Write | Útvonal-szűrő létrehozása vagy meglévő útvonal-szűrő frissítése |
> | Műveletek | Microsoft. Network/routeTables/delete | Útválasztási táblázat definíciójának törlése |
> | Műveletek | Microsoft. Network/routeTables/csatlakozás/művelet | Egy útválasztási táblázathoz csatlakozik. Nem Riasztásos. |
> | Műveletek | Microsoft. Network/routeTables/READ | Útválasztási táblázat definíciójának beolvasása |
> | Műveletek | Microsoft. Network/routeTables/Routes/delete | Útvonal-definíció törlése |
> | Műveletek | Microsoft. Network/routeTables/Routes/READ | Útvonal-definíció beolvasása |
> | Műveletek | Microsoft. Network/routeTables/Routes/Write | Útvonal létrehozása vagy meglévő útvonal frissítése |
> | Műveletek | Microsoft. Network/routeTables/Write | Útválasztási táblázat létrehozása vagy meglévő útválasztási táblázat frissítése |
> | Műveletek | Microsoft. Network/serviceEndpointPolicies/delete | Szolgáltatás-végponti házirend törlése |
> | Műveletek | Microsoft. Network/serviceEndpointPolicies/csatlakozás/művelet | Csatlakozik egy szolgáltatás-végponti házirendhez. Nem riasztásos. |
> | Műveletek | Microsoft. Network/serviceEndpointPolicies/joinSubnet/Action | Összekapcsolja az alhálózatot a szolgáltatás végponti házirendjeivel. Nem riasztásos. |
> | Műveletek | Microsoft. Network/serviceEndpointPolicies/READ | Szolgáltatás-végponti szabályzat leírásának beolvasása |
> | Műveletek | Microsoft. Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | Szolgáltatás-végponti házirend definíciójának törlése |
> | Műveletek | Microsoft. Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/READ | Lekérdezi a szolgáltatás végponti házirendjének definíciójának leírását |
> | Műveletek | Microsoft. Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/Write | Létrehoz egy szolgáltatás-végpont házirend-definícióját, vagy frissíti egy meglévő szolgáltatási végpont házirend-definícióját. |
> | Műveletek | Microsoft. Network/serviceEndpointPolicies/Write | Létrehoz egy szolgáltatás-végponti házirendet, vagy frissíti a meglévő szolgáltatási végponti házirendet |
> | Műveletek | Microsoft. Network/trafficManagerGeographicHierarchies/READ | Lekéri a földrajzi forgalom útválasztási módszerével használható régiókat tartalmazó Traffic Manager földrajzi hierarchiát |
> | Műveletek | Microsoft. Network/trafficManagerProfiles/azureEndpoints/delete | Töröl egy Azure-végpontot egy meglévő Traffic Manager-profilból. Traffic Manager leállítja az útválasztási forgalmat a törölt Azure-végpontra. |
> | Műveletek | Microsoft. Network/trafficManagerProfiles/azureEndpoints/READ | Beolvas egy Traffic Manager profilhoz tartozó Azure-végpontot, beleértve az adott Azure-végpont összes tulajdonságát. |
> | Műveletek | Microsoft. Network/trafficManagerProfiles/azureEndpoints/Write | Adjon hozzá egy új Azure-végpontot egy meglévő Traffic Manager profilhoz, vagy frissítse a meglévő Azure-végpont tulajdonságait az adott Traffic Manager-profilban. |
> | Műveletek | Microsoft. Network/trafficManagerProfiles/delete | Törölje a Traffic Manager profilt. A Traffic Manager profiljához társított összes beállítás elvész, és a profil már nem használható a forgalom irányítására. |
> | Műveletek | Microsoft. Network/trafficManagerProfiles/externalEndpoints/delete | Külső végpont törlése egy meglévő Traffic Manager-profilból. Traffic Manager megszakítja a törölt külső végpont felé irányuló útválasztási forgalmat. |
> | Műveletek | Microsoft. Network/trafficManagerProfiles/externalEndpoints/READ | Beolvas egy Traffic Manager profilhoz tartozó külső végpontot, amely tartalmazza az adott külső végpont összes tulajdonságát. |
> | Műveletek | Microsoft. Network/trafficManagerProfiles/externalEndpoints/Write | Adjon hozzá egy új külső végpontot egy meglévő Traffic Manager profilhoz, vagy frissítse a meglévő külső végpont tulajdonságait az adott Traffic Manager profilban. |
> | Műveletek | Microsoft. Network/trafficManagerProfiles/Intenzitástérképei/READ | Lekérdezi a megadott Traffic Manager-profilhoz tartozó Traffic Manager Heat térképet, amely tartalmazza a lekérdezések számát és a késési időt a hely és a forrás IP-címe alapján. |
> | Műveletek | Microsoft. Network/trafficManagerProfiles/nestedEndpoints/delete | Beágyazott végpont törlése egy meglévő Traffic Manager-profilból. Traffic Manager leállítja a törölt beágyazott végpontra irányuló útválasztási forgalmat. |
> | Műveletek | Microsoft. Network/trafficManagerProfiles/nestedEndpoints/READ | Beolvas egy Traffic Manager profilhoz tartozó beágyazott végpontot, amely tartalmazza az adott beágyazott végpont összes tulajdonságát. |
> | Műveletek | Microsoft. Network/trafficManagerProfiles/nestedEndpoints/Write | Adjon hozzá egy új beágyazott végpontot egy meglévő Traffic Manager profilhoz, vagy frissítse a meglévő beágyazott végpont tulajdonságait az adott Traffic Manager-profilban. |
> | Műveletek | Microsoft. Network/trafficManagerProfiles/READ | Traffic Manager profil konfigurációjának beolvasása. Ide tartozik a DNS-beállítások, a forgalmi útválasztási beállítások, a végpont-figyelési beállítások, valamint a Traffic Manager profil által irányított végpontok listája. |
> | Műveletek | Microsoft. Network/trafficManagerProfiles/Write | Hozzon létre egy Traffic Manager profilt, vagy módosítsa egy meglévő Traffic Manager-profil konfigurációját.<br>Ide tartozik a profilok engedélyezése vagy letiltása, valamint a DNS-beállítások, a forgalmi útválasztási beállítások vagy a végpont-figyelési beállítások módosítása.<br>A Traffic Manager profil által irányított végpontok hozzáadhatók, eltávolíthatók, engedélyezhetők vagy letilthatók. |
> | Műveletek | Microsoft. Network/trafficManagerUserMetricsKeys/delete | Törli a valós idejű felhasználói metrikák gyűjteményéhez használt előfizetési szintű kulcsot. |
> | Műveletek | Microsoft. Network/trafficManagerUserMetricsKeys/READ | Lekérdezi a valós idejű felhasználói metrikák gyűjteményéhez használt előfizetési szintű kulcsot. |
> | Műveletek | Microsoft. Network/trafficManagerUserMetricsKeys/Write | Létrehoz egy új előfizetési szintű kulcsot, amelyet a rendszer a valós idejű felhasználói metrikák gyűjteményéhez használ. |
> | Műveletek | Microsoft. Network/regisztráció/művelet | Előfizetés regisztrációjának törlése |
> | Műveletek | Microsoft. Network/virtualHubs/delete | Virtuális központ törlése |
> | Műveletek | Microsoft. Network/virtualHubs/effectiveRoutes/Action | A virtuális központban konfigurált érvényes útvonal beolvasása |
> | Műveletek | Microsoft. Network/virtualHubs/hubVirtualNetworkConnections/delete | HubVirtualNetworkConnection törlése |
> | Műveletek | Microsoft. Network/virtualHubs/hubVirtualNetworkConnections/READ | HubVirtualNetworkConnection beszerzése |
> | Műveletek | Microsoft. Network/virtualHubs/hubVirtualNetworkConnections/Write | HubVirtualNetworkConnection létrehozása vagy frissítése |
> | Műveletek | Microsoft. Network/virtualHubs/READ | Virtuális központ beszerzése |
> | Műveletek | Microsoft. Network/virtualHubs/routeTables/delete | VirtualHubRouteTableV2 törlése |
> | Műveletek | Microsoft. Network/virtualHubs/routeTables/READ | VirtualHubRouteTableV2 beszerzése |
> | Műveletek | Microsoft. Network/virtualHubs/routeTables/Write | VirtualHubRouteTableV2 létrehozása vagy frissítése |
> | Műveletek | Microsoft. Network/virtualHubs/Write | Virtuális központ létrehozása vagy frissítése |
> | Műveletek | Microsoft. Network/virtualnetworkgateways/Connections/READ | VirtualNetworkGatewayConnection beolvasása |
> | Műveletek | Microsoft. Network/virtualNetworkGateways/delete | VirtualNetworkGateway törlése |
> | Műveletek | Microsoft. Network/virtualnetworkgateways/disconnectvirtualnetworkgatewayvpnconnections/Action | Virtuális hálózati átjáró VPN-kapcsolatainak leválasztása |
> | Műveletek | Microsoft. Network/virtualnetworkgateways/generatevpnclientpackage/Action | Ügyfele-csomag előállítása a virtualNetworkGateway |
> | Műveletek | Microsoft. Network/virtualnetworkgateways/generatevpnprofile/Action | VpnProfile-csomag előállítása a VirtualNetworkGateway |
> | Műveletek | Microsoft. Network/virtualnetworkgateways/getadvertisedroutes/Action | Beolvassa a virtualNetworkGateway meghirdetett útvonalakat |
> | Műveletek | Microsoft. Network/virtualnetworkgateways/getbgppeerstatus/Action | VirtualNetworkGateway BGP-társ állapotának beolvasása |
> | Műveletek | Microsoft. Network/virtualnetworkgateways/getlearnedroutes/Action | Virtualnetworkgateway megtanult útvonalak beolvasása |
> | Műveletek | Microsoft. Network/virtualnetworkgateways/getvpnclientconnectionhealth/Action | VPN-ügyfél kapcsolati állapotának beolvasása a VirtualNetworkGateway |
> | Műveletek | Microsoft. Network/virtualnetworkgateways/getvpnclientipsecparameters/Action | A VirtualNetworkGateway P2S-ügyfél ügyfele IPSec-paramétereinek beolvasása. |
> | Műveletek | Microsoft. Network/virtualnetworkgateways/getvpnprofilepackageurl/Action | Egy előre generált VPN Client profil csomag URL-címének beolvasása |
> | Műveletek | Microsoft. Network/virtualNetworkGateways/READ | VirtualNetworkGateway beolvasása |
> | Műveletek | Microsoft. Network/virtualnetworkgateways/reset/Action | VirtualNetworkGateway alaphelyzetbe állítása |
> | Műveletek | Microsoft. Network/virtualnetworkgateways/resetvpnclientsharedkey/Action | A VirtualNetworkGateway P2S-ügyfél ügyfele-alapú megosztott kulcsának alaphelyzetbe állítása. |
> | Műveletek | Microsoft. Network/virtualnetworkgateways/setvpnclientipsecparameters/Action | Ügyfele IPSec-paraméterek beállítása a VirtualNetworkGateway P2S-ügyfél számára. |
> | Műveletek | Microsoft. Network/virtualnetworkgateways/startpacketcapture/Action | Elindítja a Virtual Network-átjáró csomagjainak rögzítését. |
> | Műveletek | Microsoft. Network/virtualnetworkgateways/stoppacketcapture/Action | Leállítja egy Virtual Network átjáró csomagjainak rögzítését. |
> | Műveletek | Microsoft. Network/virtualnetworkgateways/supportedvpndevices/Action | A támogatott VPN-eszközök listája |
> | Műveletek | Microsoft. Network/virtualNetworkGateways/Write | VirtualNetworkGateway létrehozása vagy frissítése |
> | Műveletek | Microsoft. Network/virtualNetworks/BastionHosts/Action | Beolvas egy Virtual Network. |
> | Műveletek | Microsoft. Network/virtualNetworks/bastionHosts/default/művelet | Beolvas egy Virtual Network. |
> | Műveletek | Microsoft. Network/virtualNetworks/checkIpAddressAvailability/READ | Ellenőrizze, hogy az IP-cím elérhető-e a megadott virtuális hálózaton |
> | Műveletek | Microsoft. Network/virtualNetworks/delete | Virtuális hálózat törlése |
> | Műveletek | Microsoft. Network/virtualNetworks/csatlakozás/művelet | Csatlakozik egy virtuális hálózathoz. Nem Riasztásos. |
> | Műveletek | Microsoft. Network/virtualNetworks/peer/Action | Virtuális hálózat társítása egy másik virtuális hálózattal |
> | Műveletek | Microsoft. Network/virtualNetworks/READ | A virtuális hálózat definíciójának beolvasása |
> | Műveletek | Microsoft. Network/virtualNetworks/Subnets/contextualServiceEndpointPolicies/delete | Környezetfüggő szolgáltatásbeli végponti szabályzat törlése |
> | Műveletek | Microsoft. Network/virtualNetworks/Subnets/contextualServiceEndpointPolicies/READ | Környezetfüggő szolgáltatási végponti szabályzatok beolvasása |
> | Műveletek | Microsoft. Network/virtualNetworks/alhálózat/contextualServiceEndpointPolicies/írás | Környezetfüggő szolgáltatási végponti szabályzatot hoz létre, vagy frissíti a meglévő kontextus-szolgáltatás végponti szabályzatát |
> | Műveletek | Microsoft. Network/virtualNetworks/alhálózatok/törlés | Virtuális hálózati alhálózat törlése |
> | Műveletek | Microsoft. Network/virtualNetworks/alhálózatok/csatlakozás/művelet | Csatlakozik egy virtuális hálózathoz. Nem Riasztásos. |
> | Műveletek | Microsoft. Network/virtualNetworks/Subnets/joinViaServiceEndpoint/művelet | Összekapcsolja az erőforrást, például a Storage-fiókot vagy az SQL Database-t egy alhálózathoz. Nem riasztásos. |
> | Műveletek | Microsoft. Network/virtualNetworks/Subnets/prepareNetworkPolicies/művelet | Alhálózat előkészítése a szükséges hálózati házirendek alkalmazásával |
> | Műveletek | Microsoft. Network/virtualNetworks/alhálózatok/olvasás | Virtuális hálózati alhálózat definíciójának beolvasása |
> | Műveletek | Microsoft. Network/virtualNetworks/Subnets/unprepareNetworkPolicies/művelet | Alhálózat előkészítésének megszüntetése az alkalmazott hálózati házirendek eltávolításával |
> | Műveletek | Microsoft. Network/virtualNetworks/Subnets/virtualMachines/READ | A virtuális hálózati alhálózat összes virtuális gépre mutató hivatkozás beolvasása |
> | Műveletek | Microsoft. Network/virtualNetworks/alhálózatok/írás | Létrehoz egy virtuális hálózati alhálózatot, vagy frissít egy meglévő virtuális hálózati alhálózatot |
> | Műveletek | Microsoft. Network/virtualNetworks/használat/olvasás | A virtuális hálózat minden alhálózatához tartozó IP-használat beszerzése |
> | Műveletek | Microsoft. Network/virtualNetworks/virtualMachines/READ | A virtuális hálózatban lévő összes virtuális gépre mutató hivatkozásokat kap |
> | Műveletek | Microsoft. Network/virtualNetworks/virtualNetworkPeerings/delete | Virtuális hálózati társak törlése |
> | Műveletek | Microsoft. Network/virtualNetworks/virtualNetworkPeerings/READ | Virtuális hálózat társítási definíciójának beolvasása |
> | Műveletek | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Létrehoz egy virtuális hálózatot, vagy frissíti egy meglévő virtuális hálózati társat |
> | Műveletek | Microsoft. Network/virtualNetworks/Write | Virtuális hálózat létrehozása vagy meglévő virtuális hálózat frissítése |
> | Műveletek | Microsoft. Network/virtualNetworkTaps/delete | Törlés Virtual Network koppintson |
> | Műveletek | Microsoft. Network/virtualNetworkTaps/csatlakozás/művelet | Csatlakozik egy virtuális hálózathoz. Nem Riasztásos. |
> | Műveletek | Microsoft. Network/virtualNetworkTaps/READ | Virtual Network koppintás beolvasása |
> | Műveletek | Microsoft. Network/virtualNetworkTaps/Write | Virtual Network létrehozása vagy frissítése koppintson |
> | Műveletek | Microsoft. Network/virtualRouters/delete | VirtualRouter törlése |
> | Műveletek | Microsoft. Network/virtualRouters/csatlakozás/művelet | Egy VirtualRouter illesztése. Nem riasztásos. |
> | Műveletek | Microsoft. Network/virtualRouters/társak/törlés | VirtualRouterPeering törlése |
> | Műveletek | Microsoft. Network/virtualRouters/társak/olvasás | VirtualRouterPeering beolvasása |
> | Műveletek | Microsoft. Network/virtualRouters/társak/írás | Létrehoz egy VirtualRouterPeering, vagy frissít egy meglévő VirtualRouterPeering |
> | Műveletek | Microsoft. Network/virtualRouters/READ | VirtualRouter beolvasása |
> | Műveletek | Microsoft. Network/virtualRouters/Write | Létrehoz egy VirtualRouter, vagy frissít egy meglévő VirtualRouter |
> | Műveletek | Microsoft. Network/virtualWans/delete | Virtuális WAN törlése |
> | Műveletek | Microsoft. Network/virtualwans/generateVpnProfile/Action | VirtualWanVpnServerConfiguration-VpnProfile előállítása |
> | Műveletek | Microsoft. Network/virtualWans/p2sVpnServerConfigurations/delete | Virtuális WAN-P2SVpnServerConfiguration törlése |
> | Műveletek | Microsoft. Network/virtualWans/p2sVpnServerConfigurations/READ | Virtuális WAN-P2SVpnServerConfiguration beolvasása |
> | Műveletek | Microsoft. Network/virtualWans/p2sVpnServerConfigurations/Write | Virtuális WAN-P2SVpnServerConfiguration létrehozása vagy meglévő virtuális WAN-P2SVpnServerConfiguration frissítése |
> | Műveletek | Microsoft. Network/virtualWans/READ | Virtuális WAN beszerzése |
> | Műveletek | Microsoft. Network/virtualwans/supportedSecurityProviders/READ | A VirtualWan biztonsági szolgáltatók támogatottak. |
> | Műveletek | Microsoft. Network/virtualWans/virtualHubs/READ | Minden virtuális WAN-ra hivatkozó virtuális hubok beolvasása. |
> | Műveletek | Microsoft. Network/virtualwans/vpnconfiguration/Action | VPN-konfiguráció beolvasása |
> | Műveletek | Microsoft. Network/virtualwans/vpnServerConfigurations/Action | VirtualWanVpnServerConfigurations beolvasása |
> | Műveletek | Microsoft. Network/virtualWans/vpnSites/READ | A virtuális WAN-ra hivatkozó összes VPN-hely beolvasása. |
> | Műveletek | Microsoft. Network/virtualWans/Write | Virtuális WAN létrehozása vagy frissítése |
> | Műveletek | Microsoft. Network/vpnGateways/delete | Töröl egy átjáróban. |
> | Műveletek | Microsoft. Network/vpngateways/listvpnconnectionshealth/Action | A kapcsolati állapot beolvasása a átjáróban lévő kapcsolatok teljes vagy egy részhalmazára |
> | Műveletek | Microsoft. Network/vpnGateways/READ | Átjáróban beolvasása. |
> | Műveletek | Microsoft. Network/vpngateways/reset/Action | Átjáróban alaphelyzetbe állítása |
> | Műveletek | Microsoft. Network/vpnGateways/VPN/delete | Töröl egy VpnConnection. |
> | Műveletek | Microsoft. Network/vpnGateways/VPN/READ | VpnConnection beolvasása. |
> | Műveletek | Microsoft. Network/vpnGateways/VPN/vpnLinkConnections/READ | VPN-kapcsolat kapcsolatának beolvasása |
> | Műveletek | Microsoft. Network/vpnGateways/VPN/Write | Egy VpnConnection helyez el. |
> | Műveletek | Microsoft. Network/vpnGateways/Write | Egy átjáróban helyez el. |
> | Műveletek | Microsoft. Network/vpnServerConfigurations/delete | VpnServerConfiguration törlése |
> | Műveletek | Microsoft. Network/vpnServerConfigurations/READ | VpnServerConfiguration beolvasása |
> | Műveletek | Microsoft. Network/vpnServerConfigurations/Write | VpnServerConfiguration létrehozása vagy frissítése |
> | Műveletek | Microsoft. Network/vpnsites/delete | Törli a VPN-hely erőforrását. |
> | Műveletek | Microsoft. Network/vpnsites/READ | Egy VPN-hely erőforrásának beolvasása. |
> | Műveletek | Microsoft. Network/vpnSites/vpnSiteLinks/READ | VPN-hely hivatkozásának beolvasása |
> | Műveletek | Microsoft. Network/vpnsites/Write | Egy VPN-hely erőforrásának létrehozása vagy frissítése. |

## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. NotificationHubs/CheckNamespaceAvailability/művelet | Ellenőrzi, hogy a megadott névtér-erőforrás neve elérhető-e a NotificationHub szolgáltatásban. |
> | Műveletek | Microsoft. NotificationHubs/névterek/engedélyezési szabályok/művelet | A névterek engedélyezési szabályai leírásának beolvasása. |
> | Műveletek | Microsoft. NotificationHubs/névterek/engedélyezési szabályok/delete | Névtér-engedélyezési szabály törlése. Az alapértelmezett névtér-engedélyezési szabály nem törölhető.  |
> | Műveletek | Microsoft. NotificationHubs/névterek/engedélyezési szabályok/listkeys műveletének beolvasása/művelet | A névtérhez tartozó kapcsolódási karakterlánc beolvasása |
> | Műveletek | Microsoft. NotificationHubs/névterek/engedélyezési szabályok/olvasás | A névterek engedélyezési szabályai leírásának beolvasása. |
> | Műveletek | Microsoft. NotificationHubs/névterek/engedélyezési szabályok/regenerateKeys/művelet | A névtér-engedélyezési szabály újragenerálta az elsődleges/Értesítésiközpont, megadhatja azt a kulcsot, amelyet újra kell generált |
> | Műveletek | Microsoft. NotificationHubs/névterek/engedélyezési szabályok/írás | Hozzon létre egy névtér szintű engedélyezési szabályokat, és frissítse a tulajdonságait. Az engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és a másodlagos kulcsokat is frissítheti. |
> | Műveletek | Microsoft. NotificationHubs/névterek/CheckNotificationHubAvailability/művelet | Ellenőrzi, hogy a megadott NotificationHub-név elérhető-e a névtéren belül. |
> | Műveletek | Microsoft. NotificationHubs/névterek/törlés | Névtér erőforrásának törlése |
> | Műveletek | Microsoft. NotificationHubs/névterek/NotificationHubs/engedélyezési szabályok/művelet | Az értesítési központ engedélyezési szabályainak listájának beolvasása |
> | Műveletek | Microsoft. NotificationHubs/névterek/NotificationHubs/engedélyezési szabályok/delete | Értesítési központ engedélyezési szabályainak törlése |
> | Műveletek | Microsoft. NotificationHubs/névterek/NotificationHubs/engedélyezési szabályok/listkeys műveletének beolvasása/művelet | Az értesítési központ kapcsolódási karakterláncának beolvasása |
> | Műveletek | Microsoft. NotificationHubs/névterek/NotificationHubs/engedélyezési szabályok/READ | Az értesítési központ engedélyezési szabályainak listájának beolvasása |
> | Műveletek | Microsoft. NotificationHubs/névterek/NotificationHubs/engedélyezési szabályok/regenerateKeys/művelet | Az értesítési központ engedélyezési szabálya újragenerálta az elsődleges/Értesítésiközpont, megadhatja azt a kulcsot, amelyet újra kell generált |
> | Műveletek | Microsoft. NotificationHubs/névterek/NotificationHubs/engedélyezési szabályok/Write | Hozzon létre értesítési központ engedélyezési szabályait, és frissítse a tulajdonságait. Az engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és a másodlagos kulcsokat is frissítheti. |
> | Műveletek | Microsoft. NotificationHubs/névterek/NotificationHubs/debugSend/művelet | Teszt leküldéses értesítés küldése. |
> | Műveletek | Microsoft. NotificationHubs/névterek/NotificationHubs/delete | Értesítési központ erőforrásának törlése |
> | Műveletek | Microsoft. NotificationHubs/névterek/NotificationHubs/metricDefinitions/READ | Névtér-metrikák erőforrás-leírásának beolvasása |
> | Műveletek | Microsoft. NotificationHubs/névterek/NotificationHubs/pnsCredentials/művelet | Az összes értesítési központ PNS hitelesítő adatainak beolvasása. Ide tartozik a WNS, a MPNS, a APNS, a GCM és a Baidu hitelesítő adatai. |
> | Műveletek | Microsoft. NotificationHubs/névterek/NotificationHubs/olvasás | Értesítési központ erőforrás-leírásának beolvasása |
> | Műveletek | Microsoft. NotificationHubs/névterek/NotificationHubs/írás | Hozzon létre egy értesítési központot, és frissítse a tulajdonságait. A tulajdonságai főleg PNS hitelesítő adatokkal rendelkeznek. Engedélyezési szabályok és TTL |
> | Műveletek | Microsoft. NotificationHubs/névterek/olvasás | A névtér erőforrás-leírásának beolvasása |
> | Műveletek | Microsoft. NotificationHubs/névterek/írás | Hozzon létre egy névtér-erőforrást, és frissítse a tulajdonságait. A névtér címkéi és kapacitása a frissíthető tulajdonságok. |
> | Műveletek | Microsoft. NotificationHubs/operationResults/READ | Notification Hubs szolgáltató műveleti eredményének visszaadása |
> | Műveletek | Microsoft. NotificationHubs/Operations/READ | Notification Hubs szolgáltató által támogatott műveletek listáját adja vissza. |
> | Műveletek | Microsoft. NotificationHubs/regisztráció/művelet | Regisztrálja az előfizetést a NotificationHubs erőforrás-szolgáltatóhoz, és lehetővé teszi a névterek és NotificationHubs létrehozását |
> | Műveletek | Microsoft. NotificationHubs/regisztráció/művelet | A NotificationHubs erőforrás-szolgáltatóhoz tartozó előfizetés regisztrációjának megszüntetése, és a névterek és NotificationHubs létrehozásának engedélyezése |

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. OffAzure/HyperVSites/fürtök/olvasás | Egy Hyper-V fürt tulajdonságainak beolvasása |
> | Műveletek | Microsoft. OffAzure/HyperVSites/fürtök/írás | A Hyper-V fürt létrehozása vagy frissítése |
> | Műveletek | Microsoft. OffAzure/HyperVSites/delete | A Hyper-V hely törlése |
> | Műveletek | Microsoft. OffAzure/HyperVSites/healthsummary/READ | A Hyper-V erőforrás állapot-összefoglalásának beolvasása |
> | Műveletek | Microsoft. OffAzure/HyperVSites/gazdagépek/olvasás | Egy Hyper-V-gazdagép tulajdonságainak beolvasása |
> | Műveletek | Microsoft. OffAzure/HyperVSites/hosts/Write | A Hyper-V-gazdagép létrehozása vagy frissítése |
> | Műveletek | Microsoft. OffAzure/HyperVSites/feladatok/olvasás | A Hyper-V-feladatok tulajdonságainak beolvasása |
> | Műveletek | Microsoft. OffAzure/HyperVSites/Machines/READ | A Hyper-V gépek tulajdonságainak beolvasása |
> | Műveletek | Microsoft. OffAzure/HyperVSites/operationsstatus/READ | Egy Hyper-V műveleti állapot tulajdonságainak beolvasása |
> | Műveletek | Microsoft. OffAzure/HyperVSites/READ | Egy Hyper-V-hely tulajdonságainak beolvasása |
> | Műveletek | Microsoft. OffAzure/HyperVSites/frissítés/művelet | A Hyper-V-helyen lévő objektumok frissítése |
> | Műveletek | Microsoft. OffAzure/HyperVSites/runasaccounts/READ | Hyper-V futtató fiókok tulajdonságainak beolvasása |
> | Műveletek | Microsoft. OffAzure/HyperVSites/használat/olvasás | Egy Hyper-V webhely használatának beolvasása |
> | Műveletek | Microsoft. OffAzure/HyperVSites/Write | A Hyper-V-hely létrehozása vagy frissítése |
> | Műveletek | Microsoft. OffAzure/Operations/READ | Beolvassa a feltett műveleteket |
> | Műveletek | Microsoft. OffAzure/regisztráció/művelet | Előfizetés regisztrálása a Microsoft. OffAzure erőforrás-szolgáltatónál |
> | Műveletek | Microsoft. OffAzure/ServerSites/feladatok/olvasás | Egy kiszolgálói feladat tulajdonságainak beolvasása |
> | Műveletek | Microsoft. OffAzure/ServerSites/Machines/READ | A kiszolgálói gépek tulajdonságainak beolvasása |
> | Műveletek | Microsoft. OffAzure/ServerSites/Machines/Write | Kiszolgálói gépek tulajdonságainak megírása |
> | Műveletek | Microsoft. OffAzure/ServerSites/operationsstatus/READ | Egy kiszolgáló műveleti állapotának tulajdonságainak beolvasása |
> | Műveletek | Microsoft. OffAzure/ServerSites/READ | Egy kiszolgáló-hely tulajdonságainak beolvasása |
> | Műveletek | Microsoft. OffAzure/ServerSites/frissítés/művelet | Az objektumok frissítése egy kiszolgálói helyen belül |
> | Műveletek | Microsoft. OffAzure/ServerSites/runasaccounts/READ | A kiszolgálói futtató fiókok tulajdonságainak beolvasása |
> | Műveletek | Microsoft. OffAzure/ServerSites/használat/olvasás | Egy kiszolgálói webhely használatának beolvasása |
> | Műveletek | Microsoft. OffAzure/ServerSites/Write | A kiszolgáló helyének létrehozása vagy frissítése |
> | Műveletek | Microsoft. OffAzure/VMwareSites/delete | A VMware-hely törlése |
> | Műveletek | Microsoft. OffAzure/VMwareSites/healthsummary/READ | A VMware-erőforrás állapot-összefoglalásának beolvasása |
> | Műveletek | Microsoft. OffAzure/VMwareSites/feladatok/olvasás | VMware-feladatok tulajdonságainak beolvasása |
> | Műveletek | Microsoft. OffAzure/VMwareSites/Machines/READ | VMware-gépek tulajdonságainak beolvasása |
> | Műveletek | Microsoft. OffAzure/VMwareSites/Machines/Start/Action | VMware-gépek elindítása |
> | Műveletek | Microsoft. OffAzure/VMwareSites/Machines/leállítás/művelet | Leállítja a VMware-gépeket |
> | Műveletek | Microsoft. OffAzure/VMwareSites/operationsstatus/READ | Egy VMware-műveleti állapot tulajdonságainak beolvasása |
> | Műveletek | Microsoft. OffAzure/VMwareSites/READ | Egy VMware-hely tulajdonságainak beolvasása |
> | Műveletek | Microsoft. OffAzure/VMwareSites/frissítés/művelet | Egy VMware-helyen található objektumok frissítése |
> | Műveletek | Microsoft. OffAzure/VMwareSites/runasaccounts/READ | VMware futtató fiókok tulajdonságainak beolvasása |
> | Műveletek | Microsoft. OffAzure/VMwareSites/használat/olvasás | VMware-webhely használatának beolvasása |
> | Műveletek | Microsoft. OffAzure/VMwareSites/vCenter/READ | Egy VMware-vCenter tulajdonságainak beolvasása |
> | Műveletek | Microsoft. OffAzure/VMwareSites/vCenter/Write | A VMware-vCenter létrehozása vagy frissítése |
> | Műveletek | Microsoft. OffAzure/VMwareSites/Write | A VMware-hely létrehozása vagy frissítése |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. OperationalInsights/linkTargets/READ | Felsorolja azokat a meglévő fiókokat, amelyek nincsenek Azure-előfizetéshez társítva. Ha ezt az Azure-előfizetést egy munkaterülethez szeretné kapcsolni, használja a művelet által visszaadott ügyfél-azonosítót a munkaterület létrehozása művelet ügyfél-azonosító tulajdonságában. |
> | Műveletek | Microsoft. operationalinsights/Operations/READ | Felsorolja az összes rendelkezésre álló OperationalInsights REST API-műveletet. |
> | Műveletek | Microsoft. operationalinsights/regisztráció/művelet | Rergisters az előfizetést. |
> | Műveletek | Microsoft. OperationalInsights/regisztráció/művelet | Előfizetés regisztrálása erőforrás-szolgáltatóhoz. |
> | Műveletek | Microsoft. operationalinsights/regisztráció/művelet | Az előfizetés regisztrációjának törlése. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/elemzés/lekérdezés/művelet | Keresés az új motor használatával. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/elemzés/lekérdezés/séma/olvasás | Keresési séma v2-es verziójának beolvasása. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/API/lekérdezés/művelet | Keresés az új motor használatával. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/API/lekérdezés/séma/olvasás | Keresési séma v2-es verziójának beolvasása. |
> | Műveletek | Microsoft. OperationalInsights/munkaterület/configurationScopes/delete | Konfigurációs hatókör törlése |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/configurationScopes/olvasás | Konfiguráció hatókörének beolvasása |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/configurationScopes/írás | Konfigurációs hatókör beállítása |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/adatforrások/törlés | Munkaterületen lévő adatforrások törlése. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/adatforrások/olvasás | Munkaterületen lévő adatforrások beolvasása. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/adatforrások/írás | Adatforrások létrehozása/frissítése egy munkaterület alatt. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/törlés | Töröl egy munkaterületet. Ha a munkaterületet egy meglévő munkaterülethez csatolták a létrehozáskor, akkor az ahhoz csatolt munkaterületet nem törli a rendszer. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/átjárók/törlés | Eltávolítja a munkaterülethez konfigurált átjárót. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/generateregistrationcertificate/művelet | Létrehoz egy regisztrációs tanúsítványt a munkaterülethez. Ez a tanúsítvány a Microsoft System Center Operation Manager és a munkaterület összekapcsolására szolgál. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/intelligencepacks/letiltás/művelet | Letiltja az adott munkaterülethez tartozó intelligencia-csomagokat. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/intelligencepacks/engedélyezés/művelet | Egy adott munkaterülethez tartozó intelligencia-csomag engedélyezése. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/intelligencepacks/olvasás | Felsorolja az adott munkaterület számára látható összes intelligenciát, valamint azt is, hogy a csomag engedélyezve van-e vagy le van-e tiltva az adott munkaterületen. |
> | Műveletek | Microsoft. OperationalInsights/munkaterület/linkedServices/delete | Társított szolgáltatások törlése az adott munkaterületen. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/linkedServices/olvasás | Társított szolgáltatások beolvasása az adott munkaterületen. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/linkedServices/írás | Társított szolgáltatások létrehozása/frissítése az adott munkaterületen. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/Listkeys műveletének beolvasása/művelet | Lekéri a munkaterület listájának kulcsait. Ezek a kulcsok a Microsoft Operational Insights-ügynökök munkaterülethez való összekapcsolására szolgálnak. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/Listkeys műveletének beolvasása/olvasás | Lekéri a munkaterület listájának kulcsait. Ezek a kulcsok a Microsoft Operational Insights-ügynökök munkaterülethez való összekapcsolására szolgálnak. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/managementGroups/olvasás | Beolvassa a munkaterülethez kapcsolódó System Center Operations Manager felügyeleti csoportok nevét és metaadatait. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/metricDefinitions/olvasás | Metrika-definíciók beolvasása a munkaterület területen |
> | Műveletek | Microsoft. OperationalInsights/munkaterület/notificationSettings/delete | A felhasználó értesítési beállításainak törlése a munkaterületen. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/notificationSettings/olvasás | A felhasználó értesítési beállításainak beolvasása a munkaterülethez. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/notificationSettings/írás | Adja meg a felhasználó értesítési beállításait a munkaterülethez. |
> | Műveletek | Microsoft. operationalinsights/munkaterületek/műveletek/olvasás | Egy OperationalInsights-munkaterület művelet állapotának beolvasása. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/kiürítés/művelet | Megadott adatok törlése a munkaterületről |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AADDomainServicesAccountLogon/olvasás | Adatok beolvasása a AADDomainServicesAccountLogon táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AADDomainServicesAccountManagement/olvasás | Adatok beolvasása a AADDomainServicesAccountManagement táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AADDomainServicesDirectoryServiceAccess/olvasás | Adatok beolvasása a AADDomainServicesDirectoryServiceAccess táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AADDomainServicesLogonLogoff/olvasás | Adatok beolvasása a AADDomainServicesLogonLogoff táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AADDomainServicesPolicyChange/olvasás | Adatok beolvasása a AADDomainServicesPolicyChange táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AADDomainServicesPrivilegeUse/olvasás | Adatok beolvasása a AADDomainServicesPrivilegeUse táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AADDomainServicesSystemSecurity/olvasás | Adatok beolvasása a AADDomainServicesSystemSecurity táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/ADAssessmentRecommendation/olvasás | Adatok beolvasása a ADAssessmentRecommendation táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AddonAzureBackupAlerts/olvasás | Adatok beolvasása a AddonAzureBackupAlerts táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AddonAzureBackupJobs/olvasás | Adatok beolvasása a AddonAzureBackupJobs táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AddonAzureBackupPolicy/olvasás | Adatok beolvasása a AddonAzureBackupPolicy táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AddonAzureBackupProtectedInstance/olvasás | Adatok beolvasása a AddonAzureBackupProtectedInstance táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AddonAzureBackupStorage/olvasás | Adatok beolvasása a AddonAzureBackupStorage táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/ADFActivityRun/olvasás | Adatok beolvasása a ADFActivityRun táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/ADFPipelineRun/olvasás | Adatok beolvasása a ADFPipelineRun táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/ADFTriggerRun/olvasás | Adatok beolvasása a ADFTriggerRun táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/ADReplicationResult/olvasás | Adatok beolvasása a ADReplicationResult táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/ADSecurityAssessmentRecommendation/olvasás | Adatok beolvasása a ADSecurityAssessmentRecommendation táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AegDeliveryFailureLogs/olvasás | Adatok beolvasása a AegDeliveryFailureLogs táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AegPublishFailureLogs/olvasás | Adatok beolvasása a AegPublishFailureLogs táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/riasztás/olvasás | Adatok beolvasása a riasztási táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AlertHistory/olvasás | Adatok beolvasása a AlertHistory táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AmlComputeClusterEvent/olvasás | Adatok beolvasása a AmlComputeClusterEvent táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AmlComputeClusterNodeEvent/olvasás | Adatok beolvasása a AmlComputeClusterNodeEvent táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AmlComputeJobEvent/olvasás | Adatok beolvasása a AmlComputeJobEvent táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/ApiManagementGatewayLogs/olvasás | Adatok beolvasása a ApiManagementGatewayLogs táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AppCenterError/olvasás | Adatok beolvasása a AppCenterError táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/ApplicationInsights/olvasás | Adatok beolvasása a ApplicationInsights táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AppPlatformLogsforSpring/olvasás | Adatok beolvasása a AppPlatformLogsforSpring táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AppPlatformSystemLogs/olvasás | Adatok beolvasása a AppPlatformSystemLogs táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AppServiceAppLogs/olvasás | Adatok beolvasása a AppServiceAppLogs táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AppServiceAuditLogs/olvasás | Adatok beolvasása a AppServiceAuditLogs táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AppServiceConsoleLogs/olvasás | Adatok beolvasása a AppServiceConsoleLogs táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AppServiceEnvironmentPlatformLogs/olvasás | Adatok beolvasása a AppServiceEnvironmentPlatformLogs táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AppServiceHTTPLogs/olvasás | Adatok beolvasása a AppServiceHTTPLogs táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AuditLogs/olvasás | Adatok beolvasása a AuditLogs táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AutoscaleEvaluationsLog/olvasás | Adatok beolvasása a AutoscaleEvaluationsLog táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AutoscaleScaleActionsLog/olvasás | Adatok beolvasása a AutoscaleScaleActionsLog táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AWSCloudTrail/olvasás | Adatok beolvasása a AWSCloudTrail táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AzureActivity/olvasás | Adatok beolvasása a AzureActivity táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AzureAssessmentRecommendation/olvasás | Adatok beolvasása a AzureAssessmentRecommendation táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AzureMetrics/olvasás | Adatok beolvasása a AzureMetrics táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/BaiClusterEvent/olvasás | Adatok beolvasása a BaiClusterEvent táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/BaiClusterNodeEvent/olvasás | Adatok beolvasása a BaiClusterNodeEvent táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/BaiJobEvent/olvasás | Adatok beolvasása a BaiJobEvent táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/BlockchainApplicationLog/olvasás | Adatok beolvasása a BlockchainApplicationLog táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/BlockchainProxyLog/olvasás | Adatok beolvasása a BlockchainProxyLog táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/BoundPort/olvasás | Adatok beolvasása a BoundPort táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/CommonSecurityLog/olvasás | Adatok beolvasása a CommonSecurityLog táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/ComputerGroup/olvasás | Adatok beolvasása a ComputerGroup táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/konfigurációváltozás/olvasás | Adatok beolvasása a konfigurációváltozás táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/ConfigurationData/olvasás | Adatok beolvasása a ConfigurationData táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/ContainerImageInventory/olvasás | Adatok beolvasása a ContainerImageInventory táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/ContainerInventory/olvasás | Adatok beolvasása a ContainerInventory táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/ContainerLog/olvasás | Adatok beolvasása a ContainerLog táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/ContainerNodeInventory/olvasás | Adatok beolvasása a ContainerNodeInventory táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/ContainerRegistryLoginEvents/olvasás | Adatok beolvasása a ContainerRegistryLoginEvents táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/ContainerRegistryRepositoryEvents/olvasás | Adatok beolvasása a ContainerRegistryRepositoryEvents táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/ContainerServiceLog/olvasás | Adatok beolvasása a ContainerServiceLog táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/CoreAzureBackup/olvasás | Adatok beolvasása a CoreAzureBackup táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DatabricksAccounts/olvasás | Adatok beolvasása a DatabricksAccounts táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DatabricksClusters/olvasás | Adatok beolvasása a DatabricksClusters táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DatabricksDBFS/olvasás | Adatok beolvasása a DatabricksDBFS táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DatabricksInstancePools/olvasás | Adatok beolvasása a DatabricksInstancePools táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DatabricksJobs/olvasás | Adatok beolvasása a DatabricksJobs táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DatabricksNotebook/olvasás | Adatok beolvasása a DatabricksNotebook táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DatabricksSecrets/olvasás | Adatok beolvasása a DatabricksSecrets táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DatabricksSQLPermissions/olvasás | Adatok beolvasása a DatabricksSQLPermissions táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DatabricksSSH/olvasás | Adatok beolvasása a DatabricksSSH táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DatabricksTables/olvasás | Adatok beolvasása a DatabricksTables táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DatabricksWorkspace/olvasás | Adatok beolvasása a DatabricksWorkspace táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DeviceAppCrash/olvasás | Adatok beolvasása a DeviceAppCrash táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DeviceAppLaunch/olvasás | Adatok beolvasása a DeviceAppLaunch táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DeviceCalendar/olvasás | Adatok beolvasása a DeviceCalendar táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DeviceCleanup/olvasás | Adatok beolvasása a DeviceCleanup táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DeviceConnectSession/olvasás | Adatok beolvasása a DeviceConnectSession táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DeviceEtw/olvasás | Adatok beolvasása a DeviceEtw táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DeviceHardwareHealth/olvasás | Adatok beolvasása a DeviceHardwareHealth táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DeviceHealth/olvasás | Adatok beolvasása a DeviceHealth táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DeviceHeartbeat/olvasás | Adatok beolvasása a DeviceHeartbeat táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DeviceSkypeHeartbeat/olvasás | Adatok beolvasása a DeviceSkypeHeartbeat táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DeviceSkypeSignIn/olvasás | Adatok beolvasása a DeviceSkypeSignIn táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DeviceSleepState/olvasás | Adatok beolvasása a DeviceSleepState táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DHAppFailure/olvasás | Adatok beolvasása a DHAppFailure táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DHAppReliability/olvasás | Adatok beolvasása a DHAppReliability táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DHCPActivity/olvasás | Adatok beolvasása a DHCPActivity táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DHDriverReliability/olvasás | Adatok beolvasása a DHDriverReliability táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DHLogonFailures/olvasás | Adatok beolvasása a DHLogonFailures táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DHLogonMetrics/olvasás | Adatok beolvasása a DHLogonMetrics táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DHOSCrashData/olvasás | Adatok beolvasása a DHOSCrashData táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DHOSReliability/olvasás | Adatok beolvasása a DHOSReliability táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DHWipAppLearning/olvasás | Adatok beolvasása a DHWipAppLearning táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DnsEvents/olvasás | Adatok beolvasása a DnsEvents táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DnsInventory/olvasás | Adatok beolvasása a DnsInventory táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/ETWEvent/olvasás | Adatok beolvasása a ETWEvent táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/esemény/olvasás | Adatok beolvasása az Event táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/ExchangeAssessmentRecommendation/olvasás | Adatok beolvasása a ExchangeAssessmentRecommendation táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/ExchangeOnlineAssessmentRecommendation/olvasás | Adatok beolvasása a ExchangeOnlineAssessmentRecommendation táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/FailedIngestion/olvasás | Adatok beolvasása a FailedIngestion táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/FunctionAppLogs/olvasás | Adatok beolvasása a FunctionAppLogs táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/szívverés/olvasás | Adatok beolvasása a szívverési táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/HuntingBookmark/olvasás | Adatok beolvasása a HuntingBookmark táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/IISAssessmentRecommendation/olvasás | Adatok beolvasása a IISAssessmentRecommendation táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/InboundConnection/olvasás | Adatok beolvasása a InboundConnection táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/InsightsMetrics/olvasás | Adatok beolvasása a InsightsMetrics táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/IntuneAuditLogs/olvasás | Adatok beolvasása a IntuneAuditLogs táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/IntuneDeviceComplianceOrg/olvasás | Adatok beolvasása a IntuneDeviceComplianceOrg táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/IntuneOperationalLogs/olvasás | Adatok beolvasása a IntuneOperationalLogs táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/KubeEvents/olvasás | Adatok beolvasása a KubeEvents táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/KubeHealth/olvasás | Adatok beolvasása a KubeHealth táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/KubeMonAgentEvents/olvasás | Adatok beolvasása a KubeMonAgentEvents táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/KubeNodeInventory/olvasás | Adatok beolvasása a KubeNodeInventory táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/KubePodInventory/olvasás | Adatok beolvasása a KubePodInventory táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/KubeServices/olvasás | Adatok beolvasása a KubeServices táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/LinuxAuditLog/olvasás | Adatok beolvasása a LinuxAuditLog táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAApplication/olvasás | Adatok beolvasása a MAApplication táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAApplicationHealth/olvasás | Adatok beolvasása a MAApplicationHealth táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAApplicationHealthAlternativeVersions/olvasás | Adatok beolvasása a MAApplicationHealthAlternativeVersions táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAApplicationHealthIssues/olvasás | Adatok beolvasása a MAApplicationHealthIssues táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAApplicationInstance/olvasás | Adatok beolvasása a MAApplicationInstance táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAApplicationInstanceReadiness/olvasás | Adatok beolvasása a MAApplicationInstanceReadiness táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAApplicationReadiness/olvasás | Adatok beolvasása a MAApplicationReadiness táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MADeploymentPlan/olvasás | Adatok beolvasása a MADeploymentPlan táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MADevice/olvasás | Adatok beolvasása a MADevice táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MADeviceNotEnrolled/olvasás | Adatok beolvasása a MADeviceNotEnrolled táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MADeviceNRT/olvasás | Adatok beolvasása a MADeviceNRT táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MADevicePnPHealth/olvasás | Adatok beolvasása a MADevicePnPHealth táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MADevicePnPHealthAlternativeVersions/olvasás | Adatok beolvasása a MADevicePnPHealthAlternativeVersions táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MADevicePnPHealthIssues/olvasás | Adatok beolvasása a MADevicePnPHealthIssues táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MADeviceReadiness/olvasás | Adatok beolvasása a MADeviceReadiness táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MADriverInstanceReadiness/olvasás | Adatok beolvasása a MADriverInstanceReadiness táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MADriverReadiness/olvasás | Adatok beolvasása a MADriverReadiness táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeAddin/olvasás | Adatok beolvasása a MAOfficeAddin táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeAddinEntityHealth/olvasás | Adatok beolvasása a MAOfficeAddinEntityHealth táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeAddinHealth/olvasás | Adatok beolvasása a MAOfficeAddinHealth táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeAddinHealthEventNRT/olvasás | Adatok beolvasása a MAOfficeAddinHealthEventNRT táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeAddinHealthIssues/olvasás | Adatok beolvasása a MAOfficeAddinHealthIssues táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeAddinInstance/olvasás | Adatok beolvasása a MAOfficeAddinInstance táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeAddinInstanceReadiness/olvasás | Adatok beolvasása a MAOfficeAddinInstanceReadiness táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeAddinReadiness/olvasás | Adatok beolvasása a MAOfficeAddinReadiness táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeApp/olvasás | Adatok beolvasása a MAOfficeApp táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeAppCrashesNRT/olvasás | Adatok beolvasása a MAOfficeAppCrashesNRT táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeAppHealth/olvasás | Adatok beolvasása a MAOfficeAppHealth táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeAppInstance/olvasás | Adatok beolvasása a MAOfficeAppInstance táblából |
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
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/olvasás | Lekérdezések futtatása a munkaterület összes adatán |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/ReservedCommonFields/olvasás | Adatok beolvasása a ReservedCommonFields táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/SCCMAssessmentRecommendation/olvasás | Adatok beolvasása a SCCMAssessmentRecommendation táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/SCOMAssessmentRecommendation/olvasás | Adatok beolvasása a SCOMAssessmentRecommendation táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/SecurityAlert/olvasás | Adatok beolvasása a SecurityAlert táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/SecurityBaseline/olvasás | Adatok beolvasása a SecurityBaseline táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/SecurityBaselineSummary/olvasás | Adatok beolvasása a SecurityBaselineSummary táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/SecurityDetection/olvasás | Adatok beolvasása a SecurityDetection táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/SecurityEvent/olvasás | Adatok beolvasása a SecurityEvent táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/SecurityIoTRawEvent/olvasás | Adatok beolvasása a SecurityIoTRawEvent táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/SecurityRecommendation/olvasás | Adatok beolvasása a SecurityRecommendation táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/ServiceFabricOperationalEvent/olvasás | Adatok beolvasása a ServiceFabricOperationalEvent táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/ServiceFabricReliableActorEvent/olvasás | Adatok beolvasása a ServiceFabricReliableActorEvent táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/ServiceFabricReliableServiceEvent/olvasás | Adatok beolvasása a ServiceFabricReliableServiceEvent táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/SfBAssessmentRecommendation/olvasás | Adatok beolvasása a SfBAssessmentRecommendation táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/SfBOnlineAssessmentRecommendation/olvasás | Adatok beolvasása a SfBOnlineAssessmentRecommendation táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/SharePointOnlineAssessmentRecommendation/olvasás | Adatok beolvasása a SharePointOnlineAssessmentRecommendation táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/SignalRServiceDiagnosticLogs/olvasás | Adatok beolvasása a SignalRServiceDiagnosticLogs táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/SigninLogs/olvasás | Adatok beolvasása a SigninLogs táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/SPAssessmentRecommendation/olvasás | Adatok beolvasása a SPAssessmentRecommendation táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/SQLAssessmentRecommendation/olvasás | Adatok beolvasása a SQLAssessmentRecommendation táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/SQLQueryPerformance/olvasás | Adatok beolvasása a SQLQueryPerformance táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/SqlThreatProtectionLoginAudits/olvasás | Adatok beolvasása a SqlThreatProtectionLoginAudits táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/SqlVulnerabilityAssessmentResult/olvasás | Adatok beolvasása a SqlVulnerabilityAssessmentResult táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/StorageBlobLogs/olvasás | Adatok beolvasása a StorageBlobLogs táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/StorageFileLogs/olvasás | Adatok beolvasása a StorageFileLogs táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/StorageQueueLogs/olvasás | Adatok beolvasása a StorageQueueLogs táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/StorageTableLogs/olvasás | Adatok beolvasása a StorageTableLogs táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/SucceededIngestion/olvasás | Adatok beolvasása a SucceededIngestion táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/syslog/olvasás | Adatok beolvasása a syslog-táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/SysmonEvent/olvasás | Adatok beolvasása a SysmonEvent táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/táblák. egyéni/olvasási | Adatok olvasása bármely egyéni naplóból |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/ThreatIntelligenceIndicator/olvasás | Adatok beolvasása a ThreatIntelligenceIndicator táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/UAApp/olvasás | Adatok beolvasása a UAApp táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/UAComputer/olvasás | Adatok beolvasása a UAComputer táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/UAComputerRank/olvasás | Adatok beolvasása a UAComputerRank táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/UADriver/olvasás | Adatok beolvasása a UADriver táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/UADriverProblemCodes/olvasás | Adatok beolvasása a UADriverProblemCodes táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/UAFeedback/olvasás | Adatok beolvasása a UAFeedback táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/UAHardwareSecurity/olvasás | Adatok beolvasása a UAHardwareSecurity táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/UAIESiteDiscovery/olvasás | Adatok beolvasása a UAIESiteDiscovery táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/UAOfficeAddIn/olvasás | Adatok beolvasása a UAOfficeAddIn táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/UAProposedActionPlan/olvasás | Adatok beolvasása a UAProposedActionPlan táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/UASysReqIssue/olvasás | Adatok beolvasása a UASysReqIssue táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/UAUpgradedComputer/olvasás | Adatok beolvasása a UAUpgradedComputer táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/frissítés/olvasás | Adatok beolvasása a frissítési táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/UpdateRunProgress/olvasás | Adatok beolvasása a UpdateRunProgress táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/updateSummary típusú/olvasás | Adatok beolvasása a updateSummary típusú táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/használat/olvasás | Adatok beolvasása a használati táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/VMBoundPort/olvasás | Adatok beolvasása a VMBoundPort táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/VMComputer/olvasás | Adatok beolvasása a VMComputer táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/VMConnection/olvasás | Adatok beolvasása a VMConnection táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/VMProcess/olvasás | Adatok beolvasása a VMProcess táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/W3CIISLog/olvasás | Adatok beolvasása a W3CIISLog táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/WaaSDeploymentStatus/olvasás | Adatok beolvasása a WaaSDeploymentStatus táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/WaaSInsiderStatus/olvasás | Adatok beolvasása a WaaSInsiderStatus táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/WaaSUpdateStatus/olvasás | Adatok beolvasása a WaaSUpdateStatus táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/WDAVStatus/olvasás | Adatok beolvasása a WDAVStatus táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/WDAVThreat/olvasás | Adatok beolvasása a WDAVThreat táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/WindowsClientAssessmentRecommendation/olvasás | Adatok beolvasása a WindowsClientAssessmentRecommendation táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/WindowsEvent/olvasás | Adatok beolvasása a WindowsEvent táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/WindowsFirewall/olvasás | Adatok beolvasása a WindowsFirewall táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/WindowsServerAssessmentRecommendation/olvasás | Adatok beolvasása a WindowsServerAssessmentRecommendation táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/WireData/olvasás | Adatok beolvasása a WireData táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/WorkloadMonitoringPerf/olvasás | Adatok beolvasása a WorkloadMonitoringPerf táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/WUDOAggregatedStatus/olvasás | Adatok beolvasása a WUDOAggregatedStatus táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/lekérdezés/WUDOStatus/olvasás | Adatok beolvasása a WUDOStatus táblából |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/olvasás | Meglévő munkaterület beolvasása |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/regeneratesharedkey/művelet | A megadott munkaterület megosztott kulcsának újralétrehozása |
> | Műveletek | Microsoft. operationalinsights/munkaterületek/szabályok/olvasás | Az összes riasztási szabály beolvasása. |
> | Műveletek | Microsoft. OperationalInsights/munkaterület/savedSearches/delete | Töröl egy mentett keresési lekérdezést |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/savedSearches/olvasás | Mentett keresési lekérdezés beolvasása |
> | Műveletek | Microsoft. operationalinsights/munkaterületek/savedsearches/eredmények/olvasás | Mentett keresések eredményeinek beolvasása. Elavult |
> | Műveletek | Microsoft. operationalinsights/munkaterületek/savedsearches/ütemtervek/műveletek/törlés | Ütemezett keresési műveletek törlése. |
> | Műveletek | Microsoft. operationalinsights/munkaterületek/savedsearches/ütemtervek/műveletek/olvasás | Ütemezett keresési műveletek beolvasása. |
> | Műveletek | Microsoft. operationalinsights/munkaterületek/savedsearches/ütemtervek/műveletek/írás | Ütemezett keresési műveletek létrehozása vagy frissítése. |
> | Műveletek | Microsoft. operationalinsights/munkaterületek/savedsearches/ütemtervek/törlés | Ütemezett keresések törlése. |
> | Műveletek | Microsoft. operationalinsights/munkaterületek/savedsearches/ütemtervek/olvasás | Ütemezett keresések beolvasása. |
> | Műveletek | Microsoft. operationalinsights/munkaterületek/savedsearches/ütemterv/írás | Ütemezett keresések létrehozása vagy frissítése. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/savedSearches/írás | Mentett keresési lekérdezés létrehozása |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/séma/olvasás | Lekéri a munkaterület keresési sémáját.  A keresési séma tartalmazza a kitett mezőket és azok típusait. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/keresés/művelet | Keresési lekérdezés végrehajtása |
> | Műveletek | Microsoft. operationalinsights/munkaterületek/keresés/olvasás | Keresési eredmények beolvasása. Elavult. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/sharedKeys/művelet | Lekéri a munkaterület megosztott kulcsait. Ezek a kulcsok a Microsoft Operational Insights-ügynökök munkaterülethez való összekapcsolására szolgálnak. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/sharedKeys/olvasás | Lekéri a munkaterület megosztott kulcsait. Ezek a kulcsok a Microsoft Operational Insights-ügynökök munkaterülethez való összekapcsolására szolgálnak. |
> | Műveletek | Microsoft. OperationalInsights/munkaterület/storageinsightconfigs/delete | Törli a tárolási konfigurációt. Ezzel leállítja a Microsoft Operational Insights az adatoknak a Storage-fiókból való beolvasását. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/storageinsightconfigs/olvasás | Egy tárolási konfiguráció beolvasása. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/storageinsightconfigs/írás | Új tárolási konfiguráció létrehozása. Ezek a konfigurációk egy meglévő Storage-fiókban lévő hely adatainak lekérésére szolgálnak. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/upgradetranslationfailures/olvasás | A keresés a munkaterületre vonatkozó verziófrissítési sikertelen fordítási naplójának beolvasása |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/használati adatok/olvasás | Lekéri egy munkaterület használati adatait, beleértve a munkaterület által olvasott adatok mennyiségét. |
> | Műveletek | Microsoft. OperationalInsights/munkaterületek/írás | Létrehoz egy új munkaterületet vagy egy meglévő munkaterületre mutató hivatkozásokat úgy, hogy megadja az ügyfél azonosítóját a meglévő munkaterületen. |

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. OperationsManagement/managementAssociations/delete | Meglévő felügyeleti társítás törlése |
> | Műveletek | Microsoft. OperationsManagement/managementAssociations/READ | Meglévő felügyeleti társítás beolvasása |
> | Műveletek | Microsoft. OperationsManagement/managementAssociations/Write | Új felügyeleti társítás létrehozása |
> | Műveletek | Microsoft. OperationsManagement/managementConfigurations/delete | Meglévő felügyeleti konfiguráció törlése |
> | Műveletek | Microsoft. OperationsManagement/managementConfigurations/READ | Meglévő felügyeleti konfiguráció beolvasása |
> | Műveletek | Microsoft. OperationsManagement/managementConfigurations/Write | Új felügyeleti konfiguráció létrehozása |
> | Műveletek | Microsoft. OperationsManagement/regisztráció/művelet | Előfizetés regisztrálása erőforrás-szolgáltatóhoz. |
> | Műveletek | Microsoft. OperationsManagement/megoldások/törlés | Meglévő OMS-megoldás törlése |
> | Műveletek | Microsoft. OperationsManagement/megoldások/olvasás | OMS-megoldás beolvasása |
> | Műveletek | Microsoft. OperationsManagement/megoldások/írás | Új OMS-megoldás létrehozása |

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. PolicyInsights/asyncOperationResults/READ | Az aszinkron művelet eredményének beolvasása. |
> | DataAction | Microsoft. PolicyInsights/checkDataPolicyCompliance/művelet | Egy adott összetevő megfelelőségi állapotának ellenőrzése adatszabályzatok szerint. |
> | Műveletek | Microsoft. PolicyInsights/Operations/READ | A Microsoft. PolicyInsights névtérben támogatott műveletek beolvasása |
> | DataAction | Microsoft. PolicyInsights/policyEvents/logDataEvents/művelet | Naplózza az erőforrás-összetevő házirendjének eseményeit. |
> | Műveletek | Microsoft. PolicyInsights/policyEvents/queryResults/művelet | A házirend eseményeivel kapcsolatos információk lekérdezése. |
> | Műveletek | Microsoft. PolicyInsights/policyEvents/queryResults/READ | A házirend eseményeivel kapcsolatos információk lekérdezése. |
> | Műveletek | Microsoft. PolicyInsights/policyMetadata/READ | Házirend metaadat-erőforrásainak beolvasása. |
> | Műveletek | Microsoft. PolicyInsights/policyStates/queryResults/művelet | A házirend állapotával kapcsolatos információk lekérdezése. |
> | Műveletek | Microsoft. PolicyInsights/policyStates/queryResults/READ | A házirend állapotával kapcsolatos információk lekérdezése. |
> | Műveletek | Microsoft. PolicyInsights/policyStates/összefoglalás/művelet | A szabályzat legutóbbi állapotával kapcsolatos összegző információk lekérdezése. |
> | Műveletek | Microsoft. PolicyInsights/policyStates/összefoglalás/olvasás | A szabályzat legutóbbi állapotával kapcsolatos összegző információk lekérdezése. |
> | Műveletek | Microsoft. PolicyInsights/policyStates/triggerEvaluation/művelet | Új megfelelőségi értékelést indít a kiválasztott hatókörhöz. |
> | Műveletek | Microsoft. PolicyInsights/policyTrackedResources/queryResults/READ | A DeployIfNotExists-szabályzatok által igényelt erőforrásokra vonatkozó információk lekérdezése. |
> | Műveletek | Microsoft. PolicyInsights/regisztráció/művelet | Regisztrálja a Microsoft Policy bepillantást biztosító erőforrás-szolgáltatót, és engedélyezi a műveleteket. |
> | Műveletek | Microsoft. PolicyInsights/szervizelés/megszakítás/művelet | A folyamatban lévő Microsoft-szabályzatok szervizelésének megszakítása. |
> | Műveletek | Microsoft. PolicyInsights/szervizelés/törlés | Házirend-szervizelés törlése. |
> | Műveletek | Microsoft. PolicyInsights/szervizelés/listDeployments/olvasás | Felsorolja a házirendek szervizeléséhez szükséges központi telepítéseket. |
> | Műveletek | Microsoft. PolicyInsights/szervizelés/olvasás | Házirend szervizelésének beolvasása. |
> | Műveletek | Microsoft. PolicyInsights/szervizelés/írás | Microsoft Policy szervizelések létrehozása vagy frissítése. |
> | Műveletek | Microsoft. PolicyInsights/regisztráció/művelet | A Microsoft Policy-erőforrás-szolgáltató regisztrációjának törlése. |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Portal/konzolok/törlés | Eltávolítja a Cloud Shell példányt. |
> | Műveletek | Microsoft. Portal/konzolok/olvasás | A Cloud Shell példány beolvasása. |
> | Műveletek | Microsoft. Portal/konzolok/írás | Cloud Shell példány létrehozása vagy frissítése. |
> | Műveletek | Microsoft. Portal/irányítópultok/törlés | Eltávolítja az irányítópultot az előfizetésből. |
> | Műveletek | Microsoft. Portal/irányítópultok/olvasás | Az előfizetés irányítópultjának beolvasása. |
> | Műveletek | Microsoft. Portal/irányítópultok/írás | Irányítópult hozzáadása vagy módosítása előfizetéshez. |
> | Műveletek | Microsoft. Portal/regisztráció/művelet | Regisztráció a portálra |
> | Műveletek | Microsoft. Portal/usersettings/delete | Eltávolítja a Cloud Shell felhasználói beállításait. |
> | Műveletek | Microsoft. Portal/usersettings/READ | A Cloud Shell felhasználói beállításainak beolvasása. |
> | Műveletek | Microsoft. Portal/usersettings/írás | Cloud Shell felhasználói beállítás létrehozása vagy frissítése. |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. PowerBIDedicated/kapacitások/törlés | Törli a Power BI dedikált kapacitást. |
> | Műveletek | Microsoft. PowerBIDedicated/kapacitások/olvasás | A megadott Power BI dedikált kapacitás információinak beolvasása. |
> | Műveletek | Microsoft. PowerBIDedicated/kapacitások/folytatás/művelet | Folytatja a kapacitást. |
> | Műveletek | Microsoft. PowerBIDedicated/kapacitás/SKU/READ | Rendelkezésre álló SKU-információk beolvasása a kapacitáshoz |
> | Műveletek | Microsoft. PowerBIDedicated/kapacitások/felfüggesztés/művelet | Felfüggeszti a kapacitást. |
> | Műveletek | Microsoft. PowerBIDedicated/kapacitás/írás | Létrehozza vagy frissíti a megadott Power BI dedikált kapacitást. |
> | Műveletek | Microsoft. PowerBIDedicated/Locations/checkNameAvailability/Action | Ellenőrzi, hogy a megadott Power BI dedikált kapacitás neve érvényes-e, és nincs-e használatban. |
> | Műveletek | Microsoft. PowerBIDedicated/Locations/operationresults/READ | A megadott műveleti eredmény információinak beolvasása. |
> | Műveletek | Microsoft. PowerBIDedicated/Locations/operationstatuses/READ | Lekéri a megadott műveleti állapot adatait. |
> | Műveletek | Microsoft. PowerBIDedicated/Operations/READ | A műveletek információinak lekérése |
> | Műveletek | Microsoft. PowerBIDedicated/regisztráció/művelet | Regisztrálja Power BI dedikált erőforrás-szolgáltatót. |
> | Műveletek | Microsoft. PowerBIDedicated/SKU/READ | A SKU-ra vonatkozó információk beolvasása |

## <a name="microsoftrecoveryservices"></a>Microsoft. Recoveryservices szolgáltatónál

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Locations/allocatedStamp/READ | A lefoglalt bélyegző beolvasása a szolgáltatás által használt belső művelet |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Locations/allocateStamp/Action | A AllocateStamp a szolgáltatás által használt belső művelet |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Locations/backupPreValidateProtection/Action |  |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Locations/backupProtectedItem/Write | Védett elemek biztonsági másolatának létrehozása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Locations/backupProtectedItems/READ | Az összes védett elem listáját adja vissza. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Locations/backupStatus/Action | Recovery Services-tárolók biztonsági mentési állapotának keresése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Locations/backupValidateFeatures/Action | Funkciók ellenőrzése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Locations/checkNameAvailability/Action | Ellenőrizze, hogy az erőforrás neve rendelkezésre állása egy API-e, hogy elérhető-e az erőforrás neve |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Locations/operationStatus/READ | Egy adott művelet műveleti állapotának beolvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Operations/READ | A művelet egy erőforrás-szolgáltató műveleteinek listáját adja vissza. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/regisztráció/művelet | Az adott erőforrás-szolgáltatóhoz tartozó előfizetés regisztrálása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupconfig/READ | Recovery Services-tároló konfigurációjának visszaadása. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/tárolók/backupconfig/írás | Recovery Services tár konfigurációjának frissítése. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupEncryptionConfigs/READ | A biztonsági mentési erőforrás titkosítási konfigurációjának beolvasása. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/tárolók/backupEncryptionConfigs/írás | A biztonsági mentési erőforrás titkosítási konfigurációjának frissítése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupEngines/READ | A tár összes regisztrált biztonságimásolat-felügyeleti kiszolgálóját adja vissza. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/backupProtectionIntent/delete | Biztonsági másolat védelmi leképezésének törlése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/backupProtectionIntent/READ | Biztonsági másolat védelmi szándékának beolvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/backupProtectionIntent/Write | Biztonsági másolat védelmi leképezésének létrehozása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/operationResults/READ | A művelet állapotának visszaadása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/operationsStatus/READ | A művelet állapotának visszaadása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/protectableContainers/READ | Az összes védhető tároló beolvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/protectionContainers/delete | A regisztrált tároló törlése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/backupFabrics/protectionContainers/lekérdezés/művelet | A tárolóban lévő számítási feladatok lekérdezése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/backupFabrics/protectionContainers/Items/READ | Tároló összes elemének beolvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/protectionContainers/operationResults/READ | A védelmi tárolón végrehajtott művelet eredményét kapja meg. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/protectionContainers/operationsStatus/READ | Lekéri a védelmi tárolón végrehajtott művelet állapotát. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/backupFabrics/protectionContainers/protectedItems/biztonsági mentés/művelet | A védett elemek biztonsági mentését végzi. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/protectionContainers/protectedItems/delete | Védett elemek törlése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/protectionContainers/protectedItems/operationResults/READ | A védett elemeken végrehajtott művelet eredményét kapja meg. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/protectionContainers/protectedItems/operationsStatus/READ | A védett elemeken végrehajtott művelet állapotát adja vissza. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/protectionContainers/protectedItems/READ | A védett tétel objektumának részleteit adja vissza. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/művelet | Azonnali elemek helyreállításának kiépítése védett elemek esetén |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/protectionContainers/protectedItems/recoveryPoints/READ | A védett elemek helyreállítási pontjainak beolvasása. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/Restore/Action | A védett elemek helyreállítási pontjainak visszaállítása. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/művelet | A védett elemek azonnali elemek helyreállításának visszavonása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/protectionContainers/protectedItems/Write | Védett elemek biztonsági másolatának létrehozása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/protectionContainers/READ | Az összes regisztrált tároló visszaadása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/protectionContainers/Write | Létrehoz egy regisztrált tárolót |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/backupFabrics/refreshContainers/művelet | Frissíti a tárolók listáját |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/backupJobs/megszakítás/művelet | Feladat megszakítása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupJobs/operationResults/READ | A feladatok műveletének eredményét adja vissza. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupJobs/operationsStatus/READ | A feladatok műveletének állapotát adja vissza. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupJobs/READ | Az összes feladatütemezés visszaadása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupJobsExport/művelet | Feladatok exportálása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupOperationResults/READ | Recovery Services-tár biztonsági mentési műveletének eredményét adja vissza. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupOperations/READ | Recovery Services-tároló biztonsági mentési műveletének állapotát adja vissza. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupPolicies/delete | Védelmi szabályzat törlése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupPolicies/operationResults/READ | A házirend-művelet eredményeinek beolvasása. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/backupPolicies/Operations/READ | Házirend-művelet állapotának beolvasása. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupPolicies/READ | Az összes védelmi szabályzat visszaadása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/tárolók/backupPolicies/írás | Védelmi szabályzat létrehozása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupProtectableItems/READ | Az összes védhető elem listáját adja vissza. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupProtectedItems/READ | Az összes védett elem listáját adja vissza. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupProtectionContainers/READ | Az előfizetéshez tartozó összes tárolót adja vissza. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupProtectionIntents/READ | Az összes biztonsági mentési védelmi leképezés listázása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupSecurityPIN/művelet | Recovery Services-tároló biztonsági PIN-kódjának beolvasása. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupstorageconfig/READ | Recovery Services tároló tárolási konfigurációját adja vissza. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/tárolók/backupstorageconfig/írás | Frissíti Recovery Services tár tárolási konfigurációját. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupUsageSummaries/READ | A védett elemek és a védett kiszolgálók összefoglalásait adja vissza egy Recovery Services számára. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/backupValidateOperation/művelet | A művelet érvényesítése védett elemen |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/tárolók/tanúsítványok/írás | Az erőforrás-tanúsítvány frissítése művelet frissíti az erőforrás/tároló hitelesítő adatait tartalmazó tanúsítványt. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/tárolók/törlés | A tár törlése művelet törli a "Vault" típusú megadott Azure-erőforrást. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/extendedInformation/delete | A kiterjesztett adatok beolvasása művelet lekérdezi egy objektum kiterjesztett adatait, amely a (z) típusú Azure-erőforrást jelképezi? |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/extendedInformation/READ | A kiterjesztett adatok beolvasása művelet lekérdezi egy objektum kiterjesztett adatait, amely a (z) típusú Azure-erőforrást jelképezi? |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/tárolók/extendedInformation/írás | A kiterjesztett adatok beolvasása művelet lekérdezi egy objektum kiterjesztett adatait, amely a (z) típusú Azure-erőforrást jelképezi? |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/monitoringAlerts/READ | Lekéri a Recovery Services-tároló riasztásait. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/tárolók/monitoringAlerts/írás | A riasztás feloldása. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/monitoringConfigurations/READ | A Recovery Services-tár értesítési konfigurációjának beolvasása. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/tárolók/monitoringConfigurations/írás | E-mail-értesítések konfigurálása a Recovery Services-tárolóhoz. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/tárolók/olvasás | A tár beolvasása művelet lekérdezi a Vault típusú Azure-erőforrást jelképező objektumot. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/registeredIdentities/delete | A tároló regisztrációjának törlése művelettel törölheti a tároló regisztrációját. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/registeredIdentities/operationResults/READ | A művelet eredményeinek beolvasása művelettel az aszinkron módon elküldött művelet műveleti állapota és eredménye olvasható be. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/registeredIdentities/READ | A tárolók beolvasása művelettel beolvashatók az adott erőforráshoz regisztrált tárolók. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/tárolók/registeredIdentities/írás | A szolgáltatás-tároló regisztrálása művelettel regisztrálhat egy tárolót a helyreállítási szolgáltatással. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationAlertSettings/READ | Riasztási beállítások beolvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/tárolók/replicationAlertSettings/írás | Riasztási beállítások létrehozása vagy frissítése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationEvents/READ | Események beolvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/checkConsistency/művelet | A háló konzisztenciájának ellenőrzése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/delete | Bármilyen háló törlése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/deployProcessServerImage/művelet | A Process Server lemezképének üzembe helyezése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/migratetoaad/művelet | Háló migrálása a HRE-be |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/operationresults/READ | Aszinkron művelet eredményeinek nyomon követése az erőforrás-hálón |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/READ | Bármilyen háló beolvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/reassociateGateway/művelet | Átjáró újrahozzárendelése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/eltávolítás/művelet | Háló eltávolítása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/renewcertificate/művelet | Háló tanúsítványának megújítása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationLogicalNetworks/READ | Bármely logikai hálózat beolvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationNetworks/READ | Bármilyen hálózat beolvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | Hálózati hozzárendelések törlése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationNetworks/replicationNetworkMappings/READ | Hálózati leképezések olvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationNetworks/replicationNetworkMappings/Write | Hálózati leképezések létrehozása vagy frissítése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/művelet | Védhető elemek felderítése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationProtectionContainers/operationresults/READ | Aszinkron művelet eredményeinek nyomon követése az erőforrás-védelmi tárolókban |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationProtectionContainers/READ | Védelmi tárolók beolvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/replicationProtectionContainers/Remove/Action | Védelmi tároló eltávolítása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/delete | Az áttelepítési elemek törlése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/Migrálás/művelet | Elemek átmigrálása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints/READ | Áttelepítési helyreállítási pontok olvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/operationresults/READ | Aszinkron művelet eredményeinek nyomon követése az erőforrás-áttelepítési elemeken |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/READ | Bármely áttelepítési elem olvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrate/művelet | Áttelepítés tesztelése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrateCleanup/művelet | Karbantartás áttelepíthetők |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/Write | Áttelepítési elemek létrehozása vagy frissítése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/READ | Bármely védhető elem olvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/addDisks/művelet | Lemezek hozzáadása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/művelet | Helyreállítási pont alkalmazása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | Védett elemek törlése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/művelet | Feladatátvétel véglegesítve |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/operationresults/READ | Aszinkron művelet eredményeinek nyomon követése az erőforrás által védett elemeken |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/művelet | Tervezett feladatátvétel |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/READ | Védett elemek beolvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/READ | Replikációs helyreállítási pontok olvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/eltávolítás/művelet | Védett elemek eltávolítása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/removeDisks/művelet | Lemezek eltávolítása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/művelet | Replikáció javítása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/ismételt védelem/művelet | Védett elemek ismételt védelme |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/ResolveHealthErrors/művelet |  |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/művelet | Visszajelzés elküldése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/READ | Bármely cél számítási méret olvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/művelet | Feladatátvételi teszt |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/művelet | Feladatátvételi teszt karbantartása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/művelet | Feladatátvétel |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/művelet | Mobilitási szolgáltatás frissítése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/Write | Védett elemek létrehozása vagy frissítése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | A védelmi tárolók összes leképezésének törlése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/operationresults/READ | Egy aszinkron művelet eredményének nyomon követése az erőforrás-védelmi tároló-hozzárendeléseken |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/READ | A védelmi tárolók megfeleltetésének beolvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/eltávolítás/művelet | Védelmi tároló megfeleltetésének eltávolítása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/Write | Védelmi tárolók hozzárendeléseinek létrehozása vagy frissítése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/replicationProtectionContainers/switchprotection/művelet | Védelmi tároló váltása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationProtectionContainers/Write | Védelmi tárolók létrehozása vagy frissítése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationRecoveryServicesProviders/delete | Recovery Services szolgáltatók törlése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationRecoveryServicesProviders/operationresults/READ | Aszinkron művelet eredményeinek nyomon követése az erőforrás-Recovery Services szolgáltatókon |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationRecoveryServicesProviders/READ | Recovery Services szolgáltatók beolvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/művelet | Szolgáltató frissítése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/replicationRecoveryServicesProviders/Remove/Action | Recovery Services-szolgáltató eltávolítása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationRecoveryServicesProviders/Write | Recovery Services szolgáltatók létrehozása vagy frissítése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationStorageClassifications/READ | A tárolási besorolások beolvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | A tárolási besorolási leképezések törlése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/operationresults/READ | Aszinkron művelet eredményeinek nyomon követése az erőforrás-tároló besorolási hozzárendelésein |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/READ | A tárolási besorolási leképezések beolvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/Write | Tárolási besorolási leképezések létrehozása vagy frissítése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationvCenters/delete | Tetszőleges vCenter törlése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationvCenters/operationresults/READ | Egy aszinkron művelet eredményének nyomon követése az erőforrás vCenter |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationvCenters/READ | Bármilyen vCenter beolvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationvCenters/Write | Tetszőleges vCenter létrehozása vagy frissítése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/tárolók/replicationFabrics/írás | Bármilyen háló létrehozása vagy frissítése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationJobs/megszakítás/művelet | Feladat megszakítása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationJobs/operationresults/READ | Aszinkron művelet eredményeinek nyomon követése az erőforrás-feladatokban |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationJobs/READ | Bármilyen feladat beolvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationJobs/újraindítás/művelet | Újraindítási feladatok |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationJobs/Resume/művelet | Feladatok folytatása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationMigrationItems/READ | Bármely áttelepítési elem olvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationNetworkMappings/READ | Hálózati leképezések olvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationNetworks/READ | Bármilyen hálózat beolvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationOperationStatus/READ | A tár replikációs művelet állapotának beolvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationPolicies/delete | Bármely szabályzat törlése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationPolicies/operationresults/READ | Aszinkron művelet eredményeinek nyomon követése az erőforrás-házirendekben |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationPolicies/READ | Bármilyen szabályzat beolvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/tárolók/replicationPolicies/írás | Szabályzatok létrehozása vagy frissítése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationProtectedItems/READ | Védett elemek beolvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationProtectionContainerMappings/READ | A védelmi tárolók megfeleltetésének beolvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationProtectionContainers/READ | Védelmi tárolók beolvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationRecoveryPlans/delete | Helyreállítási tervek törlése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationRecoveryPlans/failoverCommit/művelet | Feladatátvételi véglegesítés helyreállítási terve |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationRecoveryPlans/operationresults/READ | Aszinkron művelet eredményeinek nyomon követése az erőforrás-helyreállítási tervekben |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationRecoveryPlans/plannedFailover/művelet | Tervezett feladatátvétel helyreállítási terve |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationRecoveryPlans/READ | Helyreállítási tervek olvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/tárolók/replicationRecoveryPlans/ismételt védelem/művelet | Helyreállítási terv ismételt védetté |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationRecoveryPlans/testFailover/művelet | Feladatátvételi teszt helyreállítási terve |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationRecoveryPlans/testFailoverCleanup/művelet | Feladatátvételi teszt karbantartásának helyreállítási terve |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationRecoveryPlans/unplannedFailover/művelet | Feladatátvétel helyreállítási terve |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/tárolók/replicationRecoveryPlans/írás | Helyreállítási tervek létrehozása vagy frissítése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationRecoveryServicesProviders/READ | Recovery Services szolgáltatók beolvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationStorageClassificationMappings/READ | A tárolási besorolási leképezések beolvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationStorageClassifications/READ | A tárolási besorolások beolvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationSupportedOperatingSystems/READ | Bármilyen beolvasás  |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationUsages/READ | A tár replikációs használatának beolvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationVaultHealth/operationresults/READ | Aszinkron művelet eredményeinek nyomon követése az erőforrás-tároló replikációs állapotában |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationVaultHealth/READ | A tár replikációs állapotának olvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationVaultHealth/frissítés/művelet | Tár állapotának frissítése |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationVaultSettings/READ | Bármilyen beolvasás  |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/tárolók/replicationVaultSettings/írás | Tetszőlegesek létrehozása vagy frissítése  |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationvCenters/READ | Bármilyen vCenter beolvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/tárolók/használati adatok/olvasás | Bármely tár használatának olvasása |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/tárolók/használati adatok/olvasás | Egy Recovery Services tár használati adatait adja vissza. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/Vault/vaultTokens/READ | A tároló-jogkivonat művelettel lekérheti a tár szintű háttérbeli műveletek tároló-tokenjét. |
> | Műveletek | Microsoft. Recoveryservices szolgáltatónál/tárolók/írás | A Create Vault művelet létrehoz egy "Vault" típusú Azure-erőforrást. |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Relay/checkNameAvailability/művelet | Ellenőrzi a névtér elérhetőségét a megadott előfizetésben. |
> | Műveletek | Microsoft. Relay/checkNamespaceAvailability/művelet | Ellenőrzi a névtér elérhetőségét a megadott előfizetésben. Ez az API elavult. használja helyette a CheckNameAvailability. |
> | Műveletek | Microsoft. Relay/névterek/engedélyezési szabályok/művelet | Frissíti a névtér-engedélyezési szabályt. Ez az API elavult. Ehelyett használjon egy PUT hívást a névtér-engedélyezési szabály frissítéséhez. Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. |
> | Műveletek | Microsoft. Relay/névterek/engedélyezési szabályok/delete | Névtér-engedélyezési szabály törlése. Az alapértelmezett névtér-engedélyezési szabály nem törölhető.  |
> | Műveletek | Microsoft. Relay/névtér/engedélyezési szabályok/listkeys műveletének beolvasása/művelet | A névtérhez tartozó kapcsolódási karakterlánc beolvasása |
> | Műveletek | Microsoft. Relay/névtér/engedélyezési szabályok/olvasás | A névterek engedélyezési szabályai leírásának beolvasása. |
> | Műveletek | Microsoft. Relay/névtér/engedélyezési szabályok/regenerateKeys/művelet | Az elsődleges vagy másodlagos kulcs újragenerálása az erőforráshoz |
> | Műveletek | Microsoft. Relay/névterek/engedélyezési szabályok/írás | Hozzon létre egy névtér szintű engedélyezési szabályokat, és frissítse a tulajdonságait. Az engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és a másodlagos kulcsokat is frissítheti. |
> | Műveletek | Microsoft. Relay/névterek/törlés | Névtér erőforrásának törlése |
> | Műveletek | Microsoft. Relay/névtér/disasterRecoveryConfigs/engedélyezési szabályok/listkeys műveletének beolvasása/művelet | A vész-helyreállítási elsődleges névtérhez tartozó engedélyezési szabályok kulcsainak beolvasása |
> | Műveletek | Microsoft. Relay/névterek/disasterRecoveryConfigs/engedélyezési szabályok/READ | Vész-helyreállítási elsődleges névtér engedélyezési szabályainak beolvasása |
> | Műveletek | Microsoft. Relay/névtér/disasterRecoveryConfigs/breakPairing/művelet | Letiltja a vész-helyreállítást, és leállítja a módosítások elsődlegesről másodlagos névtérbe történő replikálását. |
> | Műveletek | Microsoft. Relay/névtér/disasterrecoveryconfigs/checkNameAvailability/művelet | Ellenőrzi a névtér-alias elérhetőségét a megadott előfizetés alatt. |
> | Műveletek | Microsoft. Relay/névterek/disasterRecoveryConfigs/delete | A névtérhez társított vész-helyreállítási konfiguráció törlése. Ez a művelet csak az elsődleges névtér használatával hívható meg. |
> | Műveletek | Microsoft. Relay/névterek/disasterRecoveryConfigs/feladatátvétel/művelet | Egy GEO DR feladatátvételt hív meg, és újrakonfigurálja a névtér aliasát, hogy a másodlagos névtérre mutasson. |
> | Műveletek | Microsoft. Relay/névtér/disasterRecoveryConfigs/olvasás | A névtérhez társított vész-helyreállítási konfiguráció beolvasása. |
> | Műveletek | Microsoft. Relay/névterek/disasterRecoveryConfigs/írás | A névtérhez társított vész-helyreállítási konfiguráció létrehozása vagy frissítése. |
> | Műveletek | Microsoft. Relay/névtér/HybridConnections/engedélyezési szabályok/művelet | A HybridConnection frissítésének művelete. Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. Engedélyezési szabályok. Használjon egy PUT hívást az engedélyezési szabály frissítéséhez. |
> | Műveletek | Microsoft. Relay/névterek/HybridConnections/engedélyezési szabályok/delete | HybridConnection-engedélyezési szabályok törlésének művelete |
> | Műveletek | Microsoft. Relay/névtér/HybridConnections/engedélyezési szabályok/listkeys műveletének beolvasása/művelet | A HybridConnection tartozó kapcsolódási karakterlánc beolvasása |
> | Műveletek | Microsoft. Relay/névterek/HybridConnections/engedélyezési szabályok/READ |  A HybridConnection-engedélyezési szabályok listájának beolvasása |
> | Műveletek | Microsoft. Relay/névtér/HybridConnections/engedélyezési szabályok/regeneratekeys/művelet | Az elsődleges vagy másodlagos kulcs újragenerálása az erőforráshoz |
> | Műveletek | Microsoft. Relay/névterek/HybridConnections/engedélyezési szabályok/írás | Hozzon létre HybridConnection engedélyezési szabályokat, és frissítse annak tulajdonságait. Az engedélyezési szabályokhoz tartozó hozzáférési jogosultságok frissíthetők. |
> | Műveletek | Microsoft. Relay/névterek/HybridConnections/delete | HybridConnection-erőforrás törlésére szolgáló művelet |
> | Műveletek | Microsoft. Relay/névtér/HybridConnections/olvasás | HybridConnection-erőforrás leírásának beolvasása |
> | Műveletek | Microsoft. Relay/névterek/HybridConnections/írás | HybridConnection tulajdonságainak létrehozása vagy frissítése. |
> | Műveletek | Microsoft. Relay/névtér/messagingPlan/olvasás | Lekéri egy névtér üzenetkezelési tervét.<br>Ez az API elavult.<br>A MessagingPlan-erőforráson keresztül elérhető tulajdonságok a (szülő) névtér-erőforrásba kerülnek a későbbi API-verziókban.<br>Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. |
> | Műveletek | Microsoft. Relay/névterek/messagingPlan/írás | Frissíti egy névtér üzenetkezelési tervét.<br>Ez az API elavult.<br>A MessagingPlan-erőforráson keresztül elérhető tulajdonságok a (szülő) névtér-erőforrásba kerülnek a későbbi API-verziókban.<br>Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. |
> | Műveletek | Microsoft. Relay/névtér/operationresults/olvasás | Névtér-művelet állapotának beolvasása |
> | Műveletek | Microsoft. Relay/névterek/szolgáltatók/Microsoft. bepillantást/diagnosticSettings/READ | Névtér diagnosztikai beállításainak erőforrás-leírásának beolvasása |
> | Műveletek | Microsoft. Relay/névterek/szolgáltatók/Microsoft. bepillantások/diagnosticSettings/írás | Névtér diagnosztikai beállításainak erőforrás-leírásának beolvasása |
> | Műveletek | Microsoft. Relay/névterek/szolgáltatók/Microsoft. bepillantást/logDefinitions/READ | Névtér-naplók erőforrás-leírások listájának beolvasása |
> | Műveletek | Microsoft. Relay/névterek/szolgáltatók/Microsoft. bepillantást/metricDefinitions/READ | Névtér-metrikák erőforrás-leírásának beolvasása |
> | Műveletek | Microsoft. Relay/névterek/olvasás | A névtér erőforrás-leírásának beolvasása |
> | Műveletek | Microsoft. Relay/névterek/removeAcsNamepsace/művelet | ACS-névtér eltávolítása |
> | Műveletek | Microsoft. Relay/névtér/WcfRelays/engedélyezési szabályok/művelet | A WcfRelay frissítésének művelete. Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. Engedélyezési szabályok. Használjon egy PUT hívást az engedélyezési szabály frissítéséhez. |
> | Műveletek | Microsoft. Relay/névterek/WcfRelays/engedélyezési szabályok/delete | WcfRelay-engedélyezési szabályok törlésének művelete |
> | Műveletek | Microsoft. Relay/névtér/WcfRelays/engedélyezési szabályok/listkeys műveletének beolvasása/művelet | A WcfRelay tartozó kapcsolódási karakterlánc beolvasása |
> | Műveletek | Microsoft. Relay/névterek/WcfRelays/engedélyezési szabályok/READ |  A WcfRelay-engedélyezési szabályok listájának beolvasása |
> | Műveletek | Microsoft. Relay/névtér/WcfRelays/engedélyezési szabályok/regeneratekeys/művelet | Az elsődleges vagy másodlagos kulcs újragenerálása az erőforráshoz |
> | Műveletek | Microsoft. Relay/névterek/WcfRelays/engedélyezési szabályok/írás | Hozzon létre WcfRelay engedélyezési szabályokat, és frissítse annak tulajdonságait. Az engedélyezési szabályokhoz tartozó hozzáférési jogosultságok frissíthetők. |
> | Műveletek | Microsoft. Relay/névterek/WcfRelays/delete | WcfRelay-erőforrás törlésére szolgáló művelet |
> | Műveletek | Microsoft. Relay/névtér/WcfRelays/olvasás | WcfRelay-erőforrás leírásának beolvasása |
> | Műveletek | Microsoft. Relay/névterek/WcfRelays/írás | WcfRelay tulajdonságainak létrehozása vagy frissítése. |
> | Műveletek | Microsoft. Relay/névterek/írás | Hozzon létre egy névtér-erőforrást, és frissítse a tulajdonságait. A névtér címkéi és kapacitása a frissíthető tulajdonságok. |
> | Műveletek | Microsoft. Relay/Operations/READ | Műveletek beolvasása |
> | Műveletek | Microsoft. Relay/regisztráció/művelet | Regisztrálja az előfizetést a továbbító erőforrás-szolgáltató számára, és lehetővé teszi a továbbítási erőforrások létrehozását |
> | Műveletek | Microsoft. Relay/regisztráció/művelet | Regisztrálja az előfizetést a továbbító erőforrás-szolgáltató számára, és lehetővé teszi a továbbítási erőforrások létrehozását |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. ResourceHealth/AvailabilityStatuses/aktuális/olvasási | Lekéri a megadott erőforrás rendelkezésre állási állapotát. |
> | Műveletek | Microsoft. ResourceHealth/AvailabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Műveletek | Microsoft. ResourceHealth/események/olvasás | Az adott előfizetés Service Health eseményeinek beolvasása |
> | Műveletek | Microsoft. Resourcehealth/healthevent/művelet | A megadott erőforrás állapotának változását jelöli. |
> | Műveletek | Microsoft. Resourcehealth/healthevent/aktivált/művelet | A megadott erőforrás állapotának változását jelöli. |
> | Műveletek | Microsoft. Resourcehealth/healthevent/Inprogress/művelet | A megadott erőforrás állapotának változását jelöli. |
> | Műveletek | Microsoft. Resourcehealth/healthevent/függő/művelet | A megadott erőforrás állapotának változását jelöli. |
> | Műveletek | Microsoft. Resourcehealth/healthevent/megoldott/művelet | A megadott erőforrás állapotának változását jelöli. |
> | Műveletek | Microsoft. Resourcehealth/healthevent/frissítve/művelet | A megadott erőforrás állapotának változását jelöli. |
> | Műveletek | Microsoft. ResourceHealth/impactedResources/READ | Érintett erőforrások beolvasása az adott előfizetéshez |
> | Műveletek | Microsoft. ResourceHealth/metaadatok/olvasás | Metaadatok beolvasása |
> | Műveletek | Microsoft. ResourceHealth/értesítések/olvasás | Azure Resource Manager értesítések fogadása |
> | Műveletek | Microsoft. ResourceHealth/Operations/READ | A Microsoft ResourceHealth elérhető műveletek beolvasása |
> | Műveletek | Microsoft. ResourceHealth/regisztráció/művelet | Regisztrálja az előfizetést a Microsoft ResourceHealth |
> | Műveletek | Microsoft. ResourceHealth/regisztráció/művelet | A Microsoft ResourceHealth-előfizetés regisztrációjának törlése |

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Resources/calculateTemplateHash/Action | Kiszámítja a megadott sablon kivonatát. |
> | Műveletek | Microsoft. Resources/checkPolicyCompliance/READ | Egy adott erőforrás megfelelőségi állapotának ellenőrzése erőforrás-házirendekkel. |
> | Műveletek | Microsoft. Resources/checkResourceName/Action | Győződjön meg arról, hogy az erőforrás neve érvényes. |
> | Műveletek | Microsoft. Resources/központi telepítések/megszakítás/művelet | Egy központi telepítés megszakítása. |
> | Műveletek | Microsoft. Resources/központi telepítések/törlés | Törli a központi telepítést. |
> | Műveletek | Microsoft. Resources/üzembe helyezés/exportTemplate/művelet | Sablon exportálása központi telepítéshez |
> | Műveletek | Microsoft. Resources/központi telepítések/műveletek/olvasás | Lekérdezi vagy felsorolja az üzembe helyezési műveleteket. |
> | Műveletek | Microsoft. Resources/üzembe helyezés/operationstatuses/olvasás | Lekérdezi vagy felsorolja a telepítési művelet állapotát. |
> | Műveletek | Microsoft. Resources/központi telepítések/olvasás | Lekérdezi vagy felsorolja az üzemelő példányokat. |
> | Műveletek | Microsoft. Resources/Deployments/validate/Action | Érvényesíti az üzemelő példányt. |
> | Műveletek | Microsoft. Resources/üzembe helyezés/whatIf/művelet | Megjósolja a sablon központi telepítésének változásait. |
> | Műveletek | Microsoft. Resources/üzembe helyezés/írás | Egy központi telepítés létrehozása vagy frissítése. |
> | Műveletek | Microsoft. Resources/deploymentScripts/delete | Telepítési parancsfájl törlése |
> | Műveletek | Microsoft. Resources/deploymentScripts/logs/olvasás | Lekérdezi vagy listázza a telepítési parancsfájlok naplóit |
> | Műveletek | Microsoft. Resources/deploymentScripts/READ | Lekérdezi vagy felsorolja az üzembe helyezési parancsfájlokat |
> | Műveletek | Microsoft. Resources/deploymentScripts/Write | Üzembe helyezési parancsfájl létrehozása vagy frissítése |
> | Műveletek | Microsoft. Resources/Links/delete | Töröl egy erőforrás-hivatkozást. |
> | Műveletek | Microsoft. Resources/Links/READ | Lekérdezi vagy felsorolja az erőforrás-hivatkozásokat. |
> | Műveletek | Microsoft. Resources/Links/Write | Létrehoz vagy frissít egy erőforrás-hivatkozást. |
> | Műveletek | Microsoft. Resources/Marketplace/vásárlás/művelet | Vásárol egy erőforrást a piactéren. |
> | Műveletek | Microsoft. Resources/Providers/READ | A szolgáltatók listájának beolvasása. |
> | Műveletek | Microsoft. Resources/Resources/READ | Az erőforrások listájának lekérése szűrők alapján. |
> | Műveletek | Microsoft. Resources/Subscriptions/Locations/READ | A támogatott helyszínek listájának beolvasása. |
> | Műveletek | Microsoft. Resources/Subscriptions/operationresults/READ | Az előfizetési művelet eredményeinek beolvasása. |
> | Műveletek | Microsoft. Resources/előfizetések/szolgáltatók/olvasás | Lekérdezi vagy felsorolja az erőforrás-szolgáltatókat. |
> | Műveletek | Microsoft. Resources/előfizetések/olvasás | Az előfizetések listájának beolvasása. |
> | Műveletek | Microsoft. Resources/Subscriptions/resourceGroups/delete | Töröl egy erőforráscsoportot és annak összes erőforrását. |
> | Műveletek | Microsoft. erőforrások/előfizetések/resourcegroups/üzembe helyezések/műveletek/olvasás | Lekérdezi vagy felsorolja az üzembe helyezési műveleteket. |
> | Műveletek | Microsoft. Resources/előfizetések/resourcegroups/üzembe helyezés/operationstatuses/olvasás | Lekérdezi vagy felsorolja a telepítési művelet állapotát. |
> | Műveletek | Microsoft. Resources/előfizetések/resourcegroups/üzembe helyezés/olvasás | Lekérdezi vagy felsorolja az üzemelő példányokat. |
> | Műveletek | Microsoft. Resources/előfizetések/resourcegroups/üzembe helyezés/írás | Egy központi telepítés létrehozása vagy frissítése. |
> | Műveletek | Microsoft. Resources/Subscriptions/resourceGroups/moveResources/Action | Áthelyezi az erőforrásokat az egyik erőforráscsoporthoz a másikba. |
> | Műveletek | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Műveletek | Microsoft. Resources/Subscriptions/resourcegroups/Resources/READ | Az erőforráscsoport erőforrásainak beolvasása. |
> | Műveletek | Microsoft. Resources/Subscriptions/resourceGroups/validateMoveResources/Action | Az erőforrások egyik erőforráscsoporthoz egy másikra történő áthelyezésének ellenőrzése. |
> | Műveletek | Microsoft. Resources/Subscriptions/resourceGroups/Write | Létrehoz vagy frissít egy erőforráscsoportot. |
> | Műveletek | Microsoft. Resources/előfizetések/erőforrások/olvasás | Az előfizetés erőforrásainak beolvasása. |
> | Műveletek | Microsoft. Resources/Subscriptions/tagNames/delete | Törli az előfizetés címkéjét. |
> | Műveletek | Microsoft. Resources/Subscriptions/tagNames/READ | Lekérdezi vagy felsorolja az előfizetés címkéit. |
> | Műveletek | Microsoft. Resources/Subscriptions/tagNames/tagValues/delete | Egy előfizetési címke értékének törlése. |
> | Műveletek | Microsoft. Resources/Subscriptions/tagNames/tagValues/READ | Lekérdezi vagy felsorolja az előfizetési címke értékeit. |
> | Műveletek | Microsoft. Resources/Subscriptions/tagNames/tagValues/Write | Hozzáadja az előfizetési címke értékét. |
> | Műveletek | Microsoft. Resources/Subscriptions/tagNames/Write | Hozzáadja az előfizetés címkéjét. |
> | Műveletek | Microsoft. Resources/Tags/delete | Eltávolítja az erőforrás összes címkéjét. |
> | Műveletek | Microsoft. Resources/Tags/READ | Lekéri az erőforrás összes címkéjét. |
> | Műveletek | Microsoft. Resources/Tags/Write | Frissíti egy erőforrás címkéit a meglévő címkék új címkékkel való lecserélésével vagy összevonásával, vagy a meglévő címkék eltávolításával. |
> | Műveletek | Microsoft. Resources/bérlők/olvasás | A bérlők listájának beolvasása. |

## <a name="microsoftscheduler"></a>Microsoft. Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Scheduler/feladatgyűjtemények/delete | A feladatütemezés törlése. |
> | Műveletek | Microsoft. Scheduler/feladatgyűjtemények/letiltás/művelet | A feladatütemezés letiltása. |
> | Műveletek | Microsoft. Scheduler/feladatgyűjtemények/engedélyezés/művelet | A feladatütemezés engedélyezése. |
> | Műveletek | Microsoft. Scheduler/feladatgyűjtemények/feladatok/törlés | Törli a feladatot. |
> | Műveletek | Microsoft. Scheduler/feladatgyűjtemények/Jobs/generateLogicAppDefinition/művelet | A logikai alkalmazás definícióját ütemező feladatok alapján hozza létre. |
> | Műveletek | Microsoft. Scheduler/feladatgyűjtemények/feladatok/jobhistories/olvasás | Beolvassa a feladatok előzményeit. |
> | Műveletek | Microsoft. Scheduler/feladatgyűjtemények/feladatok/olvasás | Lekérdezi a feladatot. |
> | Műveletek | Microsoft. Scheduler/feladatgyűjtemények/feladatok/Futtatás/művelet | A feladatot futtatja. |
> | Műveletek | Microsoft. Scheduler/feladatgyűjtemények/feladatok/írás | Létrehozza vagy frissíti a feladatot. |
> | Műveletek | Microsoft. Scheduler/feladatgyűjtemények/olvasás | Webhelycsoport beolvasása |
> | Műveletek | Microsoft. Scheduler/feladatgyűjtemények/írás | A feladatütemezés létrehozása vagy frissítése. |

## <a name="microsoftsearch"></a>Microsoft. Search

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Search/checkNameAvailability/művelet | A szolgáltatás nevének rendelkezésre állását ellenőrzi. |
> | Műveletek | Microsoft. Search/Operations/READ | A Microsoft. Search szolgáltató összes elérhető műveletét listázza. |
> | Műveletek | Microsoft. Search/regisztráció/művelet | Regisztrálja az előfizetést a keresési erőforrás-szolgáltatónál, és lehetővé teszi a keresési szolgáltatások létrehozását. |
> | Műveletek | Microsoft. Search/searchServices/createQueryKey/művelet | Létrehozza a lekérdezési kulcsot. |
> | Műveletek | Microsoft. Search/searchServices/delete | A keresési szolgáltatás törlése. |
> | Műveletek | Microsoft. Search/searchServices/deleteQueryKey/delete | Törli a lekérdezési kulcsot. |
> | Műveletek | Microsoft. Search/searchServices/listAdminKeys/művelet | A rendszergazdai kulcsok beolvasása. |
> | Műveletek | Microsoft. Search/searchServices/listQueryKeys/művelet | Az adott Azure Search szolgáltatáshoz tartozó lekérdezési API-kulcsok listáját adja vissza. |
> | Műveletek | Microsoft. Search/searchServices/listQueryKeys/READ | Az adott Azure Search szolgáltatáshoz tartozó lekérdezési API-kulcsok listáját adja vissza. |
> | Műveletek | Microsoft. Search/searchServices/privateEndpointConnectionProxies/delete | Meglévő privát végponti kapcsolatkezelő törlése |
> | Műveletek | Microsoft. Search/searchServices/privateEndpointConnectionProxies/READ | A privát végponti kapcsolatok proxyinak listáját adja vissza, vagy beolvassa a megadott privát végponti ügyfélkapcsolati proxy tulajdonságait |
> | Műveletek | Microsoft. Search/searchServices/privateEndpointConnectionProxies/validate/Action | Egy privát végponti kapcsolatok ellenőrzése az NRP oldalról |
> | Műveletek | Microsoft. Search/searchServices/privateEndpointConnectionProxies/Write | Létrehoz egy privát végponti ügyfélkapcsolati proxyt a megadott paraméterekkel, vagy frissíti a megadott privát végponti ügyfélkapcsolati proxy tulajdonságait vagy címkéit. |
> | Műveletek | Microsoft. Search/searchServices/READ | A keresési szolgáltatás beolvasása. |
> | Műveletek | Microsoft. Search/searchServices/regenerateAdminKey/művelet | Újra létrehozza a felügyeleti kulcsot. |
> | Műveletek | Microsoft. Search/searchServices/Start/művelet | Elindítja a keresési szolgáltatást. |
> | Műveletek | Microsoft. Search/searchServices/leállítás/művelet | Leállítja a keresési szolgáltatást. |
> | Műveletek | Microsoft. Search/searchServices/Write | A keresési szolgáltatás létrehozása vagy frissítése. |

## <a name="microsoftsecurity"></a>Microsoft. Security

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Security/adaptiveNetworkHardenings/betartatás/művelet | A megadott forgalmi korlátozási szabályok betartatása a megadott hálózati biztonsági csoport (ok) hoz tartozó egyező biztonsági szabályok létrehozásával |
> | Műveletek | Microsoft. Security/adaptiveNetworkHardenings/READ | Az Azure által védett erőforrások adaptív hálózattal kapcsolatos javaslatainak beolvasása |
> | Műveletek | Microsoft. Security/advancedThreatProtectionSettings/READ | Az erőforrás összetett veszélyforrások elleni védelmi beállításainak beolvasása |
> | Műveletek | Microsoft. Security/advancedThreatProtectionSettings/írás | Az erőforrás összetett veszélyforrások elleni védelmi beállításainak frissítése |
> | Műveletek | Microsoft. Security/riasztások/olvasás | Az összes elérhető biztonsági riasztás beolvasása |
> | Műveletek | Microsoft. Security/applicationWhitelistings/READ | Az alkalmazás engedélyezési listájának beolvasása |
> | Műveletek | Microsoft. Security/applicationWhitelistings/írás | Új alkalmazás-engedélyezési lista létrehozása vagy meglévő frissítése |
> | Műveletek | Microsoft. Security/assessmentMetadata/READ | Elérhető biztonsági értékelési metaadatok beolvasása az előfizetéshez |
> | Műveletek | Microsoft. Security/assessmentMetadata/írás | Biztonsági értékelés metaadatainak létrehozása vagy frissítése |
> | Műveletek | Microsoft. Security/értékelések/olvasás | Biztonsági értékelések beszerzése az előfizetésen |
> | Műveletek | Microsoft. Security/értékelések/írás | Biztonsági értékelések létrehozása vagy frissítése az előfizetésen |
> | Műveletek | Microsoft. Security/complianceResults/READ | Az erőforrás megfelelőségi eredményeinek beolvasása |
> | Műveletek | Microsoft. Security/informationProtectionPolicies/READ | Az erőforráshoz tartozó Information Protection-szabályzatok beolvasása |
> | Műveletek | Microsoft. Security/informationProtectionPolicies/írás | Az erőforráshoz tartozó Information Protection-szabályzatok frissítése |
> | Műveletek | Microsoft. Security/Locations/riasztások/aktiválás/művelet | Biztonsági riasztás aktiválása |
> | Műveletek | Microsoft. Security/Locations/riasztások/elutasítás/művelet | Biztonsági riasztás bezárása |
> | Műveletek | Microsoft. Security/Locations/riasztások/olvasás | Az összes elérhető biztonsági riasztás beolvasása |
> | Műveletek | Microsoft. Security/Locations/jitNetworkAccessPolicies/delete | Az igény szerinti hálózati hozzáférési házirend törlése |
> | Műveletek | Microsoft. Security/Locations/jitNetworkAccessPolicies/kezdeményezés/művelet | Igény szerinti hálózati hozzáférési szabályzat kezdeményezése |
> | Műveletek | Microsoft. Security/Locations/jitNetworkAccessPolicies/READ | Az igény szerinti hálózati hozzáférési szabályzatok beolvasása |
> | Műveletek | Microsoft. Security/Locations/jitNetworkAccessPolicies/Write | Egy új, igény szerinti hálózati hozzáférési házirend létrehozása vagy egy meglévő frissítése |
> | Műveletek | Microsoft. Security/Locations/READ | A biztonsági adattárolási hely beolvasása |
> | Műveletek | Microsoft. Security/Locations/Tasks/Activate/Action | Biztonsági javaslat aktiválása |
> | Műveletek | Microsoft. Security/Locations/Tasks/elutasítás/művelet | Biztonsági javaslat bezárása |
> | Műveletek | Microsoft. Security/Locations/Tasks/READ | Az összes elérhető biztonsági javaslat beolvasása |
> | Műveletek | Microsoft. Security/Locations/Tasks/megoldás/művelet | Biztonsági javaslat feloldása |
> | Műveletek | Microsoft. Security/Locations/Tasks/Start/Action | Biztonsági javaslat elindítása |
> | Műveletek | Microsoft. Security/házirendek/olvasás | A biztonsági szabályzat beolvasása |
> | Műveletek | Microsoft. Security/házirendek/írás | A biztonsági szabályzat frissítése |
> | Műveletek | Microsoft. Security/díjszabás/törlés | A hatókör díjszabási beállításainak törlése |
> | Műveletek | Microsoft. Security/díjszabás/olvasás | A hatókör díjszabási beállításainak beolvasása |
> | Műveletek | Microsoft. Security/díjszabás/írás | A hatókör díjszabási beállításainak frissítése |
> | Műveletek | Microsoft. Security/regisztráció/művelet | Az előfizetés regisztrálása Azure Security Center |
> | Műveletek | Microsoft. Security/securityContacts/delete | A biztonsági kapcsolattartó törlése |
> | Műveletek | Microsoft. Security/securityContacts/READ | A biztonsági kapcsolattartó beolvasása |
> | Műveletek | Microsoft. Security/securityContacts/írás | A biztonsági kapcsolattartó frissítése |
> | Műveletek | Microsoft. Security/securitySolutions/delete | Biztonsági megoldás törlése |
> | Műveletek | Microsoft. Security/securitySolutions/READ | A biztonsági megoldások beolvasása |
> | Műveletek | Microsoft. Security/securitySolutions/írás | Új biztonsági megoldás létrehozása vagy egy meglévő frissítése |
> | Műveletek | Microsoft. Security/securitySolutionsReferenceData/READ | A biztonsági megoldások hivatkozási adatkérésének beolvasása |
> | Műveletek | Microsoft. Security/securityStatuses/READ | Az Azure-erőforrások biztonsági állapotának beolvasása |
> | Műveletek | Microsoft. Security/securityStatusesSummaries/READ | A hatókörre vonatkozó biztonsági állapotok összegzésének beolvasása |
> | Műveletek | Microsoft. Security/beállítások/olvasás | A hatókör beállításainak beolvasása |
> | Műveletek | Microsoft. Security/beállítások/írás | A hatókör beállításainak frissítése |
> | Műveletek | Microsoft. Security/feladatok/olvasás | Az összes elérhető biztonsági javaslat beolvasása |
> | Műveletek | Microsoft. Security/regisztráció/művelet | Az előfizetés regisztrációjának törlése Azure Security Center |
> | Műveletek | Microsoft. Security/webApplicationFirewalls/delete | Webalkalmazási tűzfal törlése |
> | Műveletek | Microsoft. Security/webApplicationFirewalls/READ | A webalkalmazási tűzfalak beolvasása |
> | Műveletek | Microsoft. Security/webApplicationFirewalls/írás | Új webalkalmazási tűzfal létrehozása vagy egy meglévő frissítése |
> | Műveletek | Microsoft. Security/workspaceSettings/csatlakozási/művelet | Munkaterület-beállítások újrakapcsolási beállításainak módosítása |
> | Műveletek | Microsoft. Security/workspaceSettings/delete | A munkaterület beállításainak törlése |
> | Műveletek | Microsoft. Security/workspaceSettings/READ | A munkaterület beállításainak beolvasása |
> | Műveletek | Microsoft. Security/workspaceSettings/írás | A munkaterület beállításainak frissítése |

## <a name="microsoftsecuritygraph"></a>Microsoft. SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. SecurityGraph/diagnosticsettings/delete | Diagnosztikai beállítások törlése |
> | Műveletek | Microsoft. SecurityGraph/diagnosticsettings/READ | Diagnosztikai beállítások olvasása |
> | Műveletek | Microsoft. SecurityGraph/diagnosticsettings/Write | Diagnosztikai beállítások írása |
> | Műveletek | Microsoft. SecurityGraph/diagnosticsettingscategories/READ | Diagnosztikai beállítások kategóriáinak olvasása |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. SecurityInsights/összesítések/olvasás | Összesített adatok beolvasása |
> | Műveletek | Microsoft. SecurityInsights/alertRules/Actions/delete | Egy riasztási szabály válaszait törli |
> | Műveletek | Microsoft. SecurityInsights/alertRules/műveletek/olvasás | Egy riasztási szabály válaszait kéri le. |
> | Műveletek | Microsoft. SecurityInsights/alertRules/Actions/Write | Egy riasztási szabály válaszai által végrehajtott műveletek frissítése |
> | Műveletek | Microsoft. SecurityInsights/alertRules/delete | Riasztási szabályok törlése |
> | Műveletek | Microsoft. SecurityInsights/alertRules/READ | A riasztási szabályok beolvasása |
> | Műveletek | Microsoft. SecurityInsights/alertRules/Write | Frissítések riasztási szabályai |
> | Műveletek | Microsoft. SecurityInsights/könyvjelzők/törlés | Könyvjelzők törlése |
> | Műveletek | Microsoft. SecurityInsights/könyvjelzők/Kibontás/művelet | Entitás kapcsolódó entitásának beolvasása egy adott bővítéssel |
> | Műveletek | Microsoft. SecurityInsights/könyvjelzők/olvasás | Könyvjelzők beolvasása |
> | Műveletek | Microsoft. SecurityInsights/könyvjelző/írás | Könyvjelzők frissítése |
> | Műveletek | Microsoft. SecurityInsights/esetek/megjegyzések/olvasás | Az eset megjegyzéseit kapja meg |
> | Műveletek | Microsoft. SecurityInsights/esetek/megjegyzések/írás | Az eset megjegyzéseit hozza létre. |
> | Műveletek | Microsoft. SecurityInsights/esetek/törlés | Eset törlése |
> | Műveletek | Microsoft. SecurityInsights/esetek/nyomozás/olvasás | Az esetek vizsgálatának beolvasása |
> | Műveletek | Microsoft. SecurityInsights/esetek/vizsgálatok/írás | Egy eset metaadatait frissíti |
> | Műveletek | Microsoft. SecurityInsights/esetek/olvasás | Eset beolvasása |
> | Műveletek | Microsoft. SecurityInsights/esetek/írás | Eset frissítése |
> | Műveletek | Microsoft. SecurityInsights/dataConnectors/delete | Adatösszekötő törlése |
> | Műveletek | Microsoft. SecurityInsights/dataConnectors/READ | Az adatösszekötők beolvasása |
> | Műveletek | Microsoft. SecurityInsights/dataConnectors/Write | Az adatösszekötő frissítése |
> | Műveletek | Microsoft. SecurityInsights/regisztráció/művelet | Az előfizetés regisztrálása az Azure Sentinel szolgáltatásban |
> | Műveletek | Microsoft. SecurityInsights/beállítások/olvasás | Beállítások beolvasása |
> | Műveletek | Microsoft. SecurityInsights/beállítások/írás | Frissítések beállításai |
> | Műveletek | Microsoft. SecurityInsights/regisztráció/művelet | Az előfizetés regisztrációjának törlése az Azure Sentinelből |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. ServiceBus/checkNameAvailability/művelet | Ellenőrzi a névtér elérhetőségét a megadott előfizetésben. |
> | Műveletek | Microsoft. ServiceBus/checkNamespaceAvailability/művelet | Ellenőrzi a névtér elérhetőségét a megadott előfizetésben. Ez az API elavult. használja helyette a CheckNameAvailability. |
> | Műveletek | Microsoft. ServiceBus/Locations/deleteVirtualNetworkOrSubnets/Action | A megadott VNet tartozó ServiceBus erőforrás-szolgáltató VNet-szabályainak törlése |
> | Műveletek | Microsoft. ServiceBus/névterek/engedélyezési szabályok/művelet | Frissíti a névtér-engedélyezési szabályt. Ez az API elavult. Ehelyett használjon egy PUT hívást a névtér-engedélyezési szabály frissítéséhez. Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. |
> | Műveletek | Microsoft. ServiceBus/névterek/engedélyezési szabályok/delete | Névtér-engedélyezési szabály törlése. Az alapértelmezett névtér-engedélyezési szabály nem törölhető.  |
> | Műveletek | Microsoft. ServiceBus/névterek/engedélyezési szabályok/listkeys műveletének beolvasása/művelet | A névtérhez tartozó kapcsolódási karakterlánc beolvasása |
> | Műveletek | Microsoft. ServiceBus/névterek/engedélyezési szabályok/olvasás | A névterek engedélyezési szabályai leírásának beolvasása. |
> | Műveletek | Microsoft. ServiceBus/névterek/engedélyezési szabályok/regenerateKeys/művelet | Az elsődleges vagy másodlagos kulcs újragenerálása az erőforráshoz |
> | Műveletek | Microsoft. ServiceBus/névterek/engedélyezési szabályok/írás | Hozzon létre egy névtér szintű engedélyezési szabályokat, és frissítse a tulajdonságait. Az engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és a másodlagos kulcsokat is frissítheti. |
> | Műveletek | Microsoft. ServiceBus/névterek/törlés | Névtér erőforrásának törlése |
> | Műveletek | Microsoft. ServiceBus/névterek/disasterRecoveryConfigs/engedélyezési szabályok/listkeys műveletének beolvasása/művelet | A vész-helyreállítási elsődleges névtérhez tartozó engedélyezési szabályok kulcsainak beolvasása |
> | Műveletek | Microsoft. ServiceBus/névterek/disasterRecoveryConfigs/engedélyezési szabályok/READ | Vész-helyreállítási elsődleges névtér engedélyezési szabályainak beolvasása |
> | Műveletek | Microsoft. ServiceBus/névterek/disasterRecoveryConfigs/breakPairing/művelet | Letiltja a vész-helyreállítást, és leállítja a módosítások elsődlegesről másodlagos névtérbe történő replikálását. |
> | Műveletek | Microsoft. ServiceBus/névterek/disasterrecoveryconfigs/checkNameAvailability/művelet | Ellenőrzi a névtér-alias elérhetőségét a megadott előfizetés alatt. |
> | Műveletek | Microsoft. ServiceBus/névterek/disasterRecoveryConfigs/delete | A névtérhez társított vész-helyreállítási konfiguráció törlése. Ez a művelet csak az elsődleges névtér használatával hívható meg. |
> | Műveletek | Microsoft. ServiceBus/névterek/disasterRecoveryConfigs/feladatátvétel/művelet | Egy GEO DR feladatátvételt hív meg, és újrakonfigurálja a névtér aliasát, hogy a másodlagos névtérre mutasson. |
> | Műveletek | Microsoft. ServiceBus/névterek/disasterRecoveryConfigs/olvasás | A névtérhez társított vész-helyreállítási konfiguráció beolvasása. |
> | Műveletek | Microsoft. ServiceBus/névterek/disasterRecoveryConfigs/írás | A névtérhez társított vész-helyreállítási konfiguráció létrehozása vagy frissítése. |
> | Műveletek | Microsoft. ServiceBus/névterek/eventGridFilters/delete | Törli a névtérhez társított Event Grid szűrőt. |
> | Műveletek | Microsoft. ServiceBus/névterek/eventGridFilters/olvasás | A névtérhez társított Event Grid szűrő beolvasása. |
> | Műveletek | Microsoft. ServiceBus/névterek/eventGridFilters/írás | Létrehozza vagy frissíti a névtérhez társított Event Grid szűrőt. |
> | Műveletek | Microsoft. ServiceBus/névterek/eventhubs/olvasás | EventHub-erőforrás leírásának beolvasása |
> | Műveletek | Microsoft. ServiceBus/névterek/ipFilterRules/delete | IP-szűrő erőforrásának törlése |
> | Műveletek | Microsoft. ServiceBus/névterek/ipFilterRules/olvasás | IP-szűrő erőforrásának beolvasása |
> | Műveletek | Microsoft. ServiceBus/névterek/ipFilterRules/írás | IP-szűrő erőforrásának létrehozása |
> | DataAction | Microsoft. ServiceBus/névterek/üzenetek/fogadás/művelet | Üzenetek fogadása |
> | DataAction | Microsoft. ServiceBus/névterek/üzenetek/küldés/művelet | Üzenetek küldése |
> | Műveletek | Microsoft. ServiceBus/névterek/messagingPlan/olvasás | Lekéri egy névtér üzenetkezelési tervét.<br>Ez az API elavult.<br>A MessagingPlan-erőforráson keresztül elérhető tulajdonságok a (szülő) névtér-erőforrásba kerülnek a későbbi API-verziókban.<br>Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. |
> | Műveletek | Microsoft. ServiceBus/névterek/messagingPlan/írás | Frissíti egy névtér üzenetkezelési tervét.<br>Ez az API elavult.<br>A MessagingPlan-erőforráson keresztül elérhető tulajdonságok a (szülő) névtér-erőforrásba kerülnek a későbbi API-verziókban.<br>Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. |
> | Műveletek | Microsoft. ServiceBus/névterek/áttelepítés/művelet | Névtér átmigrálása művelet |
> | Műveletek | Microsoft. ServiceBus/névterek/migrationConfigurations/delete | Az áttelepítési konfiguráció törlése. |
> | Műveletek | Microsoft. ServiceBus/névterek/migrationConfigurations/olvasás | Az áttelepítési konfiguráció beolvasása, amely jelzi az áttelepítés állapotát és a függőben lévő replikációs műveleteket. |
> | Műveletek | Microsoft. ServiceBus/névterek/migrationConfigurations/visszaállítás/művelet | A standard és a prémium szintű névtér áttelepítésének visszaállítása |
> | Műveletek | Microsoft. ServiceBus/névterek/migrationConfigurations/frissítés/művelet | Hozzárendeli a standard névtérhez tartozó DNS-t a prémium névtérhez, amely befejezi az áttelepítést, és leállítja az erőforrások szinkronizálását a standard és a prémium névtér között. |
> | Műveletek | Microsoft. ServiceBus/névterek/migrationConfigurations/írás | Az áttelepítési konfiguráció létrehozása vagy frissítése. Ezzel elindítja a standard és a prémium szintű névtér erőforrásainak szinkronizálását. |
> | Műveletek | Microsoft. ServiceBus/névterek/networkruleset/delete | VNET-szabály erőforrásának törlése |
> | Műveletek | Microsoft. ServiceBus/névterek/networkruleset/olvasás | NetworkRuleSet-erőforrás beolvasása |
> | Műveletek | Microsoft. ServiceBus/névterek/networkruleset/írás | VNET-szabály erőforrásának létrehozása |
> | Műveletek | Microsoft. ServiceBus/névterek/networkrulesets/delete | VNET-szabály erőforrásának törlése |
> | Műveletek | Microsoft. ServiceBus/névterek/networkrulesets/olvasás | NetworkRuleSet-erőforrás beolvasása |
> | Műveletek | Microsoft. ServiceBus/névterek/networkrulesets/írás | VNET-szabály erőforrásának létrehozása |
> | Műveletek | Microsoft. ServiceBus/névterek/operationresults/olvasás | Névtér-művelet állapotának beolvasása |
> | Műveletek | Microsoft. ServiceBus/névterek/szolgáltatók/Microsoft. bepillantást/diagnosticSettings/READ | Névtér diagnosztikai beállításainak erőforrás-leírásának beolvasása |
> | Műveletek | Microsoft. ServiceBus/névterek/szolgáltatók/Microsoft. bepillantások/diagnosticSettings/írás | Névtér diagnosztikai beállításainak erőforrás-leírásának beolvasása |
> | Műveletek | Microsoft. ServiceBus/névterek/szolgáltatók/Microsoft. bepillantást/logDefinitions/READ | Névtér-naplók erőforrás-leírások listájának beolvasása |
> | Műveletek | Microsoft. ServiceBus/névterek/szolgáltatók/Microsoft. bepillantást/metricDefinitions/READ | Névtér-metrikák erőforrás-leírásának beolvasása |
> | Műveletek | Microsoft. ServiceBus/névterek/várólisták/engedélyezési szabályok/művelet | A várólista frissítési művelete. Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. Engedélyezési szabályok. Használjon egy PUT hívást az engedélyezési szabály frissítéséhez. |
> | Műveletek | Microsoft. ServiceBus/névterek/várólisták/engedélyezési szabályok/delete | Várólista-engedélyezési szabályok törlésének művelete |
> | Műveletek | Microsoft. ServiceBus/Namespaces/Queues/engedélyezési szabályok/listkeys műveletének beolvasása/művelet | A várólistához tartozó kapcsolódási karakterlánc lekérése |
> | Műveletek | Microsoft. ServiceBus/névterek/várólisták/engedélyezési szabályok/olvasás |  A várólista-engedélyezési szabályok listájának beolvasása |
> | Műveletek | Microsoft. ServiceBus/Namespaces/Queues/engedélyezési szabályok/regenerateKeys/művelet | Az elsődleges vagy másodlagos kulcs újragenerálása az erőforráshoz |
> | Műveletek | Microsoft. ServiceBus/névterek/várólisták/engedélyezési szabályok/írás | Várólista-engedélyezési szabályok létrehozása és a tulajdonságainak frissítése. Az engedélyezési szabályokhoz tartozó hozzáférési jogosultságok frissíthetők. |
> | Műveletek | Microsoft. ServiceBus/névterek/várólisták/törlés | Várólista-erőforrás törlésének művelete |
> | Műveletek | Microsoft. ServiceBus/névterek/várólisták/olvasás | Várólista-erőforrás leírásának beolvasása |
> | Műveletek | Microsoft. ServiceBus/névterek/várólisták/írás | Várólista tulajdonságainak létrehozása vagy frissítése |
> | Műveletek | Microsoft. ServiceBus/névterek/olvasás | A névtér erőforrás-leírásának beolvasása |
> | Műveletek | Microsoft. ServiceBus/névterek/removeAcsNamepsace/művelet | ACS-névtér eltávolítása |
> | Műveletek | Microsoft. ServiceBus/névterek/témakörök/engedélyezési szabályok/művelet | A témakör frissítésének művelete. Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. Engedélyezési szabályok. Használjon egy PUT hívást az engedélyezési szabály frissítéséhez. |
> | Műveletek | Microsoft. ServiceBus/névterek/témakörök/engedélyezési szabályok/delete | A témakör engedélyezési szabályainak törlésére szolgáló művelet |
> | Műveletek | Microsoft. ServiceBus/névterek/témakörök/engedélyezési szabályok/listkeys műveletének beolvasása/Action | A kapcsolódási karakterlánc lekérése a témakörhöz |
> | Műveletek | Microsoft. ServiceBus/névterek/témakörök/engedélyezési szabályok/READ |  A témakör engedélyezési szabályainak lekérése |
> | Műveletek | Microsoft. ServiceBus/névterek/témakörök/engedélyezési szabályok/regenerateKeys/Action | Az elsődleges vagy másodlagos kulcs újragenerálása az erőforráshoz |
> | Műveletek | Microsoft. ServiceBus/névterek/témakörök/engedélyezési szabályok/írás | Hozzon létre a témakör engedélyezési szabályait, és frissítse a tulajdonságait. Az engedélyezési szabályokhoz tartozó hozzáférési jogosultságok frissíthetők. |
> | Műveletek | Microsoft. ServiceBus/névterek/témakörök/törlés | Témakör-erőforrás törlésére szolgáló művelet |
> | Műveletek | Microsoft. ServiceBus/névterek/témakörök/olvasás | Témakör-erőforrás leírások listájának beolvasása |
> | Műveletek | Microsoft. ServiceBus/névterek/témakörök/előfizetések/törlés | TopicSubscription-erőforrás törlésére szolgáló művelet |
> | Műveletek | Microsoft. ServiceBus/névterek/témakörök/előfizetések/olvasás | TopicSubscription-erőforrás leírásának beolvasása |
> | Műveletek | Microsoft. ServiceBus/névterek/témakörök/előfizetések/szabályok/törlés | Szabály-erőforrás törlésére szolgáló művelet |
> | Műveletek | Microsoft. ServiceBus/névterek/témakörök/előfizetések/szabályok/olvasás | Szabály erőforrás-leírások listájának beolvasása |
> | Műveletek | Microsoft. ServiceBus/névterek/témakörök/előfizetések/szabályok/írás | Szabály tulajdonságainak létrehozása vagy frissítése. |
> | Műveletek | Microsoft. ServiceBus/névterek/témakörök/előfizetések/írás | TopicSubscription tulajdonságainak létrehozása vagy frissítése. |
> | Műveletek | Microsoft. ServiceBus/névterek/témakörök/írás | Témakör tulajdonságainak létrehozása vagy frissítése. |
> | Műveletek | Microsoft. ServiceBus/névterek/virtualNetworkRules/delete | VNET-szabály erőforrásának törlése |
> | Műveletek | Microsoft. ServiceBus/névterek/virtualNetworkRules/olvasás | VNET-szabály erőforrásának beolvasása |
> | Műveletek | Microsoft. ServiceBus/névterek/virtualNetworkRules/írás | VNET-szabály erőforrásának létrehozása |
> | Műveletek | Microsoft. ServiceBus/névterek/írás | Hozzon létre egy névtér-erőforrást, és frissítse a tulajdonságait. A névtér címkéi és kapacitása a frissíthető tulajdonságok. |
> | Műveletek | Microsoft. ServiceBus/Operations/READ | Műveletek beolvasása |
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
> | Műveletek | Microsoft. SignalRService/szignáló/regeneratekey/művelet | A Signaler-hozzáférési kulcsok értékének módosítása a felügyeleti portálon vagy az API-n keresztül |
> | Műveletek | Microsoft. SignalRService/szignáló/újraindítás/művelet | Az Azure Signaler szolgáltatás újraindítása a felügyeleti portálon vagy API-n keresztül. Bizonyos állásidő lesz. |
> | Műveletek | Microsoft. SignalRService/szignáló/írás | A jelző beállításainak és konfigurációjának módosítása a felügyeleti portálon vagy az API-n keresztül |
> | Műveletek | Microsoft. SignalRService/regisztráció/művelet | A Microsoft. SignalRService erőforrás-szolgáltató regisztrációjának törlése előfizetéssel |

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Solutions/applicationDefinitions/applicationArtifacts/olvasás | Felsorolja az alkalmazás definíciójának alkalmazási összetevőit. |
> | Műveletek | Microsoft. Solutions/applicationDefinitions/delete | Eltávolít egy alkalmazás-definíciót. |
> | Műveletek | Microsoft. Solutions/applicationDefinitions/olvasás | Az alkalmazás-definíciók listájának beolvasása. |
> | Műveletek | Microsoft. Solutions/applicationDefinitions/írás | Alkalmazás-definíció hozzáadása vagy módosítása. |
> | Műveletek | Microsoft. Solutions/Applications/applicationArtifacts/READ | Felsorolja az alkalmazás-összetevőket. |
> | Műveletek | Microsoft. Solutions/alkalmazások/törlés | Eltávolít egy alkalmazást. |
> | Műveletek | Microsoft. Solutions/alkalmazások/olvasás | Az alkalmazások listájának beolvasása. |
> | Műveletek | Microsoft. Solutions/Applications/refreshPermissions/Action | Frissíti az alkalmazás engedélyeit. |
> | Műveletek | Microsoft. Solutions/Applications/updateAccess/Action | Frissíti az alkalmazás-hozzáférést. |
> | Műveletek | Microsoft. Solutions/alkalmazások/írás | Létrehoz egy alkalmazást. |
> | Műveletek | Microsoft. Solutions/jitRequests/delete | JitRequest eltávolítása |
> | Műveletek | Microsoft. Solutions/jitRequests/olvasás | JitRequests listájának beolvasása |
> | Műveletek | Microsoft. Solutions/jitRequests/írás | Létrehoz egy JitRequest |
> | Műveletek | Microsoft. Solutions/Locations/operationStatuses/READ | Az erőforrás műveleti állapotának beolvasása. |
> | Műveletek | Microsoft. Solutions/Operations/READ | A műveletek listájának beolvasása. |
> | Műveletek | Microsoft. Solutions/regisztráció/művelet | Regisztráljon a megoldásokra. |
> | Műveletek | Microsoft. Solutions/regisztráció/művelet | A megoldások regisztrációjának törlése. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. SQL/checkNameAvailability/művelet | Győződjön meg arról, hogy az adott kiszolgálónév elérhető-e a világ egy adott előfizetéshez való kiépítés céljából. |
> | Műveletek | Microsoft. SQL/instancePools/delete | Egy példány készletének törlése |
> | Műveletek | Microsoft. SQL/instancePools/READ | Egy példány készletének beolvasása |
> | Műveletek | Microsoft. SQL/instancePools/használat/olvasás | Példány-készlet használati adatainak beolvasása |
> | Műveletek | Microsoft. SQL/instancePools/írás | Egy példány-készlet létrehozása vagy frissítése |
> | Műveletek | Microsoft. SQL/Locations/auditingSettingsAzureAsyncOperation/READ | A kiterjesztett kiszolgáló blob-naplózási házirendjének beállítása művelet eredményének beolvasása |
> | Műveletek | Microsoft. SQL/Locations/auditingSettingsOperationResults/READ | A kiszolgálói blob naplózási házirendjének beállítása művelet eredményének beolvasása |
> | Műveletek | Microsoft. SQL/Locations/képességek/olvasás | Az előfizetéshez tartozó képességek beolvasása egy adott helyen |
> | Műveletek | Microsoft. SQL/Locations/databaseAzureAsyncOperation/READ | Egy adatbázis-művelet állapotának beolvasása. |
> | Műveletek | Microsoft. SQL/Locations/databaseOperationResults/READ | Egy adatbázis-művelet állapotának beolvasása. |
> | Műveletek | Microsoft. SQL/Locations/deletedServerAsyncOperation/READ | Folyamatban lévő műveletek beolvasása a törölt kiszolgálón |
> | Műveletek | Microsoft. SQL/Locations/deletedServerOperationResults/READ | Folyamatban lévő műveletek beolvasása a törölt kiszolgálón |
> | Műveletek | Microsoft. SQL/Locations/deletedServers/READ | Visszaadja a törölt kiszolgálók listáját, vagy lekéri a megadott törölt kiszolgáló tulajdonságait. |
> | Műveletek | Microsoft. SQL/Locations/deletedServers/Recover/Action | Törölt kiszolgáló helyreállítása |
> | Műveletek | Microsoft. SQL/Locations/elasticPoolAzureAsyncOperation/READ | Egy rugalmas készlet aszinkron műveletéhez tartozó Azure aszinkron művelet beolvasása |
> | Műveletek | Microsoft. SQL/Locations/elasticPoolOperationResults/READ | Egy rugalmas készlet műveletének eredményét kapja meg. |
> | Műveletek | Microsoft. SQL/Locations/encryptionProtectorAzureAsyncOperation/READ | Folyamatban lévő műveletek beolvasása transzparens adattitkosítási titkosítási védőn |
> | Műveletek | Microsoft. SQL/Locations/encryptionProtectorOperationResults/READ | Folyamatban lévő műveletek beolvasása transzparens adattitkosítási titkosítási védőn |
> | Műveletek | Microsoft. SQL/Locations/extendedAuditingSettingsAzureAsyncOperation/READ | A kiterjesztett kiszolgáló blob-naplózási házirendjének beállítása művelet eredményének beolvasása |
> | Műveletek | Microsoft. SQL/Locations/extendedAuditingSettingsOperationResults/READ | A kiterjesztett kiszolgáló blob-naplózási házirendjének beállítása művelet eredményének beolvasása |
> | Műveletek | Microsoft. SQL/Locations/firewallRulesAzureAsyncOperation/READ | Egy tűzfalszabály-művelet állapotának beolvasása. |
> | Műveletek | Microsoft. SQL/Locations/firewallRulesOperationResults/READ | Egy tűzfalszabály-művelet állapotának beolvasása. |
> | Műveletek | Microsoft. SQL/Locations/instanceFailoverGroups/delete | Töröl egy meglévő példány-feladatátvételi csoportot. |
> | Műveletek | Microsoft. SQL/Locations/instanceFailoverGroups/feladatátvétel/művelet | Tervezett feladatátvételt hajt végre egy meglévő példány-feladatátvételi csoportban. |
> | Műveletek | Microsoft. SQL/Locations/instanceFailoverGroups/forceFailoverAllowDataLoss/Action | Kényszerített feladatátvételt hajt végre egy meglévő példány-feladatátvételi csoportban. |
> | Műveletek | Microsoft. SQL/Locations/instanceFailoverGroups/READ | A példányok feladatátvételi csoportjainak listáját adja vissza, vagy lekéri a megadott példány-feladatátvételi csoport tulajdonságait. |
> | Műveletek | Microsoft. SQL/Locations/instanceFailoverGroups/Write | Létrehoz egy példány-feladatátvételi csoportot a megadott paraméterekkel, vagy frissíti a megadott példány-feladatátvételi csoport tulajdonságait vagy címkéit. |
> | Műveletek | Microsoft. SQL/Locations/instancePoolAzureAsyncOperation/READ | Egy példány-készlet művelet állapotának beolvasása |
> | Műveletek | Microsoft. SQL/Locations/instancePoolOperationResults/READ | Egy példány-készlet művelet eredményének beolvasása |
> | Műveletek | Microsoft. SQL/Locations/interfaceEndpointProfileAzureAsyncOperation/READ | Egy adott interfész-végpont Azure aszinkron műveletének részleteit adja vissza. |
> | Műveletek | Microsoft. SQL/Locations/interfaceEndpointProfileOperationResults/READ | A megadott csatoló-végponti profil műveletének részleteit adja vissza. |
> | Műveletek | Microsoft. SQL/Locations/jobAgentAzureAsyncOperation/READ | Lekéri egy ügynök műveletének állapotát. |
> | Műveletek | Microsoft. SQL/Locations/jobAgentOperationResults/READ | Lekérdezi egy ügynök műveletének eredményét. |
> | Műveletek | Microsoft. SQL/Locations/longTermRetentionBackups/READ | Felsorolja a hely összes kiszolgálójának összes adatbázisának hosszú távú adatmegőrzési biztonsági másolatait. |
> | Műveletek | Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionBackups/READ | Felsorolja a kiszolgáló összes adatbázisának hosszú távú adatmegőrzési biztonsági másolatait. |
> | Műveletek | Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | Hosszú távú adatmegőrzési biztonsági mentés törlése |
> | Műveletek | Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/READ | Egy adatbázis hosszú távú adatmegőrzési biztonsági másolatait sorolja fel |
> | Műveletek | Microsoft. SQL/Locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/Action | Felügyelt adatbázis-visszaállítási művelet befejezése |
> | Műveletek | Microsoft. SQL/Locations/managedInstanceEncryptionProtectorAzureAsyncOperation/READ | Folyamatban lévő műveleteket végez a transzparens adattitkosítás felügyelt példányának titkosítási oltalmazóján |
> | Műveletek | Microsoft. SQL/Locations/managedInstanceEncryptionProtectorOperationResults/READ | Folyamatban lévő műveleteket végez a transzparens adattitkosítás felügyelt példányának titkosítási oltalmazóján |
> | Műveletek | Microsoft. SQL/Locations/managedInstanceKeyAzureAsyncOperation/READ | Folyamatban lévő műveletek beolvasása transzparens adattitkosítás felügyelt példányainak kulcsaihoz |
> | Műveletek | Microsoft. SQL/Locations/managedInstanceKeyOperationResults/READ | Folyamatban lévő műveletek beolvasása transzparens adattitkosítás felügyelt példányainak kulcsaihoz |
> | Műveletek | Microsoft. SQL/Locations/managedInstanceLongTermRetentionPolicyAzureAsyncOperation/READ | Egy felügyelt adatbázis hosszú távú adatmegőrzési házirend-műveletének állapotát kérdezi le. |
> | Műveletek | Microsoft. SQL/Locations/managedInstanceLongTermRetentionPolicyOperationResults/READ | Egy felügyelt adatbázis hosszú távú adatmegőrzési házirend-műveletének állapotát kérdezi le. |
> | Műveletek | Microsoft. SQL/Locations/managedShortTermRetentionPolicyOperationResults/READ | Rövid távú adatmegőrzési házirend-művelet állapotának beolvasása |
> | Műveletek | Microsoft. SQL/Locations/managedTransparentDataEncryptionAzureAsyncOperation/READ | Folyamatban lévő műveletek beolvasása a felügyelt adatbázis transzparens adattitkosításával |
> | Műveletek | Microsoft. SQL/Locations/managedTransparentDataEncryptionOperationResults/READ | Folyamatban lévő műveletek beolvasása a felügyelt adatbázis transzparens adattitkosításával |
> | Műveletek | Microsoft. SQL/Locations/privateEndpointConnectionAzureAsyncOperation/READ | Egy privát végponti kapcsolatok műveletének eredményét kapja meg |
> | Műveletek | Microsoft. SQL/Locations/privateEndpointConnectionOperationResults/READ | Egy privát végponti kapcsolatok műveletének eredményét kapja meg |
> | Műveletek | Microsoft. SQL/Locations/privateEndpointConnectionProxyAzureAsyncOperation/READ | A magánhálózati végponti kapcsolatok proxyjának eredményének beolvasása |
> | Műveletek | Microsoft. SQL/Locations/privateEndpointConnectionProxyOperationResults/READ | A magánhálózati végponti kapcsolatok proxyjának eredményének beolvasása |
> | Műveletek | Microsoft. SQL/Locations/READ | Egy adott előfizetés elérhető helyeinek beolvasása |
> | Műveletek | Microsoft. SQL/Locations/serverKeyAzureAsyncOperation/READ | Folyamatban lévő műveletek beolvasása transzparens adattitkosítási kiszolgáló kulcsain |
> | Műveletek | Microsoft. SQL/Locations/serverKeyOperationResults/READ | Folyamatban lévő műveletek beolvasása transzparens adattitkosítási kiszolgáló kulcsain |
> | Műveletek | Microsoft. SQL/Locations/shortTermRetentionPolicyOperationResults/READ | Rövid távú adatmegőrzési házirend-művelet állapotának beolvasása |
> | Műveletek | Microsoft. SQL/Locations/syncAgentOperationResults/READ | A Szinkronizáló ügynök erőforrás-műveletének eredményének beolvasása |
> | Műveletek | Microsoft. SQL/Locations/syncDatabaseIds/READ | Egy adott régió és előfizetés szinkronizálási adatbázis-azonosítóinak beolvasása |
> | Műveletek | Microsoft. SQL/Locations/syncGroupOperationResults/READ | A szinkronizálási csoport erőforrása művelet eredményének beolvasása |
> | Műveletek | Microsoft. SQL/Locations/syncMemberOperationResults/READ | A szinkronizálási tag erőforrása művelet eredményének beolvasása |
> | Műveletek | Microsoft. SQL/Locations/használat/olvasás | Az előfizetéshez tartozó használati metrikák gyűjteményének beolvasása egy helyen |
> | Műveletek | Microsoft. SQL/Locations/virtualNetworkRulesAzureAsyncOperation/READ | A megadott virtuális hálózati szabályok Azure aszinkron műveletének részleteit adja vissza.  |
> | Műveletek | Microsoft. SQL/Locations/virtualNetworkRulesOperationResults/READ | A virtuális hálózati szabályok megadott műveletének részleteit adja vissza.  |
> | Műveletek | Microsoft. SQL/managedInstances/rendszergazdák/törlés | A felügyelt példány meglévő rendszergazdája törölve. |
> | Műveletek | Microsoft. SQL/managedInstances/rendszergazdák/olvasás | Lekéri a felügyelt példányok rendszergazdáinak listáját. |
> | Műveletek | Microsoft. SQL/managedInstances/rendszergazdák/írás | A felügyelt példányok rendszergazdájának létrehozása vagy frissítése a megadott paraméterekkel. |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/backupLongTermRetentionPolicies/READ | Hosszú távú adatmegőrzési szabályzat beolvasása egy felügyelt adatbázishoz |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/backupLongTermRetentionPolicies/írás | Egy felügyelt adatbázis hosszú távú adatmegőrzési szabályzatának frissítése |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/backupShortTermRetentionPolicies/READ | Egy felügyelt adatbázis rövid távú adatmegőrzési szabályzatának beolvasása |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/backupShortTermRetentionPolicies/írás | Egy felügyelt adatbázis rövid távú adatmegőrzési szabályzatának frissítése |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/oszlopok/olvasás | Egy felügyelt adatbázis oszlopainak listájának visszaadása |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/completeRestore/művelet | Felügyelt adatbázis-visszaállítási művelet befejezése |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/currentSensitivityLabels/READ | Egy adott adatbázis érzékenységi címkéjének listázása |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/currentSensitivityLabels/írás | A Batch Update érzékenységi címkéi |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/törlés | Meglévő felügyelt adatbázis törlése |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/szolgáltatók/Microsoft. betekintés/diagnosticSettings/olvasás | Az erőforrás diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/szolgáltatók/Microsoft. bepillantások/diagnosticSettings/írás | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/szolgáltatók/Microsoft. betekintés/logDefinitions/olvasás | Lekéri a felügyelt példány adatbázisaihoz elérhető naplókat |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/olvasás | Meglévő felügyelt adatbázis beolvasása |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/recommendedSensitivityLabels/READ | Egy adott adatbázis érzékenységi címkéjének listázása |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/recommendedSensitivityLabels/írás | A Batch Update ajánlott érzékenységi címkéi |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/restoreDetails/READ | A felügyelt adatbázis-visszaállítás részleteit adja vissza, amíg a visszaállítás folyamatban van. |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/sémák/olvasás | Felügyelt adatbázis-séma beszerzése. |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/sémák/táblák/oszlopok/olvasás | Felügyelt adatbázis oszlopának beolvasása |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/sémák/táblák/oszlopok/sensitivityLabels/delete | Egy adott oszlop érzékenységi címkéjének törlése |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/sémák/táblák/oszlopok/sensitivityLabels/letiltás/művelet | Az adott oszlopra vonatkozó érzékenységi javaslatok letiltása |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/sémák/táblák/oszlopok/sensitivityLabels/engedélyezés/művelet | Érzékenységi javaslatok engedélyezése egy adott oszlopban |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/sémák/táblák/oszlopok/sensitivityLabels/olvasás | Egy adott oszlop érzékenységi címkéjének beolvasása |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/sémák/táblák/oszlopok/sensitivityLabels/írás | Egy adott oszlop érzékenységi címkéjének létrehozása vagy frissítése |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/sémák/táblák/olvasás | Felügyelt adatbázistábla beszerzése |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/securityAlertPolicies/READ | Az adott kiszolgálóhoz konfigurált felügyelt adatbázis-veszélyforrások észlelési házirendjeinek listájának beolvasása |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/securityAlertPolicies/írás | Az adatbázis-veszélyforrások észlelési házirendjének módosítása egy adott felügyelt adatbázis esetében |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/securityEvents/READ | A felügyelt adatbázis biztonsági eseményeinek beolvasása |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/sensitivityLabels/READ | Egy adott adatbázis érzékenységi címkéjének listázása |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/transparentDataEncryption/READ | Az adatbázis transzparens adattitkosítás adatainak beolvasása egy adott felügyelt adatbázisban |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/transparentDataEncryption/írás | Az adatbázis transzparens adattitkosítás módosítása egy adott felügyelt adatbázisra vonatkozóan |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/vulnerabilityAssessments/delete | Egy adott adatbázis sebezhetőségi felmérésének eltávolítása |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/vulnerabilityAssessments/READ | A sebezhetőségi felmérési szabályzatok beolvasása egy givendatabase |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/vulnerabilityAssessments/szabályok/alaptervek/törlés | Az adott adatbázis sebezhetőségi felmérési szabályának alaptervének eltávolítása |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/vulnerabilityAssessments/szabályok/alaptervek/olvasás | Egy adott adatbázis sebezhetőségi felmérési szabályának alaptervének beolvasása |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/vulnerabilityAssessments/szabályok/alaptervek/írás | Egy adott adatbázis sebezhetőségi felmérési szabályának alaptervének módosítása |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/vulnerabilityAssessments/vizsgálatok/exportálás/művelet | Meglévő vizsgálat eredményének konvertálása emberi olvasási formátumba. Ha már létezik, semmi nem történik |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/vulnerabilityAssessments/Scans/initiateScan/Action | Sebezhetőség-felmérési adatbázis vizsgálatának végrehajtása. |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/vulnerabilityAssessments/vizsgálatok/olvasás | Visszaadja az adatbázis sebezhetőségi felmérési rekordjainak listáját, vagy beolvassa a megadott vizsgálati AZONOSÍTÓhoz tartozó vizsgálati rekordot. |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/vulnerabilityAssessments/írás | Egy adott adatbázis sebezhetőségi felmérésének módosítása |
> | Műveletek | Microsoft. SQL/managedInstances/adatbázisok/írás | Létrehoz egy új adatbázist, vagy frissít egy meglévő adatbázist. |
> | Műveletek | Microsoft. SQL/managedInstances/delete | Egy meglévő felügyelt példány törlése. |
> | Műveletek | Microsoft. SQL/managedInstances/encryptionProtector/READ | A kiszolgáló titkosítási védelmi adatainak listáját adja vissza, vagy lekéri a megadott kiszolgálói titkosítási védő tulajdonságait. |
> | Műveletek | Microsoft. SQL/managedInstances/encryptionProtector/revalidate/Action | A megadott kiszolgálói titkosítási fólia tulajdonságainak frissítése. |
> | Műveletek | Microsoft. SQL/managedInstances/encryptionProtector/Write | A megadott kiszolgálói titkosítási fólia tulajdonságainak frissítése. |
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
> | Műveletek | Microsoft. SQL/privateEndpointConnectionsApproval/művelet | Meghatározza, hogy a felhasználó jóváhagyhat-e privát végponti kapcsolatokat |
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
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/importExportOperationResults/olvasás | Folyamatban lévő importálási/exportálási műveletek beolvasása |
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
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/sémák/olvasás | Adatbázis-séma beszerzése. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/sémák/táblák/oszlopok/olvasás | Adatbázis oszlopának beolvasása. |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/sémák/táblák/oszlopok/sensitivityLabels/törlés | Egy adott oszlop érzékenységi címkéjének törlése |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/sémák/táblák/oszlopok/sensitivityLabels/letiltás/művelet | Az adott oszlopra vonatkozó érzékenységi javaslatok letiltása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/sémák/táblák/oszlopok/sensitivityLabels/engedélyezés/művelet | Érzékenységi javaslatok engedélyezése egy adott oszlopban |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/sémák/táblák/oszlopok/sensitivityLabels/olvasás | Egy adott oszlop érzékenységi címkéjének beolvasása |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/sémák/táblák/oszlopok/sensitivityLabels/írás | Egy adott oszlop érzékenységi címkéjének létrehozása vagy frissítése |
> | Műveletek | Microsoft. SQL/kiszolgálók/adatbázisok/sémák/táblák/olvasás | Adatbázis táblázatának beolvasása. |
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
> | Műveletek | Microsoft. SQL/kiszolgálók/encryptionProtector/olvasás | A kiszolgáló titkosítási védelmi adatainak listáját adja vissza, vagy lekéri a megadott kiszolgálói titkosítási védő tulajdonságait. |
> | Műveletek | Microsoft. SQL/Servers/encryptionProtector/revalidate/Action | A megadott kiszolgálói titkosítási fólia tulajdonságainak frissítése. |
> | Műveletek | Microsoft. SQL/kiszolgálók/encryptionProtector/írás | A megadott kiszolgálói titkosítási fólia tulajdonságainak frissítése. |
> | Műveletek | Microsoft. SQL/kiszolgálók/extendedAuditingSettings/olvasás | Az adott kiszolgálón konfigurált kiterjesztett kiszolgálói blob-naplózási szabályzat részleteinek beolvasása |
> | Műveletek | Microsoft. SQL/kiszolgálók/extendedAuditingSettings/írás | A kiterjesztett kiszolgáló blob-naplózásának módosítása egy adott kiszolgálón |
> | Műveletek | Microsoft. SQL/Servers/failoverGroups/delete | Töröl egy meglévő feladatátvételi csoportot. |
> | Műveletek | Microsoft. SQL/kiszolgálók/failoverGroups/feladatátvétel/művelet | Tervezett feladatátvételt hajt végre egy meglévő feladatátvételi csoportban. |
> | Műveletek | Microsoft. SQL/Servers/failoverGroups/forceFailoverAllowDataLoss/Action | Kényszerített feladatátvételt hajt végre egy meglévő feladatátvételi csoportban. |
> | Műveletek | Microsoft. SQL/kiszolgálók/failoverGroups/olvasás | A feladatátvételi csoportok listáját adja vissza, vagy lekéri a megadott feladatátvételi csoport tulajdonságait. |
> | Műveletek | Microsoft. SQL/kiszolgálók/failoverGroups/írás | Létrehoz egy feladatátvételi csoportot a megadott paraméterekkel, vagy frissíti a megadott feladatátvételi csoport tulajdonságait vagy címkéit. |
> | Műveletek | Microsoft. SQL/Servers/firewallRules/delete | Töröl egy meglévő kiszolgálói tűzfalszabály-szabályt. |
> | Műveletek | Microsoft. SQL/kiszolgálók/firewallRules/olvasás | Visszaadja a kiszolgálói tűzfalszabályok listáját, vagy lekéri a megadott kiszolgálói tűzfalszabály tulajdonságait. |
> | Műveletek | Microsoft. SQL/kiszolgálók/firewallRules/írás | Létrehoz egy kiszolgálói tűzfalszabály a megadott paraméterekkel, frissíti a megadott szabály tulajdonságait, vagy felülírja az összes meglévő szabályt az új kiszolgálói tűzfalszabályok (ok) használatával. |
> | Műveletek | Microsoft. SQL/kiszolgálók/importálás/művelet | Új adatbázis létrehozása a kiszolgálón és a séma és az adatok központi telepítése DacPac-csomagból |
> | Műveletek | Microsoft. SQL/kiszolgálók/importExportOperationResults/olvasás | Folyamatban lévő importálási/exportálási műveletek beolvasása |
> | Műveletek | Microsoft. SQL/kiszolgálók/inaccessibleDatabases/olvasás | A nem elérhető adatbázis (ok) listájának visszaadása egy logikai kiszolgálón. |
> | Műveletek | Microsoft. SQL/Servers/interfaceEndpointProfiles/delete | A megadott csatoló-végponti profil törlése |
> | Műveletek | Microsoft. SQL/kiszolgálók/interfaceEndpointProfiles/olvasás | A megadott csatoló-végpont profiljának tulajdonságait adja vissza. |
> | Műveletek | Microsoft. SQL/kiszolgálók/interfaceEndpointProfiles/írás | Létrehoz egy illesztőfelület-végponti profilt a megadott paraméterekkel, vagy frissíti a megadott csatoló-végpont tulajdonságait vagy címkéit. |
> | Műveletek | Microsoft. SQL/Servers/jobAgents/delete | Az Azure SQL DB-feladatok ügynökének törlése |
> | Műveletek | Microsoft. SQL/kiszolgálók/jobAgents/olvasás | Azure SQL Database-feladatok ügynökének beolvasása |
> | Műveletek | Microsoft. SQL/kiszolgálók/jobAgents/írás | Létrehoz vagy frissít egy Azure SQL DB-feladatot kezelő ügynököt |
> | Műveletek | Microsoft. SQL/kiszolgálók/kulcsok/törlés | Töröl egy meglévő kiszolgálói kulcsot. |
> | Műveletek | Microsoft. SQL/kiszolgálók/kulcsok/olvasás | Visszaadja a kiszolgálói kulcsok listáját, vagy lekéri a megadott kiszolgálói kulcs tulajdonságait. |
> | Műveletek | Microsoft. SQL/kiszolgálók/kulcsok/írás | Létrehoz egy kulcsot a megadott paraméterekkel, vagy frissíti a megadott kiszolgálói kulcs tulajdonságait vagy címkéit. |
> | Műveletek | Microsoft. SQL/kiszolgálók/operationResults/olvasás | Folyamatban lévő kiszolgálói műveletek beolvasása |
> | Műveletek | Microsoft. SQL/kiszolgálók/műveletek/olvasás | A kiszolgálón végrehajtott műveletek listájának visszaadása |
> | Műveletek | Microsoft. SQL/Servers/privateEndpointConnectionProxies/delete | Meglévő privát végponti kapcsolatkezelő törlése |
> | Műveletek | Microsoft. SQL/kiszolgálók/privateEndpointConnectionProxies/olvasás | A privát végponti kapcsolatok proxyinak listáját adja vissza, vagy beolvassa a megadott privát végpont-összekötő tulajdonságait. |
> | Műveletek | Microsoft. SQL/Servers/privateEndpointConnectionProxies/validate/Action | Egy privát végponti kapcsolatok ellenőrzése az NRP oldalról |
> | Műveletek | Microsoft. SQL/kiszolgálók/privateEndpointConnectionProxies/írás | Létrehoz egy privát végponti ügyfélkapcsolati proxyt a megadott paraméterekkel, vagy frissíti a megadott magánhálózati végponthoz tartozó tulajdonságokat vagy címkéket. |
> | Műveletek | Microsoft. SQL/Servers/privateEndpointConnections/delete | Meglévő magánhálózati végponti kapcsolatok törlése |
> | Műveletek | Microsoft. SQL/kiszolgálók/privateEndpointConnections/olvasás | A privát végponti kapcsolatok listáját adja vissza, vagy lekéri a megadott privát végponti kapcsolat tulajdonságait. |
> | Műveletek | Microsoft. SQL/kiszolgálók/privateEndpointConnections/írás | Meglévő magánhálózati végponti kapcsolatok jóváhagyása vagy elutasítása |
> | Műveletek | Microsoft. SQL/kiszolgálók/privateEndpointConnectionsApproval/művelet | Meghatározza, hogy a felhasználó jóváhagyhat-e privát végponti kapcsolatokat |
> | Műveletek | Microsoft. SQL/kiszolgálók/privateLinkResources/olvasás | A kapcsolódó SQL Serverhez tartozó magánhálózati kapcsolati erőforrások beolvasása |
> | Műveletek | Microsoft. SQL/kiszolgálók/szolgáltatók/Microsoft. bepillantást/metricDefinitions/READ | A kiszolgálók számára elérhető metrikák visszatérési típusai |
> | Műveletek | Microsoft. SQL/kiszolgálók/olvasás | A kiszolgálók listájának visszaadása vagy a megadott kiszolgáló tulajdonságainak beolvasása. |
> | Műveletek | Microsoft. SQL/kiszolgálók/recommendedElasticPools/adatbázisok/olvasás | Az ajánlott rugalmas adatbázis-készletek metrikáinak beolvasása egy adott kiszolgálóhoz |
> | Műveletek | Microsoft. SQL/kiszolgálók/recommendedElasticPools/olvasás | A rugalmas adatbázis-készletek javaslatának beolvasása, amely csökkenti a költségeket, vagy javítja a teljesítményt a korábbi erőforrások kihasználtsága alapján |
> | Műveletek | Microsoft. SQL/kiszolgálók/recoverableDatabases/olvasás | Ez a művelet az élő adatbázis vész-helyreállítására szolgál az adatbázis utolsó ismert jó biztonsági mentési pontra való visszaállításához. Az utolsó jó biztonsági mentésre vonatkozó adatokat ad vissza, de az doesn\u0027t valójában visszaállítja az adatbázist. |
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
> | Műveletek | Microsoft. Storage/storageAccounts/PrivateEndpointConnectionsApproval/művelet | Privát végponti kapcsolatok jóváhagyása |
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
> | Műveletek | Microsoft. storagesync/helyszínek/munkafolyamatok/műveletek/olvasás | Aszinkron művelet állapotának beolvasása |
> | Műveletek | Microsoft. storagesync/Operations/READ | A támogatott műveletek listájának beolvasása |
> | Műveletek | Microsoft. storagesync/regisztráció/művelet | Regisztrálja az előfizetést a Storage Sync Provider számára |
> | Műveletek | Microsoft. storagesync/storageSyncServices/delete | A Storage Sync szolgáltatásainak törlése |
> | Műveletek | Microsoft. storagesync/storageSyncServices/Providers/Microsoft. bepillantást/metricDefinitions/READ | A Storage Sync Services elérhető metrikáinak beolvasása |
> | Műveletek | Microsoft. storagesync/storageSyncServices/READ | A Storage Sync szolgáltatásainak beolvasása |
> | Műveletek | Microsoft. storagesync/storageSyncServices/registeredServers/delete | Bármely regisztrált kiszolgáló törlése |
> | Műveletek | Microsoft. storagesync/storageSyncServices/registeredServers/Providers/Microsoft. bepillantást/metricDefinitions/READ | A regisztrált kiszolgáló elérhető metrikáinak beolvasása |
> | Műveletek | Microsoft. storagesync/storageSyncServices/registeredServers/READ | Bármely regisztrált kiszolgáló beolvasása |
> | Műveletek | Microsoft. storagesync/storageSyncServices/registeredServers/Write | Bármely regisztrált kiszolgáló létrehozása vagy frissítése |
> | Műveletek | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | Bármilyen Felhőbeli végpont törlése |
> | Műveletek | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/READ | Aszinkron biztonsági mentési/visszaállítási művelet állapotának beolvasása |
> | Műveletek | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/művelet | A művelet meghívása a biztonsági mentés után |
> | Műveletek | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/művelet | A művelet meghívása a visszaállítás után |
> | Műveletek | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/előbiztonsági Mentés/művelet | A művelet meghívása a biztonsági mentés előtt |
> | Műveletek | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/elővisszaállítás/művelet | A művelet meghívása a visszaállítás előtt |
> | Műveletek | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/READ | Felhőbeli végpontok beolvasása |
> | Műveletek | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/művelet | Szívverés visszaállítása |
> | Műveletek | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/triggerChangeDetection/művelet | Ennek a műveletnek a meghívásával aktiválhatja a Felhőbeli végpontok módosításainak észlelését |
> | Műveletek | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/Write | Felhőalapú végpontok létrehozása vagy frissítése |
> | Műveletek | Microsoft. storagesync/storageSyncServices/syncGroups/delete | Szinkronizált csoportok törlése |
> | Műveletek | Microsoft. storagesync/storageSyncServices/syncGroups/Providers/Microsoft. bepillantást/metricDefinitions/READ | A szinkronizálási csoportok elérhető metrikáinak beolvasása |
> | Műveletek | Microsoft. storagesync/storageSyncServices/syncGroups/READ | Bármely szinkronizálási csoport olvasása |
> | Műveletek | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | Bármely kiszolgálói végpont törlése |
> | Műveletek | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints/Providers/Microsoft. bepillantást/metricDefinitions/READ | A kiszolgálói végpontok elérhető metrikáinak beolvasása |
> | Műveletek | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints/READ | Bármely kiszolgálói végpont olvasása |
> | Műveletek | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/művelet | A művelet meghívása a fájlok egy kiszolgálóra való felidézéséhez |
> | Műveletek | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints/Write | Bármely kiszolgálói végpont létrehozása vagy frissítése |
> | Műveletek | Microsoft. storagesync/storageSyncServices/syncGroups/Write | Szinkronizált csoportok létrehozása vagy frissítése |
> | Műveletek | Microsoft. storagesync/storageSyncServices/munkafolyamatok/operationresults/olvasás | Aszinkron művelet állapotának beolvasása |
> | Műveletek | Microsoft. storagesync/storageSyncServices/munkafolyamatok/műveletek/olvasás | Aszinkron művelet állapotának beolvasása |
> | Műveletek | Microsoft. storagesync/storageSyncServices/munkafolyamatok/olvasás | Munkafolyamatok olvasása |
> | Műveletek | Microsoft. storagesync/storageSyncServices/Write | Tárolási szinkronizálási szolgáltatások létrehozása vagy frissítése |
> | Műveletek | Microsoft. storagesync/regisztráció/művelet | A Storage Sync Provider előfizetés regisztrációjának törlése |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. StorSimple/Managers/accessControlRecords/delete | Törli a Access Control rekordokat. |
> | Műveletek | Microsoft. StorSimple/Managers/accessControlRecords/operationResults/READ | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/Manager/accessControlRecords/READ | A Access Control rekordok listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/Manager/accessControlRecords/Write | A Access Control rekordok létrehozása vagy frissítése |
> | Műveletek | Microsoft. StorSimple/vezetők/riasztások/olvasás | A riasztások listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/vezetők/biztonsági másolatok/olvasás | A biztonságimásolat-készlet listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/Managers/bandwidthSettings/delete | Meglévő sávszélesség-beállítások törlése (csak 8000 sorozat) |
> | Műveletek | Microsoft. StorSimple/Managers/bandwidthSettings/operationResults/READ | A művelet eredményeinek listázása |
> | Műveletek | Microsoft. StorSimple/Manager/bandwidthSettings/READ | A sávszélesség-beállítások listázása (csak 8000 sorozat) |
> | Műveletek | Microsoft. StorSimple/Manager/bandwidthSettings/Write | Új vagy frissített sávszélesség-beállításokat hoz létre (csak 8000 sorozat) |
> | Műveletek | Microsoft. StorSimple/vezetők/tanúsítványok/írás | Tanúsítványok létrehozása vagy frissítése |
> | Műveletek | Microsoft. StorSimple/vezetők/tanúsítványok/írás | Az erőforrás-tanúsítvány frissítése művelet frissíti az erőforrás/tároló hitelesítő adatait tartalmazó tanúsítványt. |
> | Műveletek | Microsoft. StorSimple/menedzserek/clearAlerts/művelet | Törölje az Eszközkezelőhöz társított összes riasztást. |
> | Műveletek | Microsoft. StorSimple/Manager/cloudApplianceConfigurations/READ | A Cloud Appliance által támogatott konfigurációk listázása |
> | Műveletek | Microsoft. StorSimple/menedzserek/configureDevice/művelet | Eszköz konfigurálása |
> | Műveletek | Microsoft. StorSimple/menedzserek/törlés | Az eszközkezelők törlése |
> | Műveletek | Microsoft. StorSimple/menedzserek/törlés | A tár törlése művelet törli a "Vault" típusú megadott Azure-erőforrást. |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/alertSettings/operationResults/READ | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/alertSettings/olvasás | A riasztási beállítások listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/alertSettings/írás | Riasztási beállítások létrehozása vagy frissítése |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/authorizeForServiceEncryptionKeyRollover/művelet | A szolgáltatás titkosítási kulcsának átváltásának engedélyezése az eszközökön |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/backupPolicies/biztonsági mentés/művelet | Készítsen manuális biztonsági mentést a szabályzat által védett összes kötetre vonatkozó igény szerinti biztonsági mentés létrehozásához. |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/backupPolicies/delete | Meglévő biztonsági mentési házirendek törlése (csak 8000 sorozat) |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/backupPolicies/operationResults/READ | A művelet eredményeinek listázása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/backupPolicies/olvasás | A biztonsági mentési házirendek listázása (csak 8000 sorozat) |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/backupPolicies/ütemtervek/törlés | Meglévő ütemtervek törlése |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/backupPolicies/Schedules/operationResults/READ | A művelet eredményeinek listázása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/backupPolicies/ütemterv/olvasás | Az ütemtervek listázása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/backupPolicies/ütemterv/írás | Új vagy frissítési ütemtervet hoz létre |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/backupPolicies/írás | Új vagy frissítési biztonsági mentési szabályzatok létrehozása (csak 8000 sorozat) |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/biztonsági másolatok/törlés | A biztonságimásolat-készlet törlése |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/biztonsági másolatok/elemek/klónozás/művelet | Megosztás vagy kötet klónozása egy biztonsági mentési elem használatával. |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/biztonsági másolatok/elemek/operationResults/olvasás | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/Backups/operationResults/READ | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/biztonsági másolatok/olvasás | A biztonságimásolat-készlet listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/biztonsági másolatok/visszaállítás/művelet | Állítsa vissza az összes kötetet egy biztonságimásolat-készletből. |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/backupScheduleGroups/delete | A biztonsági mentési ütemterv csoportok törlése |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/backupScheduleGroups/operationResults/READ | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/backupScheduleGroups/olvasás | A biztonsági mentési ütemterv csoportok listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/backupScheduleGroups/írás | A biztonsági mentési ütemterv csoportok létrehozása vagy frissítése |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/chapSettings/delete | A CHAP-beállítások törlése |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/chapSettings/operationResults/READ | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/chapSettings/olvasás | A CHAP-beállítások listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/chapSettings/írás | A CHAP-beállítások létrehozása vagy frissítése |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/inaktiválás/művelet | Inaktiválja az eszközt. |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/törlés | Az eszközök törlése |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/lemezek/olvasás | A lemezek listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/Letöltés/művelet | Egy eszköz frissítéseinek letöltése. |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/feladatátvétel/művelet | Az eszköz feladatátvétele. |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/feladatátvétel/operationResults/olvasás | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/failoverTargets/olvasás | Felsorolja vagy lekéri az eszközök feladatátvételi céljait |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/fileservers/biztonsági mentés/művelet | Készítsen biztonsági másolatot a fájlkiszolgálón. |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/fileservers/delete | A fájlkiszolgálók törlése |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/fileservers/mérőszámok/olvasás | A metrikák listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/menedzserek/eszközök/fileservers/metricsDefinitions/READ | A metrikák definícióinak listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/menedzserek/eszközök/fileservers/operationResults/READ | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/fileservers/olvasás | A fájlkiszolgálók listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/Manager/Devices/fileservers/shares/delete | A megosztások törlése |
> | Műveletek | Microsoft. StorSimple/menedzserek/eszközök/fileservek/megosztások/mérőszámok/olvasás | A metrikák listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/Manager/Devices/fileservers/shares/metricsDefinitions/READ | A metrikák definícióinak listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/Manager/Devices/fileservers/shares/operationResults/READ | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/Manager/Devices/fileservers/shares/READ | A megosztások listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/Manager/Devices/fileservers/shares/Write | Megosztások létrehozása vagy frissítése |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/fileservers/Write | Fájlkiszolgálók létrehozása vagy frissítése |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/hardwareComponentGroups/changeControllerPowerState/Action | Hardver-összetevő csoportok vezérlő energiagazdálkodási állapotának módosítása |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/hardwareComponentGroups/operationResults/READ | A művelet eredményeinek listázása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/hardwareComponentGroups/olvasás | A hardver-összetevő csoportjai listázása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/telepítés/művelet | Frissítések telepítése az eszközön. |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/installUpdates/művelet | Telepíti a frissítéseket az eszközökön (csak 8000 sorozat esetén). |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/iscsiservers/biztonsági mentés/művelet | Készítsen biztonsági másolatot egy iSCSI-kiszolgálóról. |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/iscsiservers/delete | Az iSCSI-kiszolgálók törlése |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/iscsiservers/lemezek/törlés | A lemezek törlése |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/iscsiservers/lemezek/metrikák/olvasás | A metrikák listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/iscsiservers/Disks/metricsDefinitions/READ | A metrikák definícióinak listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/iscsiservers/Disks/operationResults/READ | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/iscsiservers/lemezek/olvasás | A lemezek listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/iscsiservers/lemezek/írás | Lemezek létrehozása vagy frissítése |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/iscsiservers/mérőszámok/olvasás | A metrikák listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/iscsiservers/metricsDefinitions/READ | A metrikák definícióinak listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/iscsiservers/operationResults/READ | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/iscsiservers/olvasás | Az iSCSI-kiszolgálók listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/iscsiservers/írás | ISCSI-kiszolgálók létrehozása vagy frissítése |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/feladatok/megszakítás/művelet | Futó feladat megszakítása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/feladatok/operationResults/olvasás | A művelet eredményeinek listázása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/feladatok/olvasás | A feladatok listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/listFailoverSets/művelet | Egy meglévő eszközhöz tartozó feladatátvevő készletek listázása (csak 8000 sorozat esetén). |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/listFailoverTargets/művelet | Az eszközök feladatátvételi céljainak listázása (csak 8000 sorozat esetén). |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/mérőszámok/olvasás | A metrikák listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/metricsDefinitions/olvasás | A metrikák definícióinak listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/confirmMigration/Action | Ellenőrzi a sikeres áttelepítést, és véglegesíti azt. |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/confirmMigrationStatus/READ | Az áttelepítés megerősítési állapotának listázása |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/Action | Az áttelepítés megerősítési állapotának beolvasása. |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/fetchMigrationEstimate/Action | Az áttelepítési becslési feladatokhoz tartozó állapot beolvasása. |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/fetchMigrationStatus/Action | Az áttelepítés állapotának beolvasása. |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/migrationSourceConfigurations/importálás/művelet | Forrás-konfigurációk importálása az áttelepítéshez |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/migrationEstimate/READ | Az áttelepítési becslés listázása |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/migrationStatus/READ | Az áttelepítés állapotának listázása |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/operationResults/READ | A művelet eredményeinek listázása |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/startMigration/Action | Áttelepítés indítása a forrás-konfigurációk használatával |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/startMigrationEstimate/Action | Az áttelepítési folyamat időtartamának becsléséhez indítsa el a feladatot. |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/networkSettings/operationResults/READ | A művelet eredményeinek listázása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/networkSettings/olvasás | A hálózati beállítások listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/networkSettings/írás | Új vagy frissített hálózati beállításokat hoz létre |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/operationResults/olvasás | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/publicEncryptionKey/művelet | Az Eszközkezelő nyilvános titkosítási kulcsának listázása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/publishSupportPackage/művelet | Egy meglévő eszköz támogatási csomagjának közzététele. A StorSimple-támogatási csomag egy könnyen használható mechanizmus, amely az összes releváns naplót gyűjti, hogy segítséget nyújtson Microsoft ügyfélszolgálatanek a StorSimple-eszközök hibáinak elhárításában. |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/olvasás | Az eszközök listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/scanForUpdates/művelet | Frissítések keresése az eszközön. |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/securitySettings/operationResults/READ | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/securitySettings/olvasás | A biztonsági beállítások listázása |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/securitySettings/syncRemoteManagementCertificate/Action | Egy eszköz távfelügyeleti tanúsítványának szinkronizálása. |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/securitySettings/Update/Action | Frissítse a biztonsági beállításokat. |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/securitySettings/írás | Új vagy frissített biztonsági beállítások létrehozása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/sendTestAlertEmail/művelet | Tesztüzenet küldése e-mailben a konfigurált e-mail-címzetteknek. |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/megosztások/olvasás | A megosztások listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/timeSettings/operationResults/READ | A művelet eredményeinek listázása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/timeSettings/olvasás | Felsorolja vagy lekéri az időbeállításokat |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/timeSettings/írás | Új vagy frissítési idő beállításainak létrehozása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/frissítések/operationResults/olvasás | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/updateSummary típusú/olvasás | Felsorolja vagy lekéri a frissítés összegzését |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/volumeContainers/delete | Meglévő mennyiségi tárolók törlése (csak 8000 sorozat) |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/volumeContainers/mérőszámok/olvasás | A metrikák listázása |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/volumeContainers/metricsDefinitions/READ | A metrikák definícióinak listázása |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/volumeContainers/operationResults/READ | A művelet eredményeinek listázása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/volumeContainers/olvasás | A mennyiségi tárolók listázása (csak 8000 sorozat) |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/volumeContainers/Volumes/delete | Meglévő kötetek törlése |
> | Műveletek | Microsoft. StorSimple/menedzserek/eszközök/volumeContainers/kötetek/mérőszámok/olvasás | A metrikák listázása |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/volumeContainers/Volumes/metricsDefinitions/READ | A metrikák definícióinak listázása |
> | Műveletek | Microsoft. StorSimple/Managers/Devices/volumeContainers/Volumes/operationResults/READ | A művelet eredményeinek listázása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/volumeContainers/kötetek/olvasás | Kötetek listázása |
> | Műveletek | Microsoft. StorSimple/menedzserek/eszközök/volumeContainers/kötetek/írás | Új vagy frissített kötetek létrehozása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/volumeContainers/írás | Létrehoz egy új vagy frissített kötet-tárolót (csak 8000 sorozat) |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/kötetek/olvasás | Kötetek listázása |
> | Műveletek | Microsoft. StorSimple/vezetők/eszközök/írás | Eszközök létrehozása vagy frissítése |
> | Műveletek | Microsoft. StorSimple/Manager/encryptionSettings/READ | A titkosítási beállítások listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/Managers/extendedInformation/delete | A kiterjesztett tár adatainak törlése |
> | Műveletek | Microsoft. StorSimple/Managers/extendedInformation/delete | A kiterjesztett adatok beolvasása művelet lekérdezi egy objektum kiterjesztett adatait, amely a (z) típusú Azure-erőforrást jelképezi? |
> | Műveletek | Microsoft. StorSimple/Manager/extendedInformation/READ | A kiterjesztett tár adatainak listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/Manager/extendedInformation/READ | A kiterjesztett adatok beolvasása művelet lekérdezi egy objektum kiterjesztett adatait, amely a (z) típusú Azure-erőforrást jelképezi? |
> | Műveletek | Microsoft. StorSimple/Manager/extendedInformation/Write | A kiterjesztett tár adatainak létrehozása vagy frissítése |
> | Műveletek | Microsoft. StorSimple/Manager/extendedInformation/Write | A kiterjesztett adatok beolvasása művelet lekérdezi egy objektum kiterjesztett adatait, amely a (z) típusú Azure-erőforrást jelképezi? |
> | Műveletek | Microsoft. StorSimple/vezetők/funkciók/olvasás | A funkciók listázása |
> | Műveletek | Microsoft. StorSimple/Manager/fileservers/olvasás | A fájlkiszolgálók listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/menedzserek/getEncryptionKey/művelet | Az Eszközkezelő titkosítási kulcsának beolvasása. |
> | Műveletek | Microsoft. StorSimple/Manager/iscsiservers/READ | Az iSCSI-kiszolgálók listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/menedzserek/feladatok/olvasás | A feladatok listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/menedzserek/listActivationKey/művelet | A StorSimple Eszközkezelő aktiválási kulcsának beolvasása. |
> | Műveletek | Microsoft. StorSimple/menedzserek/listPublicEncryptionKey/művelet | Egy StorSimple Eszközkezelő nyilvános titkosítási kulcsainak listázása. |
> | Műveletek | Microsoft. StorSimple/vezetők/mérőszámok/olvasás | A metrikák listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/Manager/metricsDefinitions/READ | A metrikák definícióinak listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/menedzserek/migrateClassicToResourceManager/művelet | Klasszikus áttelepítése a Jászolok Resource Managerbe |
> | Műveletek | Microsoft. StorSimple/Manager/migrationSourceConfigurations/READ | Áttelepítési forrás konfigurációinak listázása (csak 8000 sorozat) |
> | Műveletek | Microsoft. StorSimple/Manager/operationResults/READ | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/menedzserek/provisionCloudAppliance/művelet | Hozzon létre egy új felhőalapú készüléket. |
> | Műveletek | Microsoft. StorSimple/vezetők/olvasás | Az eszközkezelők listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/vezetők/olvasás | A tár beolvasása művelet lekérdezi a Vault típusú Azure-erőforrást jelképező objektumot. |
> | Műveletek | Microsoft. StorSimple/menedzserek/regenerateActivationKey/művelet | Egy meglévő StorSimple Eszközkezelő aktiválási kulcsának újragenerálta. |
> | Műveletek | Microsoft. StorSimple/Managers/storageAccountCredentials/delete | A Storage-fiók hitelesítő adatainak törlése |
> | Műveletek | Microsoft. StorSimple/Managers/storageAccountCredentials/operationResults/READ | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/Manager/storageAccountCredentials/READ | A Storage-fiók hitelesítő adatainak listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/Manager/storageAccountCredentials/Write | A Storage-fiók hitelesítő adatainak létrehozása vagy frissítése |
> | Műveletek | Microsoft. StorSimple/Managers/storageDomains/delete | Törli a tárolási tartományokat |
> | Műveletek | Microsoft. StorSimple/Managers/storageDomains/operationResults/READ | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/Manager/storageDomains/READ | A tárolási tartományok listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/Manager/storageDomains/Write | Tárolási tartományok létrehozása vagy frissítése |
> | Műveletek | Microsoft. StorSimple/vezetők/írás | Eszközkezelők létrehozása vagy frissítése |
> | Műveletek | Microsoft. StorSimple/vezetők/írás | A Create Vault művelet létrehoz egy "Vault" típusú Azure-erőforrást. |
> | Műveletek | Microsoft. StorSimple/Operations/READ | A műveletek listája vagy beolvasása |
> | Műveletek | Microsoft. StorSimple/regisztráció/művelet | Szolgáltató regisztrálása a Microsoft. StorSimple |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. StreamAnalytics/Locations/kvóták/olvasás | Stream Analytics előfizetés kvótájának beolvasása |
> | Műveletek | Microsoft. StreamAnalytics/Operations/READ | Stream Analytics műveletek beolvasása |
> | Műveletek | Microsoft. StreamAnalytics/regisztráció/művelet | Előfizetés regisztrálása Stream Analytics erőforrás-szolgáltatóval |
> | Műveletek | Microsoft. StreamAnalytics/streamingjobs/delete | Stream Analytics-feladatok törlése |
> | Műveletek | Microsoft. StreamAnalytics/streamingjobs/functions/delete | Stream Analytics feladat funkciójának törlése |
> | Műveletek | Microsoft. StreamAnalytics/streamingjobs/functions/operationresults/READ | A Stream Analytics Job függvény olvasási műveletének eredményei |
> | Műveletek | Microsoft. StreamAnalytics/streamingjobs/functions/READ | Stream Analytics feladat funkciójának beolvasása |
> | Műveletek | Microsoft. StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/Action | Stream Analytics feladat funkciójának alapértelmezett definíciójának beolvasása |
> | Műveletek | Microsoft. StreamAnalytics/streamingjobs/functions/test/Action | Stream Analytics feladat funkciójának tesztelése |
> | Műveletek | Microsoft. StreamAnalytics/streamingjobs/functions/Write | Írási Stream Analytics feladat függvény |
> | Műveletek | Microsoft. StreamAnalytics/streamingjobs/bemenetek/törlés | Stream Analytics-feladathoz tartozó bevitel törlése |
> | Műveletek | Microsoft. StreamAnalytics/streamingjobs/bemenetek/operationresults/olvasás | Stream Analytics-feladathoz való bevitel olvasási műveletének eredményei |
> | Műveletek | Microsoft. StreamAnalytics/streamingjobs/bemenetek/olvasás | Stream Analytics-feladatba való bevitel beolvasása |
> | Műveletek | Microsoft. StreamAnalytics/streamingjobs/bemenetek/minta/művelet | Példa Stream Analytics feladatba való bevitelre |
> | Műveletek | Microsoft. StreamAnalytics/streamingjobs/Inputs/test/Action | Stream Analytics-feladathoz való bevitel tesztelése |
> | Műveletek | Microsoft. StreamAnalytics/streamingjobs/bemenet/írás | Írási Stream Analytics feladatok bevitele |
> | Műveletek | Microsoft. StreamAnalytics/streamingjobs/metricdefinitions/READ | Metrikus definíciók olvasása |
> | Műveletek | Microsoft. StreamAnalytics/streamingjobs/operationresults/READ | Stream Analytics feladatokhoz tartozó olvasási művelet eredményei |
> | Műveletek | Microsoft. StreamAnalytics/streamingjobs/kimenet/törlés | Stream Analytics feladatok kimenetének törlése |
> | Műveletek | Microsoft. StreamAnalytics/streamingjobs/kimenet/operationresults/olvasás | Stream Analytics feladatok kimenetének olvasási műveletének eredményei |
> | Műveletek | Microsoft. StreamAnalytics/streamingjobs/kimenet/olvasás | Stream Analytics a feladatok kimenetének olvasása |
> | Műveletek | Microsoft. StreamAnalytics/streamingjobs/kimenet/teszt/művelet | Stream Analytics feladatok kimenetének tesztelése |
> | Műveletek | Microsoft. StreamAnalytics/streamingjobs/kimenet/írás | Stream Analytics a feladatok kimenetének írása |
> | Műveletek | Microsoft. StreamAnalytics/streamingjobs/Providers/Microsoft. bepillantást/diagnosticSettings/READ | Diagnosztikai beállítások beolvasása. |
> | Műveletek | Microsoft. StreamAnalytics/streamingjobs/Providers/Microsoft. bepillantások/diagnosticSettings/írás | Diagnosztikai beállítás írása |
> | Műveletek | Microsoft. StreamAnalytics/streamingjobs/Providers/Microsoft. bepillantást/logDefinitions/READ | Lekéri a streamingjobs elérhető naplóit |
> | Műveletek | Microsoft. StreamAnalytics/streamingjobs/Providers/Microsoft. bepillantást/metricDefinitions/READ | A streamingjobs elérhető metrikáinak beolvasása |
> | Műveletek | Microsoft. StreamAnalytics/streamingjobs/PublishEdgePackage/művelet | Stream Analytics feladatokhoz tartozó Edge-csomag közzététele |
> | Műveletek | Microsoft. StreamAnalytics/streamingjobs/READ | Stream Analytics-feladatok beolvasása |
> | Műveletek | Microsoft. StreamAnalytics/streamingjobs/méretezés/művelet | Stream Analytics feladatok méretezése |
> | Műveletek | Microsoft. StreamAnalytics/streamingjobs/Start/művelet | Stream Analytics-feladat indítása |
> | Műveletek | Microsoft. StreamAnalytics/streamingjobs/leállítás/művelet | Stream Analytics feladatok leállítása |
> | Műveletek | Microsoft. StreamAnalytics/streamingjobs/átalakítások/törlés | Stream Analytics feladatok átalakításának törlése |
> | Műveletek | Microsoft. StreamAnalytics/streamingjobs/átalakítások/olvasás | Stream Analytics feladatok átalakításának olvasása |
> | Műveletek | Microsoft. StreamAnalytics/streamingjobs/átalakítás/írás | Stream Analytics feladatok átalakításának írása |
> | Műveletek | Microsoft. StreamAnalytics/streamingjobs/Write | Stream Analyticsi feladatok írása |

## <a name="microsoftsubscription"></a>Microsoft. előfizetés

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. előfizetés/megszakítás/művelet | Az előfizetés megszakítása |
> | Műveletek | Microsoft. előfizetés/CreateSubscription/művelet | Azure-előfizetés létrehozása |
> | Műveletek | Microsoft. előfizetés/regisztráció/művelet | Előfizetés regisztrálása a Microsoft. előfizetés erőforrás-szolgáltatónál |
> | Műveletek | Microsoft. előfizetés/Átnevezés/művelet | Átnevezi az előfizetést |
> | Műveletek | Microsoft. előfizetés/SubscriptionDefinitions/olvasás | Azure-előfizetések definíciójának beszerzése egy felügyeleti csoporton belül. |
> | Műveletek | Microsoft. előfizetés/SubscriptionDefinitions/írás | Azure-előfizetés definíciójának létrehozása |

## <a name="microsoftsupport"></a>Microsoft. support

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Support/register/action | Regisztrálás a támogatási erőforrás-szolgáltatóra. |
> | Műveletek | Microsoft.Support/supportTickets/read | Támogatási jegy részleteinek lekérése (beleértve az állapotot, súlyosságot, kapcsolattartási adatokat és kommunikációkat) vagy az összes előfizetés támogatási jegyeinek lekérése. |
> | Műveletek | Microsoft.Support/supportTickets/write | Támogatási jegy létrehozása vagy frissítése. Támogatási jegyet technikai, számlázási, kvótákkal kapcsolatos vagy előfizetés-kezelési problémákkal kapcsolatban hozhat létre. A meglévő támogatási jegyeknek frissítheti a súlyosságát, kapcsolattartási adatait és kommunikációit. |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. TimeSeriesInsights/Environments/accesspolicies/delete | Törli a hozzáférési házirendet. |
> | Műveletek | Microsoft. TimeSeriesInsights/Environments/accesspolicies/READ | Hozzáférési szabályzat tulajdonságainak beolvasása. |
> | Műveletek | Microsoft. TimeSeriesInsights/Environments/accesspolicies/Write | Új hozzáférési szabályzatot hoz létre egy környezethez, vagy frissít egy meglévő hozzáférési szabályzatot. |
> | Műveletek | Microsoft. TimeSeriesInsights/környezetek/törlés | Törli a környezetet. |
> | Műveletek | Microsoft. TimeSeriesInsights/Environments/eventsources/delete | Az eseményforrás törlése. |
> | Műveletek | Microsoft. TimeSeriesInsights/Environments/eventsources/READ | Egy eseményforrás tulajdonságainak beolvasása. |
> | Műveletek | Microsoft. TimeSeriesInsights/Environments/eventsources/Write | Új eseményforrás létrehozása egy környezethez, vagy egy meglévő eseményforrás frissítése. |
> | Műveletek | Microsoft. TimeSeriesInsights/környezetek/olvasás | Egy környezet tulajdonságainak beolvasása. |
> | Műveletek | Microsoft. TimeSeriesInsights/Environments/referencedatasets/delete | Törli a hivatkozási adatkészletet. |
> | Műveletek | Microsoft. TimeSeriesInsights/Environments/referencedatasets/READ | Egy hivatkozási adathalmaz tulajdonságainak beolvasása. |
> | Műveletek | Microsoft. TimeSeriesInsights/Environments/referencedatasets/Write | Új hivatkozási adatkészletet hoz létre egy adott környezethez, vagy frissít egy meglévő hivatkozási adatkészletet. |
> | Műveletek | Microsoft. TimeSeriesInsights/környezetek/állapot/olvasás | A környezet állapotának lekérése, a hozzá tartozó műveletek állapota, például bejövő adatok. |
> | Műveletek | Microsoft. TimeSeriesInsights/környezetek/írás | Új környezet létrehozása vagy egy meglévő környezet frissítése. |
> | Műveletek | Microsoft. TimeSeriesInsights/regisztráció/művelet | Regisztrálja az előfizetést a Time Series Insights erőforrás-szolgáltatóhoz, és lehetővé teszi Time Series Insights környezetek létrehozását. |

## <a name="microsoftvisualstudio"></a>Microsoft. VisualStudio

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. VisualStudio/fiók/törlés | Fiók törlése |
> | Műveletek | Microsoft. VisualStudio/fiók/bővítmény/olvasás | Fiók/bővítmény olvasása |
> | Műveletek | Microsoft. VisualStudio/fiók/projekt/olvasás | Fiók/projekt olvasása |
> | Műveletek | Microsoft. VisualStudio/fiók/projekt/írás | Fiók/projekt beállítása |
> | Műveletek | Microsoft. VisualStudio/fiók/olvasás | Fiók beolvasása |
> | Műveletek | Microsoft. VisualStudio/fiók/írás | Fiók beállítása |
> | Műveletek | Microsoft. VisualStudio/bővítmény/törlés | Bővítmény törlése |
> | Műveletek | Microsoft. VisualStudio/bővítmény/olvasás | Olvasási bővítmény |
> | Műveletek | Microsoft. VisualStudio/bővítmény/írás | Bővítmény beállítása |
> | Műveletek | Microsoft. VisualStudio/Project/delete | Projekt törlése |
> | Műveletek | Microsoft. VisualStudio/Project/READ | Projekt beolvasása |
> | Műveletek | Microsoft. VisualStudio/Project/Write | Projekt beállítása |
> | Műveletek | Microsoft. VisualStudio/regisztráció/művelet | Azure-előfizetés regisztrálása a Microsoft. VisualStudio-szolgáltatóval |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. Web/apimanagementaccounts/apiacls/READ | Az API Management-fiókok Apiacls beolvasása. |
> | Műveletek | Microsoft. Web/apimanagementaccounts/API-k/apiacls/delete | Az API Management-fiókok API-Apiacls törlése. |
> | Műveletek | Microsoft. Web/apimanagementaccounts/API-k/apiacls/olvasás | Az API Management-fiókok API-Apiacls beolvasása. |
> | Műveletek | Microsoft. Web/apimanagementaccounts/API-k/apiacls/írás | Az API Management-fiókok API-Apiacls frissítése. |
> | Műveletek | Microsoft. Web/apimanagementaccounts/API-k/connectionacls/olvasás | Az API Management-fiókok API-Connectionacls beolvasása. |
> | Műveletek | Microsoft. Web/apimanagementaccounts/API-k/kapcsolatok/confirmconsentcode/művelet | Erősítse meg a jóváhagyás kód API Management-fiókok API-kapcsolatait. |
> | Műveletek | Microsoft. Web/apimanagementaccounts/API-k/Connections/connectionacls/delete | Az API Management-fiókok API-kapcsolatainak törlése Connectionacls. |
> | Műveletek | Microsoft. Web/apimanagementaccounts/API/Connections/connectionacls/READ | API Management-fiókok API-kapcsolatok Connectionacls beolvasása. |
> | Műveletek | Microsoft. Web/apimanagementaccounts/API-k/kapcsolatok/connectionacls/írás | Az API Management-fiókok API-kapcsolatainak Connectionacls frissítése. |
> | Műveletek | Microsoft. Web/apimanagementaccounts/API-k/kapcsolatok/törlés | API Management-fiókok API-kapcsolatainak törlése. |
> | Műveletek | Microsoft. Web/apimanagementaccounts/API-k/kapcsolatok/getconsentlinks/művelet | Az API Management-fiókok API-kapcsolataihoz tartozó engedélyezési hivatkozások beolvasása. |
> | Műveletek | Microsoft. Web/apimanagementaccounts/API-k/kapcsolatok/listconnectionkeys/művelet | Kapcsolati kulcsok API Management-fiókok API-kapcsolatainak listázása. |
> | Műveletek | Microsoft. Web/apimanagementaccounts/API-k/kapcsolatok/listsecrets/művelet | A Secrets API Management-fiókok API-kapcsolatainak listázása. |
> | Műveletek | Microsoft. Web/apimanagementaccounts/API-k/kapcsolatok/olvasás | API Management-fiókok API-kapcsolatainak beolvasása. |
> | Műveletek | Microsoft. Web/apimanagementaccounts/API-k/kapcsolatok/írás | API Management-fiókok API-kapcsolatainak frissítése. |
> | Műveletek | Microsoft. Web/apimanagementaccounts/API-k/törlés | Az API Management-fiókok API-k törlése. |
> | Műveletek | Microsoft. Web/apimanagementaccounts/API-k/localizeddefinitions/delete | Az API Management-fiókok API-k honosított definícióinak törlése. |
> | Műveletek | Microsoft. Web/apimanagementaccounts/API-k/localizeddefinitions/olvasás | Az API Management-fiókok API-k honosított definícióinak beolvasása. |
> | Műveletek | Microsoft. Web/apimanagementaccounts/API-k/localizeddefinitions/írás | Az API Management-fiókok API-k honosított definícióinak frissítése. |
> | Műveletek | Microsoft. Web/apimanagementaccounts/API-k/olvasás | API Management-fiókok API-k beszerzése. |
> | Műveletek | Microsoft. Web/apimanagementaccounts/API-k/írás | Frissítse az API Management accounts API-kat. |
> | Műveletek | Microsoft. Web/apimanagementaccounts/connectionacls/READ | Az API Management-fiókok Connectionacls beolvasása. |
> | Műveletek | Microsoft. Web/availablestacks/READ | Elérhető stackek beolvasása. |
> | Műveletek | Microsoft. Web/tanúsítványok/törlés | Meglévő tanúsítvány törlése. |
> | Műveletek | Microsoft. Web/tanúsítványok/olvasás | A tanúsítványok listájának beolvasása. |
> | Műveletek | Microsoft. Web/tanúsítványok/írás | Adjon hozzá egy új tanúsítványt, vagy frissítsen egy meglévőt. |
> | Műveletek | Microsoft. Web/checknameavailability/READ | Ellenőrizze, hogy elérhető-e az erőforrás neve. |
> | Műveletek | Microsoft. Web/classicmobileservices/READ | Klasszikus Mobile Services beolvasása. |
> | Műveletek | Microsoft. Web/connectionGateways/delete | Egy összekötő átjáró törlése. |
> | Műveletek | Microsoft. Web/connectionGateways/csatlakozás/művelet | Kapcsolódási átjáróhoz csatlakozik. |
> | Műveletek | Microsoft. Web/connectionGateways/ListStatus/művelet | Egy összekötő átjáró állapotának felsorolása. |
> | Műveletek | Microsoft. Web/connectionGateways/Move/Action | Egy összekötő átjáró mozgatása. |
> | Műveletek | Microsoft. Web/connectionGateways/READ | A kapcsolatok átjáróinak listájának beolvasása. |
> | Műveletek | Microsoft. Web/connectionGateways/írás | Létrehoz vagy frissít egy összekötő átjárót. |
> | Műveletek | Microsoft. Web/Connections/confirmconsentcode/Action | Erősítse meg a kapcsolatok jóváhagyásának kódját. |
> | Műveletek | Microsoft. Web/Connections/delete | Töröl egy-kapcsolatokat. |
> | Műveletek | Microsoft. Web/kapcsolatok/csatlakozás/művelet | Csatlakozik a csatlakozáshoz. |
> | Műveletek | Microsoft. Web/Connections/listconsentlinks/Action | A kapcsolatok csatlakozási kapcsolatainak listázása. |
> | Műveletek | Microsoft. Web/Connections/Move/Action | Egy kapcsolatok áthelyezése. |
> | Műveletek | Microsoft. Web/Connections/READ | A kapcsolatok listájának beolvasása. |
> | Műveletek | Microsoft. Web/Connections/Write | Létrehozza vagy frissíti a kapcsolatokat. |
> | Műveletek | Microsoft. Web/customApis/delete | Töröl egy egyéni API-t. |
> | Műveletek | Microsoft. Web/customApis/extractApiDefinitionFromWsdl/művelet | Az API-definíció kibontása egy WSDL-ből. |
> | Műveletek | Microsoft. Web/customApis/csatlakozás/művelet | Csatlakozik egy egyéni API-hoz. |
> | Műveletek | Microsoft. Web/customApis/listWsdlInterfaces/művelet | Az egyéni API WSDL-felületeit listázza. |
> | Műveletek | Microsoft. Web/customApis/Move/Action | Áthelyez egy egyéni API-t. |
> | Műveletek | Microsoft. Web/customApis/READ | Az egyéni API-k listájának beolvasása. |
> | Műveletek | Microsoft. Web/customApis/írás | Létrehoz vagy frissít egy egyéni API-t. |
> | Műveletek | Microsoft. Web/deletedSites/READ | Törölt webalkalmazás tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Web/deploymentlocations/READ | Üzembe helyezési helyszínek beolvasása. |
> | Műveletek | Microsoft. Web/geoRegions/READ | A földrajzi régiók listájának beolvasása. |
> | Műveletek | Microsoft. Web/hostingenvironments/kapacitás/olvasás | Üzemeltetési környezetek kapacitásainak beolvasása. |
> | Műveletek | Microsoft. Web/hostingEnvironments/delete | App Service Environment törlése |
> | Műveletek | Microsoft. Web/hostingenvironments/érzékelők/olvasás | Üzemeltetési környezetek érzékelők beszerzése. |
> | Műveletek | Microsoft. Web/hostingenvironments/diagnosztika/olvasás | Üzemeltetési környezetek diagnosztika beszerzése. |
> | Műveletek | Microsoft. Web/hostingenvironments/inboundnetworkdependenciesendpoints/READ | Az összes bejövő függőség hálózati végpontjának beolvasása. |
> | Műveletek | Microsoft. Web/hostingEnvironments/csatlakozás/művelet | Egy App Service Environment illesztése |
> | Műveletek | Microsoft. Web/hostingenvironments/metricdefinitions/READ | Üzemeltetési környezetek metrikai definícióinak beolvasása. |
> | Műveletek | Microsoft. Web/hostingenvironments/multirolepools/metricdefinitions/READ | Üzemeltetési környezetek többszerepköres készletek metrikai definícióinak beolvasása. |
> | Műveletek | Microsoft. Web/hostingenvironments/multirolepools/metrika/olvasás | Üzemeltetési környezetek többszerepköres készletek metrikáinak beolvasása. |
> | Műveletek | Microsoft. Web/hostingEnvironments/multiRolePools/READ | Előtér-készlet tulajdonságainak beolvasása egy App Service Environment |
> | Műveletek | Microsoft. Web/hostingenvironments/multirolepools/SKU/READ | Üzemeltetési környezetek többszerepköres készletek SKU-i beszerzése. |
> | Műveletek | Microsoft. Web/hostingenvironments/multirolepools/használati/olvasási | Üzemeltetési környezetek többszerepköres készletek használatának beolvasása. |
> | Műveletek | Microsoft. Web/hostingEnvironments/multiRolePools/írás | Új előtér-készlet létrehozása App Service Environment vagy meglévő frissítése |
> | Műveletek | Microsoft. Web/hostingenvironments/Operations/READ | Üzemeltetési környezetek műveleteinek beolvasása. |
> | Műveletek | Microsoft. Web/hostingenvironments/outboundnetworkdependenciesendpoints/READ | Az összes kimenő függőség hálózati végpontjának beolvasása. |
> | Műveletek | Microsoft. Web/hostingEnvironments/PrivateEndpointConnectionsApproval/művelet | Privát végponti kapcsolatok jóváhagyása |
> | Műveletek | Microsoft. Web/hostingEnvironments/READ | App Service Environment tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Web/hostingEnvironments/újraindítás/művelet | App Service Environment összes számítógépének újraindítása |
> | Műveletek | Microsoft. Web/hostingenvironments/folytatás/művelet | Üzemeltetési környezetek folytatása. |
> | Műveletek | Microsoft. Web/hostingenvironments/kiszolgálófarmok/READ | Üzemeltetési környezetek App Service csomagok beszerzése. |
> | Műveletek | Microsoft. Web/hostingenvironments/Sites/READ | Üzemeltetési környezetek Web Apps. |
> | Műveletek | Microsoft. Web/hostingenvironments/felfüggesztés/művelet | Üzemeltetési környezetek felfüggesztése. |
> | Műveletek | Microsoft. Web/hostingenvironments/használat/olvasás | Üzemeltetési környezetek használatának beolvasása. |
> | Műveletek | Microsoft. Web/hostingenvironments/workerpools/metricdefinitions/READ | Üzemeltetési környezetek Workerpools metrika-definíciók. |
> | Műveletek | Microsoft. Web/hostingenvironments/workerpools/metrika/olvasás | Az üzemeltetési környezetek Workerpools metrikák beszerzése. |
> | Műveletek | Microsoft. Web/hostingEnvironments/workerPools/READ | Munkavégző készlet tulajdonságainak beolvasása egy App Service Environment |
> | Műveletek | Microsoft. Web/hostingenvironments/workerpools/SKU/READ | Üzemeltetési környezetek beszerzése Workerpools SKU-ban. |
> | Műveletek | Microsoft. Web/hostingenvironments/workerpools/használati/olvasási | Üzemeltetési környezetek Workerpools-használatok beszerzése. |
> | Műveletek | Microsoft. Web/hostingEnvironments/workerPools/írás | Új munkavégző készlet létrehozása egy App Service Environment vagy meglévő frissítése |
> | Műveletek | Microsoft. Web/hostingEnvironments/írás | Hozzon létre egy új App Service Environment vagy frissítsen egy meglévőt |
> | Műveletek | Microsoft. Web/ishostingenvironmentnameavailable/READ | Szerezze be, hogy elérhető-e az üzemeltetési környezet neve. |
> | Műveletek | Microsoft. Web/ishostnameavailable/READ | Ellenőrizze, hogy az állomásnév elérhető-e. |
> | Műveletek | Microsoft. Web/isusernameavailable/READ | Ellenőrizze, hogy elérhető-e a Felhasználónév. |
> | Műveletek | Microsoft. Web/listSitesAssignedToHostName/READ | Az állomásnévhez rendelt helyek nevének beolvasása. |
> | Műveletek | Microsoft. Web/Locations/apioperations/READ | Locations API-műveletek beolvasása. |
> | Műveletek | Microsoft. Web/Locations/connectiongatewayinstallations/READ | Telephelyek közötti kapcsolatok átjárójának beolvasása. |
> | Műveletek | Microsoft. Web/Locations/deleteVirtualNetworkOrSubnets/Action | Vnet vagy alhálózati törlési értesítés a helyszínekről. |
> | Műveletek | Microsoft. Web/Locations/extractapidefinitionfromwsdl/Action | API-definíció kinyerése a WSDL-ből a helyekhez. |
> | Műveletek | Microsoft. Web/Locations/listwsdlinterfaces/Action | A helyükhöz tartozó WSDL-felületek listázása. |
> | Műveletek | Microsoft. Web/Locations/król/apioperations/READ | Telephelyek által felügyelt API-műveletek beolvasása. |
> | Műveletek | Microsoft. Web/Locations/król/JOIN/Action | Egy felügyelt API-hoz csatlakozik. |
> | Műveletek | Microsoft. Web/Locations/król/READ | Webhelyek által felügyelt API-k beolvasása. |
> | Műveletek | Microsoft. Web/Locations/operationResults/READ | Műveletek beolvasása. |
> | Műveletek | Microsoft. Web/Locations/Operations/READ | Műveletek beolvasása. |
> | Műveletek | Microsoft. Web/Operations/READ | Műveletek beolvasása. |
> | Műveletek | Microsoft. Web/publishingusers/READ | Felhasználók közzétételének beolvasása. |
> | Műveletek | Microsoft. Web/publishingusers/írás | A közzétételi felhasználók frissítése. |
> | Műveletek | Microsoft. Web/javaslatok/olvasás | Az előfizetésekre vonatkozó javaslatok listájának beolvasása. |
> | Műveletek | Microsoft. Web/regisztráció/művelet | Regisztrálja a Microsoft. web erőforrás-szolgáltatót az előfizetéshez. |
> | Műveletek | Microsoft. Web/resourcehealthmetadata/READ | Resource Health metaadatok beolvasása. |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/képességek/olvasás | App Service csomagok funkcióinak beolvasása. |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/delete | Meglévő App Service csomag törlése |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/eventGridFilters/delete | Event Grid szűrő törlése a kiszolgálófarm esetében. |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/eventGridFilters/READ | Event Grid szűrő beolvasása a kiszolgálófarm számára. |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/eventGridFilters/írás | Helyezze Event Grid szűrőt a kiszolgálófarm számára. |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/firstpartyapps/Settings/delete | App Service-csomagok törlése az első féltől származó alkalmazások beállításaival. |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/firstpartyapps/beállítások/olvasás | App Service-csomagok beszerzése az első féltől származó alkalmazások beállításait. |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/firstpartyapps/beállítások/írás | App Service-csomagok frissítése az első féltől származó alkalmazások beállításait. |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/hybridconnectionnamespaces/Relay/delete | Törölje App Service a hibrid kapcsolatok névtereit továbbító csomagok. |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/hybridconnectionnamespaces/Relay/READ | App Service csomagok hibrid kapcsolatok névterei továbbítók beolvasása. |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/hybridconnectionnamespaces/Relays/Sites/READ | App Service csomagok hibrid kapcsolatok névtereit Web Apps. |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/hybridconnectionplanlimits/READ | App Service csomagok hibrid csatlakoztatási csomagokra vonatkozó korlátainak beolvasása. |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/hybridconnectionrelays/READ | App Service csomagok hibrid kapcsolatok továbbításának beolvasása. |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/metricdefinitions/READ | App Service csomagok metrika-definícióinak beolvasása. |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/mérőszámok/olvasás | App Service csomagok metrikáinak beolvasása. |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/operationresults/READ | App Service csomagok műveleti eredményeinek beolvasása. |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/READ | App Service csomag tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/restartSites/művelet | App Service-csomag összes Web Apps újraindítása |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/Sites/READ | App Service csomagok beszerzése Web Apps. |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/SKU/READ | App Service csomagok beolvasása. |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/használat/olvasás | App Service csomagok használatának beolvasása. |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/virtualnetworkconnections/átjárók/írás | App Service csomagok frissítése Virtual Network kapcsolatok átjárók. |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/virtualnetworkconnections/READ | App Service csomagok beszerzése Virtual Network kapcsolatokhoz. |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/virtualnetworkconnections/Routes/delete | App Service csomagok törlése Virtual Network kapcsolatok útvonala. |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/virtualnetworkconnections/útvonalak/olvasás | App Service csomagok beszerzése Virtual Network kapcsolatok útvonala. |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/virtualnetworkconnections/Routes/Write | App Service csomagok frissítése Virtual Network kapcsolatok útvonala. |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/Workers/újraindítás/művelet | App Service-csomagok újraindítása. |
> | Műveletek | Microsoft. Web/kiszolgálófarmok/írás | Új App Service terv létrehozása vagy egy meglévő frissítése |
> | Műveletek | Microsoft. Web/Sites/analyzecustomhostname/READ | Egyéni állomásnév elemzése. |
> | Műveletek | Microsoft. Web/Sites/applySlotConfig/Action | Webalkalmazás-tárolóhely konfigurációjának alkalmazása a cél tárolóhelyről az aktuális webalkalmazásra |
> | Műveletek | Microsoft. Web/Sites/biztonsági mentés/művelet | Új webalkalmazás biztonsági másolatának létrehozása |
> | Műveletek | Microsoft. Web/Sites/biztonsági mentés/olvasás | Web Apps biztonsági másolat beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Backup/Write | Frissítse Web Apps biztonsági mentést. |
> | Műveletek | Microsoft. Web/Sites/Backups/művelet | Felfedi egy meglévő alkalmazás biztonsági mentését, amely az Azure Storage-beli blobból állítható vissza. |
> | Műveletek | Microsoft. Web/Sites/Backups/delete | Web Apps biztonsági mentések törlése. |
> | Műveletek | Microsoft. Web/Sites/Backups/List/Action | Web Apps biztonsági mentések listázása. |
> | Műveletek | Microsoft. Web/Sites/Backups/READ | Egy webalkalmazás biztonsági mentésének tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Web/Sites/Backups/Restore/Action | Web Apps biztonsági mentések visszaállítása. |
> | Műveletek | Microsoft. Web/Sites/Backups/Write | Web Apps biztonsági mentések frissítése. |
> | Műveletek | Microsoft. Web/Sites/config/delete | Web Apps konfiguráció törlése. |
> | Műveletek | Microsoft. Web/Sites/config/List/Action | A webalkalmazás biztonsági szempontból bizalmas beállításainak, például a hitelesítő adatok közzétételének, az Alkalmazásbeállítások és a kapcsolati karakterláncok listázása |
> | Műveletek | Microsoft. Web/Sites/config/READ | Webalkalmazás konfigurációs beállításainak beolvasása |
> | Műveletek | Microsoft. Web/Sites/config/Pillanatképek/olvasás | Web Apps konfigurációs Pillanatképek beolvasása. |
> | Műveletek | Microsoft. Web/Sites/config/Write | A webalkalmazás konfigurációs beállításainak frissítése |
> | Műveletek | Microsoft. Web/Sites/containerlogs/Action | A webalkalmazás tömörített tárolóinak naplóinak beolvasása. |
> | Műveletek | Microsoft. Web/Sites/containerlogs/Letöltés/művelet | Web Apps tároló naplófájljainak letöltése. |
> | Műveletek | Microsoft. Web/Sites/continuouswebjobs/delete | Web Apps folyamatos webes feladatok törlése. |
> | Műveletek | Microsoft. Web/Sites/continuouswebjobs/READ | Web Apps folyamatos webes feladatok beolvasása. |
> | Műveletek | Microsoft. Web/Sites/continuouswebjobs/Start/művelet | Indítsa el Web Apps folyamatos webes feladatokat. |
> | Műveletek | Microsoft. Web/Sites/continuouswebjobs/leállítás/művelet | A folyamatos webes feladatok Web Apps leállítása. |
> | Műveletek | Microsoft. Web/Sites/delete | Meglévő webalkalmazás törlése |
> | Műveletek | Microsoft. Web/Sites/központi telepítések/törlés | Web Apps üzemelő példányok törlése. |
> | Műveletek | Microsoft. Web/Sites/üzembe helyezés/napló/olvasás | Web Apps központi telepítések naplójának beolvasása. |
> | Műveletek | Microsoft. Web/Sites/üzembe helyezés/olvasás | Web Apps üzemelő példányok beolvasása. |
> | Műveletek | Microsoft. Web/Sites/telepítés/írás | Web Apps központi telepítések frissítése. |
> | Műveletek | Microsoft. Web/Sites/érzékelők/olvasás | Web Apps érzékelők beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/elemzések/végrehajtás/művelet | Web Apps diagnosztikai elemzés futtatása. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/elemzés/olvasás | Web Apps diagnosztika elemzésének beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/aspnetcore/READ | Web Apps diagnosztika beszerzése ASP.NET Core alkalmazáshoz. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/autoheal/READ | Szerezze be Web Apps diagnosztika autohealt. |
> | Műveletek | Microsoft. Web/Sites/diagnosztika/üzembe helyezés/olvasás | Web Apps diagnosztika központi telepítésének beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/központi telepítések/olvasás | Web Apps diagnosztika központi telepítésének beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/érzékelők/végrehajtás/művelet | Web Apps diagnosztikai detektor futtatása. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/érzékelők/olvasás | Web Apps diagnosztikai detektor beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/failedrequestsperuri/READ | Web Apps diagnosztika sikertelen kérelmének beolvasása URI-n. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/frebanalysis/READ | Web Apps diagnosztikai sikertelen kérelmek ESEMÉNYTÁROLÁSI elemzésének beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/loganalyzer/READ | Web Apps diagnosztikai napló Analizátorjának beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/READ | Web Apps diagnosztikai kategóriák beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/runtimeavailability/READ | Web Apps diagnosztikai futtatókörnyezet rendelkezésre állásának beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/servicehealth/READ | Web Apps diagnosztikai Service Health beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/sitecpuanalysis/READ | Web Apps diagnosztikai hely CPU-elemzésének beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/sitecrashes/READ | Web Apps diagnosztikai hely összeomlásának beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/sitelatency/READ | Web Apps diagnosztikai hely késésének beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/sitememoryanalysis/READ | Web Apps diagnosztikai hely memória-elemzésének beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/siterestartsettingupdate/READ | Web Apps diagnosztikai hely újraindítási beállításának beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/siterestartuserinitiated/READ | Web Apps diagnosztikai hely újraindítását kezdeményező felhasználó beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/siteswap/READ | Web Apps diagnosztikai hely felcserélése. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/ThreadCount/READ | Web Apps diagnosztikai szálak számának beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/workeravailability/READ | Web Apps diagnosztikai Workeravailability beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Diagnostics/workerprocessrecycle/READ | Web Apps diagnosztika munkavégző folyamatának újrahasznosítása. |
> | Műveletek | Microsoft. Web/Sites/domainownershipidentifiers/READ | Web Apps tartomány tulajdonjog-azonosítóinak beolvasása. |
> | Műveletek | Microsoft. Web/Sites/domainownershipidentifiers/Write | Web Apps tartomány tulajdonjog-azonosítóinak frissítése. |
> | Műveletek | Microsoft. Web/Sites/eventGridFilters/delete | Event Grid szűrő törlése a webalkalmazásban. |
> | Műveletek | Microsoft. Web/Sites/eventGridFilters/READ | Event Grid szűrő beolvasása a webalkalmazásban. |
> | Műveletek | Microsoft. Web/Sites/eventGridFilters/Write | Event Grid szűrő elhelyezése a webalkalmazásban. |
> | Műveletek | Microsoft. Web/Sites/Extensions/delete | Web Apps hely bővítményeinek törlése. |
> | Műveletek | Microsoft. Web/Sites/Extensions/READ | Web Apps hely bővítményeinek beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Extensions/Write | Web Apps hely bővítményeinek frissítése. |
> | Műveletek | Microsoft. Web/Sites/functions/művelet | Functions Web Apps. |
> | Műveletek | Microsoft. Web/Sites/functions/delete | Web Apps függvények törlése. |
> | Műveletek | Microsoft. Web/Sites/functions/kulcsok/törlés | Funkcióbillentyűk törlése. |
> | Műveletek | Microsoft. Web/webhelyek/függvények/kulcsok/írás | Funkcióbillentyűk frissítése. |
> | Műveletek | Microsoft. Web/Sites/functions/listkeys műveletének beolvasása/Action | A függvények kulcsainak listázása. |
> | Műveletek | Microsoft. Web/Sites/functions/listsecrets/Action | A függvények titkainak listázása. |
> | Műveletek | Microsoft. Web/Sites/functions/MasterKey/READ | Web Apps függvények MasterKey beolvasása. |
> | Műveletek | Microsoft. Web/Sites/functions/Properties/READ | Web Apps függvények tulajdonságainak olvasása. |
> | Műveletek | Microsoft. Web/Sites/functions/Properties/Write | Web Apps függvények tulajdonságainak frissítése. |
> | Műveletek | Microsoft. Web/Sites/functions/READ | Web Apps függvények beolvasása. |
> | Műveletek | Microsoft. Web/Sites/functions/token/READ | Web Apps functions token beolvasása. |
> | Műveletek | Microsoft. Web/Sites/functions/Write | Web Apps függvények frissítése. |
> | Műveletek | Microsoft. Web/Sites/Host/functionkeys/delete | A functions Host funkcióbillentyűk törlése. |
> | Műveletek | Microsoft. Web/Sites/Host/functionkeys/Write | Functions Host-funkcióbillentyűk frissítése. |
> | Műveletek | Microsoft. Web/Sites/Host/listkeys műveletének beolvasása/művelet | Listázza a functions gazdagép kulcsait. |
> | Műveletek | Microsoft. Web/Sites/Host/listsyncstatus/művelet | A szinkronizálási függvény eseményindítóinak listázása. |
> | Műveletek | Microsoft. Web/Sites/Host/Properties/READ | Olvassa el Web Apps függvények gazdagépének tulajdonságait. |
> | Műveletek | Microsoft. Web/Sites/Host/Sync/Action | A szinkronizálási függvények eseményindítói. |
> | Műveletek | Microsoft. Web/Sites/Host/systemkeys/delete | A functions gazdagép rendszerkulcsainak törlése. |
> | Műveletek | Microsoft. Web/Sites/Host/systemkeys/Write | A functions gazdagép rendszerkulcsainak frissítése. |
> | Műveletek | Microsoft. Web/Sites/hostnamebindings/delete | Web Apps állomásnév kötésének törlése. |
> | Műveletek | Microsoft. Web/Sites/hostnamebindings/READ | Web Apps állomásnév kötésének beolvasása. |
> | Műveletek | Microsoft. Web/Sites/hostnamebindings/Write | Web Apps állomásnév-kötések frissítése. |
> | Műveletek | Microsoft. Web/Sites/hostruntime/functions/kulcsok/olvasás | Web Apps Hostruntime-függvények kulcsainak beolvasása. |
> | Műveletek | Microsoft. Web/Sites/hostruntime/Host/_master/READ | függvényalkalmazás fő kulcsának beolvasása rendszergazdai műveletekhez |
> | Műveletek | Microsoft. Web/Sites/hostruntime/Host/Action | Hajtsa végre függvényalkalmazás futtatókörnyezet műveleteit, például a Szinkronizáló eseményindítókat, a függvények hozzáadását, a függvények meghívását, a függvények törlését |
> | Műveletek | Microsoft. Web/Sites/hostruntime/Host/READ | Web Apps Hostruntime-gazdagép beszerzése. |
> | Műveletek | Microsoft. Web/Sites/hybridconnection/delete | Web Apps hibrid kapcsolatok törlése. |
> | Műveletek | Microsoft. Web/Sites/hybridconnection/READ | Web Apps hibrid kapcsolatok beszerzése. |
> | Műveletek | Microsoft. Web/Sites/hybridconnection/Write | Web Apps hibrid kapcsolatok frissítése. |
> | Műveletek | Microsoft. Web/Sites/hybridconnectionnamespaces/Relays/delete | Törölje Web Apps hibrid kapcsolatok névtereit. |
> | Műveletek | Microsoft. Web/Sites/hybridconnectionnamespaces/Relays/listkeys műveletének beolvasása/Action | Kulcsok listázása Web Apps hibrid kapcsolatok névterei továbbítók. |
> | Műveletek | Microsoft. Web/Sites/hybridconnectionnamespaces/Relays/READ | Web Apps hibrid kapcsolatok névterei továbbításának beolvasása. |
> | Műveletek | Microsoft. Web/Sites/hybridconnectionnamespaces/Relay/Write | Frissítse Web Apps hibrid kapcsolatok névtereit. |
> | Műveletek | Microsoft. Web/Sites/hybridconnectionrelays/READ | Web Apps hibrid kapcsolatok továbbításának beolvasása. |
> | Műveletek | Microsoft. Web/helyek/példányok/központi telepítések/törlés | Web Apps példányok telepítésének törlése. |
> | Műveletek | Microsoft. Web/Sites/példányok/központi telepítés/olvasás | Web Apps példányok üzembe helyezésének beolvasása. |
> | Műveletek | Microsoft. Web/Sites/instances/Extensions/log/READ | Web Apps példányok bővítmények naplójának beolvasása. |
> | Műveletek | Microsoft. Web/Sites/instances/Extensions/processs/READ | Web Apps példányok bővítmények folyamatainak beolvasása. |
> | Műveletek | Microsoft. Web/Sites/instances/Extensions/READ | Web Apps példányok bővítményeinek beolvasása. |
> | Műveletek | Microsoft. Web/Sites/instances/Processes/delete | Web Apps példányok folyamatainak törlése. |
> | Műveletek | Microsoft. Web/Sites/instances/folyamatok/modulok/olvasás | Web Apps példányok beolvasása modulokat dolgoz fel. |
> | Műveletek | Microsoft. Web/Sites/instances/Processes/READ | Web Apps példányok folyamatainak beolvasása. |
> | Műveletek | Microsoft. Web/Sites/instances/processs/Threads/READ | Web Apps példányok beolvasása szálakat dolgoz fel. |
> | Műveletek | Microsoft. Web/Sites/instances/READ | Web Apps példányok beolvasása. |
> | Műveletek | Microsoft. Web/Sites/listsyncfunctiontriggerstatus/Action | A szinkronizálási függvény trigger állapotának listázása. |
> | Műveletek | Microsoft. Web/Sites/metricdefinitions/READ | Web Apps metrikák definícióinak beolvasása. |
> | Műveletek | Microsoft. Web/Sites/metrika/olvasás | Web Apps mérőszámok beolvasása. |
> | Műveletek | Microsoft. Web/Sites/metricsdefinitions/READ | Web Apps mérőszámok definícióinak beolvasása. |
> | Műveletek | Microsoft. Web/Sites/migratemysql/Action | Telepítse át a MySql Web Apps. |
> | Műveletek | Microsoft. Web/Sites/migratemysql/READ | A MySql Web Apps migrálása. |
> | Műveletek | Microsoft. Web/Sites/networktrace/Action | Hálózati nyomkövetési Web Apps. |
> | Műveletek | Microsoft. Web/Sites/networktraces/operationresults/READ | Web Apps hálózati nyomkövetési művelet eredményeinek beolvasása. |
> | Műveletek | Microsoft. Web/Sites/ÚjJelszó/Action | ÚjJelszó Web Apps. |
> | Műveletek | Microsoft. Web/Sites/operationresults/READ | Web Apps művelet eredményeinek beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Operations/READ | Web Apps műveletek beolvasása. |
> | Műveletek | Microsoft. Web/Sites/perfcounters/READ | Web Apps teljesítményszámlálók beolvasása. |
> | Műveletek | Microsoft. Web/Sites/premieraddons/delete | Web Apps Premier bővítmények törlése. |
> | Műveletek | Microsoft. Web/Sites/premieraddons/READ | Web Apps Premier addons beszerzése. |
> | Műveletek | Microsoft. Web/Sites/premieraddons/Write | Web Apps Premier addons frissítése. |
> | Műveletek | Microsoft. Web/Sites/privateaccess/READ | A webhelyhez hozzáférő, a webhely elérését engedélyező és engedélyezett virtuális hálózatok körének beolvasása. |
> | Műveletek | Microsoft. Web/Sites/PrivateEndpointConnectionsApproval/Action | Privát végponti kapcsolatok jóváhagyása |
> | Műveletek | Microsoft. Web/Sites/folyamatok/modulok/olvasás | Web Apps folyamatok moduljainak beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Process/READ | Web Apps folyamatok beolvasása. |
> | Műveletek | Microsoft. Web/Sites/processs/Threads/READ | Web Apps folyamatok szálának beolvasása. |
> | Műveletek | Microsoft. Web/Sites/publiccertificates/delete | Web Apps nyilvános tanúsítványok törlése. |
> | Műveletek | Microsoft. Web/Sites/publiccertificates/READ | Web Apps nyilvános tanúsítványok beszerzése. |
> | Műveletek | Microsoft. Web/Sites/publiccertificates/Write | Web Apps nyilvános tanúsítványok frissítése. |
> | Műveletek | Microsoft. Web/Sites/közzététel/művelet | Webalkalmazás közzététele |
> | Műveletek | Microsoft. Web/Sites/publishxml/Action | Webes alkalmazás közzétételi profiljának XML-kódjának beolvasása |
> | Műveletek | Microsoft. Web/Sites/publishxml/READ | Web Apps közzétételi XML-fájl beolvasása. |
> | Műveletek | Microsoft. Web/Sites/READ | Webalkalmazás tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Web/Sites/recommendationhistory/READ | Web Apps javaslat előzményeinek beolvasása. |
> | Műveletek | Microsoft. Web/Sites/javaslatok/letiltás/művelet | Web Apps javaslatok letiltása. |
> | Műveletek | Microsoft. Web/Sites/javaslatok/olvasás | A webalkalmazáshoz tartozó javaslatok listájának beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Recover/Action | Web Apps helyreállítása. |
> | Műveletek | Microsoft. Web/Sites/resetSlotConfig/Action | Webalkalmazás-konfiguráció alaphelyzetbe állítása |
> | Műveletek | Microsoft. Web/Sites/resourcehealthmetadata/READ | Web Apps Resource Health metaadatok beolvasása. |
> | Műveletek | Microsoft. Web/Sites/újraindítás/művelet | Webalkalmazás újraindítása |
> | Műveletek | Microsoft. Web/Sites/Restore/READ | Web Apps visszaállítás beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Restore/Write | Web Apps visszaállítása. |
> | Műveletek | Microsoft. Web/Sites/restorefrombackupblob/Action | A webalkalmazás visszaállítása a biztonsági mentési Blobból. |
> | Műveletek | Microsoft. Web/Sites/restorefromdeletedapp/Action | Web Apps visszaállítása a törölt alkalmazásból. |
> | Műveletek | Microsoft. Web/Sites/restoresnapshot/Action | Web Apps Pillanatképek visszaállítása. |
> | Műveletek | Microsoft. Web/Sites/siteextensions/delete | Web Apps hely bővítményeinek törlése. |
> | Műveletek | Microsoft. Web/Sites/siteextensions/READ | Web Apps hely bővítményeinek beolvasása. |
> | Műveletek | Microsoft. Web/Sites/siteextensions/Write | Web Apps hely bővítményeinek frissítése. |
> | Műveletek | Microsoft. Web/Sites/Slots/analyzecustomhostname/READ | Web Apps tárolóhelyek elemzése az egyéni állomásnév elemzéséhez. |
> | Műveletek | Microsoft. Web/Sites/Slots/applySlotConfig/Action | Webalkalmazás-tárolóhely konfigurációjának alkalmazása a cél tárolóhelyről az aktuális bővítőhelyre. |
> | Műveletek | Microsoft. Web/Sites/slot/Backup/művelet | Hozzon létre új webalkalmazás-tárolóhely biztonsági mentést. |
> | Műveletek | Microsoft. Web/Sites/bővítőhely/biztonsági mentés/olvasás | Web Apps tárolóhelyek biztonsági másolatának beolvasása. |
> | Műveletek | Microsoft. Web/Sites/bővítőhely/biztonsági mentés/írás | Web Apps tárolóhelyek biztonsági mentésének frissítése. |
> | Műveletek | Microsoft. Web/Sites/Slots/Backups/művelet | Fedezze fel Web Apps bővítőhelyek biztonsági másolatait. |
> | Műveletek | Microsoft. Web/Sites/Slots/Backups/delete | Web Apps tárolóhelyek biztonsági mentésének törlése. |
> | Műveletek | Microsoft. Web/Sites/Slots/Backups/List/Action | Web Apps tárolóhelyek biztonsági másolatainak listázása. |
> | Műveletek | Microsoft. Web/Sites/Slots/Backups/READ | Webalkalmazási tárolóhelyek biztonsági mentésének tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Web/Sites/Slots/Backups/Restore/Action | Web Apps tárolóhelyek biztonsági másolatának visszaállítása. |
> | Műveletek | Microsoft. Web/Sites/Slots/config/delete | Web Apps tárolóhelyek konfigurációjának törlése. |
> | Műveletek | Microsoft. Web/Sites/Slots/config/List/Action | A webalkalmazási tárolóhely biztonsági kényes beállításainak listázása, például a hitelesítő adatok közzététele, az Alkalmazásbeállítások és a kapcsolati karakterláncok |
> | Műveletek | Microsoft. Web/Sites/Slots/config/READ | Webalkalmazási tárolóhely konfigurációs beállításainak beolvasása |
> | Műveletek | Microsoft. Web/Sites/Slots/config/Write | A webalkalmazási tárolóhely konfigurációs beállításainak frissítése |
> | Műveletek | Microsoft. Web/Sites/Slots/containerlogs/Action | A webalkalmazások tárolóhelyének tömörített tároló-naplófájljainak beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/containerlogs/Letöltés/művelet | Töltse le Web Apps tárolóhelyek tárolójának naplóit. |
> | Műveletek | Microsoft. Web/Sites/Slots/continuouswebjobs/delete | Web Apps tárolóhelyek folyamatos webes feladatának törlése. |
> | Műveletek | Microsoft. Web/Sites/Slots/continuouswebjobs/READ | Web Apps tárolóhelyek folyamatos webes feladatainak beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/continuouswebjobs/Start/művelet | Indítsa el Web Apps bővítőhelyek folyamatos webes feladatait. |
> | Műveletek | Microsoft. Web/Sites/Slots/continuouswebjobs/leállítás/művelet | Állítsa le Web Apps résidők folyamatos webes feladatait. |
> | Műveletek | Microsoft. Web/Sites/Slots/delete | Meglévő webalkalmazási tárolóhely törlése |
> | Műveletek | Microsoft. Web/Sites/bővítőhelyek/központi telepítések/törlés | Web Apps tárolóhelyek telepítésének törlése. |
> | Műveletek | Microsoft. Web/Sites/bővítőhely/üzembe helyezés/napló/olvasás | Web Apps tárolóhelyek üzembe helyezési naplójának beolvasása. |
> | Műveletek | Microsoft. Web/Sites/bővítőhely/üzembe helyezés/olvasás | Web Apps tárolóhelyek központi telepítésének beolvasása. |
> | Műveletek | Microsoft. Web/Sites/bővítőhely/üzembe helyezés/írás | Web Apps bővítőhelyek telepítésének frissítése. |
> | Műveletek | Microsoft. Web/Sites/Slots/érzékelők/olvasás | Web Apps tárolóhelyek észlelésének beolvasása. |
> | Műveletek | Microsoft. Web/Sites/bővítőhely/diagnosztika/elemzés/végrehajtás/művelet | Futtassa Web Apps bővítőhely diagnosztikai elemzését. |
> | Műveletek | Microsoft. Web/Sites/bővítőhely/diagnosztika/elemzés/olvasás | Web Apps tárolóhelyek diagnosztikai elemzésének beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/Diagnostics/aspnetcore/READ | ASP.NET Core alkalmazáshoz Web Apps bővítőhely diagnosztika beszerzése. |
> | Műveletek | Microsoft. Web/Sites/bővítőhely/diagnosztika/autoheal/olvasás | Szerezze be a Web Apps bővítőhely diagnosztika autoheal szolgáltatását. |
> | Műveletek | Microsoft. Web/Sites/bővítőhely/diagnosztika/üzembe helyezés/olvasás | Szerezze be Web Apps tárolóhelyek diagnosztika üzembe helyezését. |
> | Műveletek | Microsoft. Web/Sites/bővítőhely/diagnosztika/központi telepítés/olvasás | Web Apps tárolóhelyek diagnosztikai telepítésének beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/Diagnostics/érzékelők/végrehajtás/művelet | Web Apps bővítőhely diagnosztikai detektor futtatása. |
> | Műveletek | Microsoft. Web/Sites/Slots/Diagnostics/érzékelők/olvasás | Web Apps tárolóhelyek diagnosztikai Detektorának beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/Diagnostics/frebanalysis/READ | Web Apps bővítőhelyek diagnosztikai sikertelen kérelmek ESEMÉNYTÁROLÁSI elemzésének beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/Diagnostics/loganalyzer/READ | Web Apps tárolóhelyek diagnosztikai naplójának beolvasása. |
> | Műveletek | Microsoft. Web/Sites/bővítőhely/diagnosztika/olvasás | Web Apps tárolóhelyek diagnosztika beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/Diagnostics/runtimeavailability/READ | Szerezze be Web Apps tárolóhelyek diagnosztika-futtatókörnyezetének rendelkezésre állását. |
> | Műveletek | Microsoft. Web/Sites/Slots/Diagnostics/servicehealth/READ | Szerezze be Web Apps bővítőhelyek diagnosztikai Service Healthét. |
> | Műveletek | Microsoft. Web/Sites/Slots/Diagnostics/sitecpuanalysis/READ | Szerezze be Web Apps tárolóhelyek diagnosztikai helyének CPU-elemzését. |
> | Műveletek | Microsoft. Web/Sites/Slots/Diagnostics/sitecrashes/READ | A Web Apps tárolóhelyek diagnosztikai helyének összeomlása. |
> | Műveletek | Microsoft. Web/Sites/Slots/Diagnostics/sitelatency/READ | Szerezze be Web Apps tárolóhelyek diagnosztikai helyének késését. |
> | Műveletek | Microsoft. Web/Sites/Slots/Diagnostics/sitememoryanalysis/READ | Szerezze be Web Apps tárolóhelyek diagnosztikai helyének memóriájának elemzését. |
> | Műveletek | Microsoft. Web/Sites/Slots/Diagnostics/siterestartsettingupdate/READ | Web Apps tárolóhelyek diagnosztikai helyének újraindítása a frissítés beállításának beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/Diagnostics/siterestartuserinitiated/READ | Web Apps tárolóhelyek diagnosztikai helyének újraindítási felhasználójának beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/Diagnostics/siteswap/READ | Web Apps tárolóhelyek diagnosztikai helyének felcserélése. |
> | Műveletek | Microsoft. Web/Sites/Slots/Diagnostics/ThreadCount/READ | Web Apps bővítőhelyek diagnosztikai szálak számának beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/Diagnostics/workeravailability/READ | Web Apps bővítőhely diagnosztikai Workeravailability beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/Diagnostics/workerprocessrecycle/READ | Szerezze be Web Apps bővítőhely diagnosztika munkavégző folyamatának újrahasznosítását. |
> | Műveletek | Microsoft. Web/Sites/Slots/domainownershipidentifiers/READ | Web Apps bővítőhelyek tartományi tulajdonosi azonosítóinak beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/functions/listsecrets/Action | A Secrets Web Apps a tárolóhelyek funkcióinak listázása. |
> | Műveletek | Microsoft. Web/Sites/Slots/functions/READ | Web Apps tárolóhelyek funkcióinak beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/hostnamebindings/delete | Web Apps tárolóhelyek állomásnév-kötéseinek törlése. |
> | Műveletek | Microsoft. Web/Sites/Slots/hostnamebindings/READ | Web Apps tárolóhelyek állomásnév kötéseinek beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/hostnamebindings/Write | Web Apps tárolóhelyek állomásnév-kötéseinek frissítése. |
> | Műveletek | Microsoft. Web/Sites/Slots/hybridconnection/delete | Web Apps bővítőhelyek hibrid kapcsolatainak törlése. |
> | Műveletek | Microsoft. Web/Sites/Slots/hybridconnection/READ | Web Apps bővítőhelyek hibrid kapcsolatainak beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/hybridconnection/Write | Web Apps bővítőhelyek hibrid kapcsolatainak frissítése. |
> | Műveletek | Microsoft. Web/Sites/Slots/hybridconnectionnamespaces/Relays/delete | Törölje Web Apps bővítőhely hibrid kapcsolatok névtereit. |
> | Műveletek | Microsoft. Web/Sites/Slots/hybridconnectionnamespaces/Relay/Write | Frissítse Web Apps bővítőhely hibrid kapcsolatok névtereit. |
> | Műveletek | Microsoft. Web/Sites/Slots/hybridconnectionrelays/READ | Web Apps bővítőhelyek hibrid kapcsolatainak beolvasása. |
> | Műveletek | Microsoft. Web/Sites/bővítőhely/példányok/központi telepítések/olvasás | Web Apps bővítőhely példányok üzembe helyezésének beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/instances/processs/delete | Web Apps tárolóhely-példányok folyamatainak törlése. |
> | Műveletek | Microsoft. Web/Sites/Slots/instances/processs/READ | Web Apps résidő-példányok folyamatainak beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/instances/READ | Web Apps tárolóhelyek példányainak beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/metricdefinitions/READ | Web Apps bővítőhelyek metrikai definícióinak beolvasása. |
> | Műveletek | Microsoft. Web/Sites/slot/metrika/olvasás | Web Apps bővítőhely metrikáinak beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/migratemysql/READ | Web Apps bővítőhelyek áttelepíthetők a MySql-re. |
> | Műveletek | Microsoft. Web/Sites/Slots/networktrace/Action | Hálózati nyomkövetési Web Apps tárolóhelyek. |
> | Műveletek | Microsoft. Web/Sites/Slots/networktraces/operationresults/READ | Web Apps tárolóhelyek hálózati nyomkövetési műveletének eredményeinek beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/ÚjJelszó/Action | ÚjJelszó Web Apps tárolóhelyek. |
> | Műveletek | Microsoft. Web/Sites/Slots/operationresults/READ | Web Apps bővítőhelyek műveleti eredményeinek beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/Operations/READ | Web Apps tárolóhelyek műveleteinek beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/perfcounters/READ | Web Apps bővítőhelyek teljesítményszámlálók beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/phplogging/READ | Web Apps bővítőhelyek Phplogging beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/premieraddons/delete | Web Apps bővítőhely Premier addons törlése. |
> | Műveletek | Microsoft. Web/Sites/Slots/premieraddons/READ | Web Apps Slots Premier addons beszerzése. |
> | Műveletek | Microsoft. Web/Sites/Slots/premieraddons/Write | Web Apps Slots Premier addons frissítése. |
> | Műveletek | Microsoft. Web/Sites/Slots/processs/READ | Web Apps tárolóhelyek folyamatainak beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/publiccertificates/delete | Web Apps bővítőhely nyilvános tanúsítványainak törlése. |
> | Műveletek | Microsoft. Web/Sites/Slots/publiccertificates/READ | Web Apps bővítőhely nyilvános tanúsítványainak beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/publiccertificates/Write | Web Apps bővítőhely nyilvános tanúsítványainak létrehozása vagy frissítése. |
> | Műveletek | Microsoft. Web/Sites/Slots/közzététel/művelet | Webalkalmazási tárolóhely közzététele |
> | Műveletek | Microsoft. Web/Sites/Slots/publishxml/Action | Közzétételi profil XML-kódjának beolvasása a webalkalmazási tárolóhelyhez |
> | Műveletek | Microsoft. Web/Sites/Slots/READ | Webalkalmazás-telepítési tárolóhely tulajdonságainak beolvasása |
> | Műveletek | Microsoft. Web/Sites/Slots/Recover/Action | Web Apps tárolóhelyek helyreállítása. |
> | Műveletek | Microsoft. Web/Sites/Slots/resetSlotConfig/Action | Webalkalmazás-tárolóhely konfigurációjának alaphelyzetbe állítása |
> | Műveletek | Microsoft. Web/Sites/Slots/resourcehealthmetadata/READ | Web Apps tárolóhelyek Resource Health metaadatok beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/újraindítás/művelet | Webalkalmazás-tárolóhely újraindítása |
> | Műveletek | Microsoft. Web/Sites/slot/Restore/READ | Web Apps tárolóhelyek visszaállításának beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/Restore/Write | Web Apps tárolóhelyek visszaállítása. |
> | Műveletek | Microsoft. Web/Sites/Slots/restorefrombackupblob/Action | Web Apps tárolóhely visszaállítása a biztonsági mentési Blobból. |
> | Műveletek | Microsoft. Web/Sites/Slots/restorefromdeletedapp/Action | Webalkalmazás-tárolóhelyek visszaállítása a törölt alkalmazásból. |
> | Műveletek | Microsoft. Web/Sites/Slots/restoresnapshot/Action | Web Apps tárolóhelyek pillanatképének visszaállítása. |
> | Műveletek | Microsoft. Web/Sites/Slots/siteextensions/delete | Web Apps bővítőhelyek helyének bővítményeinek törlése. |
> | Műveletek | Microsoft. Web/Sites/Slots/siteextensions/READ | Web Apps bővítőhelyek helyének bővítményeinek beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/siteextensions/Write | Frissítse Web Apps bővítőhelyek helyének bővítményeit. |
> | Műveletek | Microsoft. Web/Sites/Slots/slotsdiffs/Action | A webalkalmazás és a bővítőhelyek közötti beállítások közötti különbségek beolvasása |
> | Műveletek | Microsoft. Web/Sites/Slots/slotsswap/Action | Webalkalmazás-telepítési bővítőhelyek cseréje |
> | Műveletek | Microsoft. Web/Sites/Slots/Pillanatképek/olvasás | Web Apps tárolóhelyek pillanatképének beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/sourcecontrols/delete | Webalkalmazás-tárolóhely forrás-ellenőrzési konfigurációs beállításainak törlése |
> | Műveletek | Microsoft. Web/Sites/Slots/sourcecontrols/READ | A webalkalmazási tárolóhely forrás-ellenőrzési konfigurációs beállításainak beolvasása |
> | Műveletek | Microsoft. Web/Sites/Slots/sourcecontrols/Write | A webalkalmazási tárolóhely forrás-ellenőrzési konfigurációs beállításainak frissítése |
> | Műveletek | Microsoft. Web/Sites/Slots/Start/művelet | Webalkalmazás-tárolóhely elindítása |
> | Műveletek | Microsoft. Web/Sites/Slots/leállítás/művelet | Webalkalmazási tárolóhely leállítása |
> | Műveletek | Microsoft. Web/Sites/slot/Sync/Action | Web Apps tárolóhelyek szinkronizálása. |
> | Műveletek | Microsoft. Web/Sites/Slots/triggeredwebjobs/delete | A webjobs által aktivált Web Apps tárolóhelyek törlése. |
> | Műveletek | Microsoft. Web/Sites/Slots/triggeredwebjobs/READ | Beolvashatja Web Apps bővítőhelyek által aktivált webjobs-feladatokat. |
> | Műveletek | Microsoft. Web/Sites/Slots/triggeredwebjobs/Run/művelet | Futtassa Web Apps bővítőhelyen indított webjobs-feladatokat. |
> | Műveletek | Microsoft. Web/Sites/Slots/használati adatok/olvasás | Web Apps tárolóhelyek használatának beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Slots/virtualnetworkconnections/delete | Web Apps bővítőhelyek Virtual Network kapcsolatok törlése. |
> | Műveletek | Microsoft. Web/Sites/slot/virtualnetworkconnections/Gateway/Write | Frissítse Web Apps tárolóhelyek Virtual Network kapcsolatok átjáróit. |
> | Műveletek | Microsoft. Web/Sites/Slots/virtualnetworkconnections/READ | Web Apps bővítőhelyek Virtual Network kapcsolatok beszerzése. |
> | Műveletek | Microsoft. Web/Sites/Slots/virtualnetworkconnections/Write | Web Apps bővítőhelyek Virtual Network kapcsolatok frissítése. |
> | Műveletek | Microsoft. Web/Sites/Slots/webjobs/olvasás | Web Apps slot webjobs beszerzése. |
> | Műveletek | Microsoft. Web/Sites/Slots/Write | Hozzon létre egy új webalkalmazási tárolóhelyet vagy frissítsen egy meglévőt |
> | Műveletek | Microsoft. Web/Sites/slotsdiffs/Action | A webalkalmazás és a bővítőhelyek közötti beállítások közötti különbségek beolvasása |
> | Műveletek | Microsoft. Web/Sites/slotsswap/Action | Webalkalmazás-telepítési bővítőhelyek cseréje |
> | Műveletek | Microsoft. Web/Sites/Pillanatképek/olvasás | Web Apps Pillanatképek beolvasása. |
> | Műveletek | Microsoft. Web/Sites/sourcecontrols/delete | Webalkalmazás forrás-ellenőrzési konfigurációs beállításainak törlése |
> | Műveletek | Microsoft. Web/Sites/sourcecontrols/READ | A webalkalmazás forrás-ellenőrzési konfigurációs beállításainak beolvasása |
> | Műveletek | Microsoft. Web/Sites/sourcecontrols/Write | A webalkalmazás forrás-ellenőrzési konfigurációs beállításainak frissítése |
> | Műveletek | Microsoft. Web/Sites/indítás/művelet | Webalkalmazás indítása |
> | Műveletek | Microsoft. Web/Sites/leállítás/művelet | Webalkalmazás leállítása |
> | Műveletek | Microsoft. Web/Sites/Sync/Action | Web Apps szinkronizálása. |
> | Műveletek | Microsoft. Web/Sites/syncfunctiontriggers/Action | A szinkronizálási függvények eseményindítói. |
> | Műveletek | Microsoft. Web/Sites/triggeredwebjobs/delete | Web Apps indított webjobs-feladatok törlése. |
> | Műveletek | Microsoft. Web/Sites/triggeredwebjobs/előzmények/olvasás | Web Apps indított webjobs-előzmények beolvasása. |
> | Műveletek | Microsoft. Web/Sites/triggeredwebjobs/READ | Web Apps indított webjobs-feladatok beolvasása. |
> | Műveletek | Microsoft. Web/Sites/triggeredwebjobs/Run/művelet | Web Apps indított webjobs-feladatok futtatása. |
> | Műveletek | Microsoft. Web/Sites/használat/olvasás | Web Apps használati adatok beolvasása. |
> | Műveletek | Microsoft. Web/Sites/virtualnetworkconnections/delete | Web Apps Virtual Network kapcsolatok törlése. |
> | Műveletek | Microsoft. Web/Sites/virtualnetworkconnections/Gateway/READ | Web Apps Virtual Network kapcsolatok átjáróinak beolvasása. |
> | Műveletek | Microsoft. Web/Sites/virtualnetworkconnections/Gateway/Write | Frissítse Web Apps Virtual Network Connections átjárókat. |
> | Műveletek | Microsoft. Web/Sites/virtualnetworkconnections/READ | Web Apps Virtual Network kapcsolatok beolvasása. |
> | Műveletek | Microsoft. Web/Sites/virtualnetworkconnections/Write | Web Apps Virtual Network kapcsolatok frissítése. |
> | Műveletek | Microsoft. Web/Sites/webjobs/olvasás | Web Apps webjobs-feladatok beolvasása. |
> | Műveletek | Microsoft. Web/Sites/Write | Hozzon létre egy új webalkalmazást, vagy frissítsen egy meglévőt |
> | Műveletek | Microsoft. Web/SKU/READ | SKU-i beolvasása. |
> | Műveletek | Microsoft. Web/sourcecontrols/READ | Forrás vezérlők beolvasása. |
> | Műveletek | Microsoft. Web/sourcecontrols/írás | Frissítse a forrás vezérlőelemeket. |
> | Műveletek | Microsoft. Web/regisztráció/művelet | A Microsoft. web erőforrás-szolgáltató regisztrációjának törlése az előfizetéshez. |
> | Műveletek | Microsoft. Web/érvényesítés/művelet | Érvényesít. |
> | Műveletek | Microsoft. Web/verifyhostingenvironmentvnet/művelet | Az üzemeltetési környezet vnet ellenőrzése. |

## <a name="microsoftworkloadmonitor"></a>Microsoft. WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft. WorkloadMonitor/összetevők/olvasás | Az erőforrás összetevőinek beolvasása |
> | Műveletek | Microsoft. WorkloadMonitor/componentsSummary/READ | Az összetevők összegzésének beolvasása |
> | Műveletek | Microsoft. WorkloadMonitor/monitorInstances/READ | Az erőforráshoz tartozó figyelők példányainak beolvasása |
> | Műveletek | Microsoft. WorkloadMonitor/monitorInstancesSummary/READ | A figyelő példányok összegzésének beolvasása |
> | Műveletek | Microsoft. WorkloadMonitor/figyelők/olvasás | Az erőforráshoz tartozó figyelők beolvasása |
> | Műveletek | Microsoft. WorkloadMonitor/figyelők/írás | Az erőforráshoz tartozó figyelő konfigurálása |
> | Műveletek | Microsoft. WorkloadMonitor/notificationSettings/READ | Értesítési beállítások beolvasása az erőforráshoz |
> | Műveletek | Microsoft. WorkloadMonitor/notificationSettings/Write | Az erőforráshoz tartozó értesítési beállítások konfigurálása |
> | Műveletek | Microsoft. WorkloadMonitor/Operations/READ | A támogatott műveletek beolvasása |

## <a name="next-steps"></a>Következő lépések

- [Erőforrás-szolgáltató egyeztetése a szolgáltatással](../azure-resource-manager/azure-services-resource-providers.md)
- [Egyéni szerepkörök Azure-erőforrásokhoz](custom-roles.md)
- [Beépített szerepkörök Azure-erőforrásokhoz](built-in-roles.md)
