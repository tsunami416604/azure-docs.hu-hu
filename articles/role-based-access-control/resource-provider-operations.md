---
title: Az Azure Resource Manager erőforrás-szolgáltatói műveletekhez |} Microsoft Docs
description: A Microsoft Azure Resource Manager erőforrás-szolgáltató elérhető műveleteinek listázása
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/11/2018
ms.author: rolyon
ms.openlocfilehash: 08059da95baa9e70b8dba2dd847d0b28669778b7
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Az Azure Resource Manager erőforrás-szolgáltatói műveletekhez

Ez a cikk az egyes Azure Resource Manager erőforrás-szolgáltató elérhető műveleteinek listázása. Ezek a műveletek használható [egyéni szerepkörök](custom-roles.md) biztosításához részletes [szerepköralapú hozzáférés-vezérlést (RBAC)](overview.md) erőforrások az Azure-ban. A művelet karakterláncok formátuma a következő: `Microsoft.{ProviderName}/{ChildResourceType}/{action}`

Az erőforrás-szolgáltatói műveletekhez mindig fejlesztik vannak. Megtekintéséhez használja a legújabb műveletek [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) vagy [az szolgáltató műveletlista](/cli/azure/provider/operation#az-provider-operation-list).

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.AAD/domainServices/delete | Deletes Domain Services. |
> | Művelet | Microsoft.AAD/domainServices/read | Reads Domain Services. |
> | Művelet | Microsoft.AAD/domainServices/write | Write Domain Services |
> | Művelet | Microsoft.AAD/locations/operationresults/read | Egy aszinkron művelet állapotát olvassa. |
> | Művelet | Microsoft.AAD/Operations/read | A művelet, mert a honosított rövid leírás megjelenjen-e a felhasználó. |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.aadiam/diagnosticsettings/DELETE | A diagnosztikai beállítás törlése |
> | Művelet | Microsoft.aadiam/diagnosticsettings/Read | A diagnosztikai beállítás beolvasásakor. |
> | Művelet | Microsoft.aadiam/diagnosticsettings/Write | A diagnosztikai beállításának írása |
> | Művelet | Microsoft.aadiam/diagnosticsettingscategories/Read | A diagnosztikai beállításának kategóriák olvasása |
> | Művelet | microsoft.aadiam/tenants/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Művelet | microsoft.aadiam/tenants/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Művelet | microsoft.aadiam/tenants/providers/Microsoft.Insights/logDefinitions/read | A bérlők számára a naplók beolvasása |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.Addons/operations/read | Lekérdezi a függő Entitás műveletek támogatott. |
> | Művelet | Microsoft.Addons/register/action | A megadott előfizetés regisztrálása Microsoft.Addons |
> | Művelet | Microsoft.Addons/supportProviders/listsupportplaninfo/action | A megadott előfizetés aktuális támogatási terv információkat sorolja fel. |
> | Művelet | Microsoft.Addons/supportProviders/supportPlanTypes/delete | Eltávolítja a megadott kanonikus támogatási csomag |
> | Művelet | Microsoft.Addons/supportProviders/supportPlanTypes/read | A megadott kanonikus támogatási terv állapot beolvasása. |
> | Művelet | Microsoft.Addons/supportProviders/supportPlanTypes/write | A megadott típus kanonikus támogatási terv hozzáadja. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.ADHybridHealthService/addsservices/action | Új erdő létrehozása a bérlő számára. |
> | Művelet | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | Minden kiszolgáló lekérdezi a megadott szolgáltatás neve. |
> | Művelet | Microsoft.ADHybridHealthService/addsservices/alerts/read | Lekérdezi a riasztás részleteit a erdő például alertid, riasztás jelenik meg, dátum, a riasztás utolsó észlelt, a riasztás leírásában, az utolsó frissített, a riasztási szint, riasztás állapota, riasztás hibaelhárítási hivatkozások stb. |
> | Művelet | Microsoft.ADHybridHealthService/addsservices/configuration/read | Lekérdezi a szolgáltatás konfigurációját az erdőhöz. Példa - erdő neve, Functionla szint, tartomány-kiosztási főkiszolgáló műveleti Főkiszolgálói szerepkörének, séma-főkiszolgáló műveleti Főkiszolgálói szerepkör stb. |
> | Művelet | Microsoft.ADHybridHealthService/addsservices/delete | Szolgáltatás törlése, és azt-kiszolgálókból, valamint az egészségügyi adatokat. |
> | Művelet | Microsoft.ADHybridHealthService/addsservices/dimensions/read | A tartományok és a helyek adatokat lekérdezi az erdőhöz. Példa-állapot, aktív riasztások feloldotta a riasztásokat, például a tartomány működési szintjét, erdő, infrastruktúra-főkiszolgálóhoz, az elsődleges tartományvezérlő, tulajdonságok relatív azonosítókat kezelő fő stb.  |
> | Művelet | Microsoft.ADHybridHealthService/addsservices/features/userpreference/read | Lekérdezi a felhasználói beállítás, az erdőhöz.<br>Példa - pl. ldapsuccessfulbinds ntlmauthentications, kerberosauthentications, addsinsightsagentprivatebytes, ldapsearches MetricCounterName.<br>A felhasználói felület diagramok stb beállításait. |
> | Művelet | Microsoft.ADHybridHealthService/addsservices/forestsummary/read | Erdő összefoglaló lekérdezi a megadott erdő esetén, például az erdő neve, ebben az erdőben a tartományok száma, a helyek számát és helyek részletek stb. |
> | Művelet | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | Beolvassa a támogatott metrikák listáját egy adott szolgáltatáshoz.<br>Példa extranetes fiókzárolást, sikertelen kérelmek teljes, függőben lévő a jogkivonat-kérelmeket (Proxy), a jogkivonat-kérelmeket/másodperc az AD FS szolgáltatás stb.<br>NTLM-hitelesítés/mp, LDAP sikeres kötések/mp, LDAP-kötési idő, LDAP aktív szálak, Kerberos hitelesítési események másodpercenkénti stb ATQ szálak teljes ADDomainService a.<br>Futtassa a profil késés, a TCP-kapcsolatok létrehozott, a Insights Agent a saját memória, a exportálási statisztikák az Azure AD-ADSync szolgáltatás. |
> | Művelet | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | Megadott egy szolgáltatást, ez az API lekérdezi a mérőszámok-adatokat.<br>Például az API segítségével kapcsolatos információk: extranetes fiókzárolást, a sikertelen kérelmek teljes, a függőben lévő a jogkivonat-kérelmeket (Proxy), a jogkivonat-kérelmeket/sec ADFederation szolgáltatás stb.<br>NTLM-hitelesítés/mp, LDAP sikeres kötések/mp, LDAP-kötési idő, LDAP aktív szálak, Kerberos hitelesítési események másodpercenkénti stb ATQ szálak teljes ADDomain szolgáltatáshoz.<br>Profil késést, az TCP-kapcsolatok létrehozása Insights Agent a saját memória, Exportálás futtatása statisztikák az Azure AD Sync szolgáltatás. |
> | Művelet | Microsoft.ADHybridHealthService/addsservices/premiumcheck/read | Ez az API premium-bérlő minden előkészítve ADDomainServices listájának beolvasása. |
> | Művelet | Microsoft.ADHybridHealthService/addsservices/read | A megadott szolgáltatásnév lekéri a szolgáltatás részletes adatai. |
> | Művelet | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | Lekéri a megadott szolgáltatásnevéhez tartozó összes kiszolgáló replikációs adatokat. |
> | Művelet | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | Lekérdezi a tartományvezérlők és a replikálási hibákat, ha van ilyen. |
> | Művelet | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | Lekérdezi és replikálási adatainak az adott erdőben a tartománynév-vezérlő listája végezze el. |
> | Művelet | Microsoft.ADHybridHealthService/addsservices/servicemembers/action | Adja hozzá a szolgáltatás a server-példány. |
> | Művelet | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | Ez az api során a kiszolgáló regisztrációjának ADDomainService, a hitelesítő adatokat lekérni a bevezetéshez új kiszolgálók neve. |
> | Művelet | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | Egy adott szolgáltatáshoz, és a bérlői kiszolgáló törlése. |
> | Művelet | Microsoft.ADHybridHealthService/addsservices/write | Létrehozza vagy frissíti a ADDomainService példány a bérlő számára. |
> | Művelet | Microsoft.ADHybridHealthService/configuration/action | Frissítések bérlő konfigurációjához. |
> | Művelet | Microsoft.ADHybridHealthService/configuration/read | Beolvassa a bérlő konfigurációjához. |
> | Művelet | Microsoft.ADHybridHealthService/configuration/write | Létrehoz egy bérlői konfigurációt. |
> | Művelet | Microsoft.ADHybridHealthService/logs/contents/read | Az ügynök telepítése és a blob tárolt regisztrációs naplók tartalmának beolvasása. |
> | Művelet | Microsoft.ADHybridHealthService/logs/read | Az ügynök telepítése és a regisztrációs naplókat lekérdezi a bérlő számára. |
> | Művelet | Microsoft.ADHybridHealthService/operations/read | Beolvassa a rendszer által támogatott műveletek listáját. |
> | Művelet | Microsoft.ADHybridHealthService/register/action | Állapotfigyelő szolgáltatás ADHybrid erőforrás-szolgáltató regisztrálja, és lehetővé teszi, hogy a ADHybrid Állapotfigyelő szolgáltatás-erőforrást. |
> | Művelet | Microsoft.ADHybridHealthService/reports/availabledeployments/read | Beolvassa a felhasználói incidensek támogatásához DevOps által használt elérhető régiók listáját. |
> | Művelet | Microsoft.ADHybridHealthService/reports/badpassword/read | Beolvassa a rossz jelszót listáját az Active Directory összevonási szolgáltatás összes felhasználója számára. |
> | Művelet | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | Lekérdezi a Blob SAS URI-t tartalmazó állapotát, és a / / IP-cím, egy adott bérlő naponta UserId kísérletek gyakorisága rossz felhasználónév/jelszó újonnan a várólistában levő jelentés feladat végső eredménye. |
> | Művelet | Microsoft.ADHybridHealthService/reports/consentedtodevopstenants/read | Beolvassa a DevOps listáját átadni kívánt hozzájárult e bérlők. Általában használt ügyfél-támogatási szolgálatához. |
> | Művelet | Microsoft.ADHybridHealthService/reports/isdevops/read | Lekérdezi a érték azt jelzi, hogy a teannt időjárási DevOps átadni kívánt hozzájárult e vagy sem. |
> | Művelet | Microsoft.ADHybridHealthService/reports/selectdevopstenant/read | A kijelölt fejlesztői ops bérlői userid(objectid) frissítések. |
> | Művelet | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | Lekérdezi a megadott tenant kijelölt telepítése. |
> | Művelet | Microsoft.ADHybridHealthService/reports/tenantassigneddeployment/read | A bérlő azonosítója megadott lekérdezi a bérlő tárolási helyét. |
> | Művelet | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | Lekérdezi a földrajzi hely, ahonnan adatokat érhető el. |
> | Művelet | Microsoft.ADHybridHealthService/services/action | Frissíti a szolgáltatáspéldány a bérlőben. |
> | Művelet | Microsoft.ADHybridHealthService/services/alerts/read | Beolvassa a riasztásokat, a szolgáltatás számára. |
> | Művelet | Microsoft.ADHybridHealthService/services/alerts/read | Beolvassa a riasztásokat, a szolgáltatás számára. |
> | Művelet | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | A szolgáltatás a megadott neve ellenőrzi, egy szolgáltatás rendelkezik-e minden szükséges, hogy a szolgáltatás használatához. |
> | Művelet | Microsoft.ADHybridHealthService/services/delete | Törli a szolgáltatáspéldány a bérlőben. |
> | Művelet | Microsoft.ADHybridHealthService/services/exporterrors/read | Az exportálási hibák lekérdezi a megadott szinkronizálási szolgáltatás. |
> | Művelet | Microsoft.ADHybridHealthService/services/exportstatus/read | Exportálás állapotát olvassa be egy adott szolgáltatáshoz. |
> | Művelet | Microsoft.ADHybridHealthService/services/feedbacktype/feedback/read | Riasztások visszajelzést kap egy adott szolgáltatáshoz, és a kiszolgáló. |
> | Művelet | Microsoft.ADHybridHealthService/services/metricmetadata/read | Beolvassa a támogatott metrikák listáját egy adott szolgáltatáshoz.<br>Példa extranetes fiókzárolást, sikertelen kérelmek teljes, függőben lévő a jogkivonat-kérelmeket (Proxy), a jogkivonat-kérelmeket/másodperc az AD FS szolgáltatás stb.<br>NTLM-hitelesítés/mp, LDAP sikeres kötések/mp, LDAP-kötési idő, LDAP aktív szálak, Kerberos hitelesítési események másodpercenkénti stb ATQ szálak teljes ADDomainService a.<br>Futtassa a profil késés, a TCP-kapcsolatok létrehozott, a Insights Agent a saját memória, a exportálási statisztikák az Azure AD-ADSync szolgáltatás. |
> | Művelet | Microsoft.ADHybridHealthService/services/metrics/groups/average/read | Megadott egy szolgáltatás, az API lekérdezi a átlagos metrikáihoz egy adott szolgáltatáshoz.<br>Például az API segítségével kapcsolatos információk: extranetes fiókzárolást, a sikertelen kérelmek teljes, a függőben lévő a jogkivonat-kérelmeket (Proxy), a jogkivonat-kérelmeket/sec ADFederation szolgáltatás stb.<br>NTLM-hitelesítés/mp, LDAP sikeres kötések/mp, LDAP-kötési idő, LDAP aktív szálak, Kerberos hitelesítési események másodpercenkénti stb ATQ szálak teljes ADDomain szolgáltatáshoz.<br>Profil késést, az TCP-kapcsolatok létrehozása Insights Agent a saját memória, Exportálás futtatása statisztikák az Azure AD Sync szolgáltatás. |
> | Művelet | Microsoft.ADHybridHealthService/services/metrics/groups/read | Megadott egy szolgáltatást, ez az API lekérdezi a mérőszámok-adatokat.<br>Például az API segítségével kapcsolatos információk: extranetes fiókzárolást, a sikertelen kérelmek teljes, a függőben lévő a jogkivonat-kérelmeket (Proxy), a jogkivonat-kérelmeket/sec ADFederation szolgáltatás stb.<br>NTLM-hitelesítés/mp, LDAP sikeres kötések/mp, LDAP-kötési idő, LDAP aktív szálak, Kerberos hitelesítési események másodpercenkénti stb ATQ szálak teljes ADDomain szolgáltatáshoz.<br>Profil késést, az TCP-kapcsolatok létrehozása Insights Agent a saját memória, Exportálás futtatása statisztikák az Azure AD Sync szolgáltatás. |
> | Művelet | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | Megadott egy szolgáltatást, ez az API lekérdezi a összesített nézet metrikáihoz egy adott szolgáltatáshoz.<br>Például az API segítségével kapcsolatos információk: extranetes fiókzárolást, a sikertelen kérelmek teljes, a függőben lévő a jogkivonat-kérelmeket (Proxy), a jogkivonat-kérelmeket/sec ADFederation szolgáltatás stb.<br>NTLM-hitelesítés/mp, LDAP sikeres kötések/mp, LDAP-kötési idő, LDAP aktív szálak, Kerberos hitelesítési események másodpercenkénti stb ATQ szálak teljes ADDomain szolgáltatáshoz.<br>Profil késést, az TCP-kapcsolatok létrehozása Insights Agent a saját memória, Exportálás futtatása statisztikák az Azure AD Sync szolgáltatás. |
> | Művelet | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | Adja hozzá, vagy a szolgáltatás figyelési konfigurációjának frissítése. |
> | Művelet | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | Lekéri egy adott szolgáltatáshoz a figyelési konfigurációhoz. |
> | Művelet | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | Adja hozzá, vagy egy szolgáltatás figyelési beállítások frissítése. |
> | Művelet | Microsoft.ADHybridHealthService/services/premiumcheck/read | Ez az API lekérdezi egy premium-bérlő minden előkészítve szolgáltatások listájában. |
> | Művelet | Microsoft.ADHybridHealthService/services/read | A szolgáltatáspéldány a bérlő beolvasása. |
> | Művelet | Microsoft.ADHybridHealthService/services/reports/details/read | Lekérdezi a jelentés az 50 legfontosabb felhasználók rossz jelszó hibák az elmúlt 7 napban |
> | Művelet | Microsoft.ADHybridHealthService/services/servicemembers/action | A szolgáltatás hoz létre egy kiszolgálópéldányt. |
> | Művelet | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | A riasztások beolvassa a kiszolgálón. |
> | Művelet | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | Kiszolgáló regisztrálása során a hitelesítő adatokat lekérni a bevezetéshez új kiszolgálók az api neve. |
> | Művelet | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | Egy adott kiszolgálóhoz az API megjelenő a kiszolgálókat és a legújabb minden feltöltésének idejét feltöltése listájának lekérése. |
> | Művelet | Microsoft.ADHybridHealthService/services/servicemembers/delete | Törli a szolgáltatás a server-példányra. |
> | Művelet | Microsoft.ADHybridHealthService/services/servicemembers/exportstatus/read | A szinkronizálás exportálása hiba részletes adatait lekérdezi a megadott szinkronizálási szolgáltatás. |
> | Művelet | Microsoft.ADHybridHealthService/services/servicemembers/metrics/groups/read | Megadott egy szolgáltatást, ez az API lekérdezi a mérőszámok-adatokat.<br>Például az API segítségével kapcsolatos információk: extranetes fiókzárolást, a sikertelen kérelmek teljes, a függőben lévő a jogkivonat-kérelmeket (Proxy), a jogkivonat-kérelmeket/sec ADFederation szolgáltatás stb.<br>NTLM-hitelesítés/mp, LDAP sikeres kötések/mp, LDAP-kötési idő, LDAP aktív szálak, Kerberos hitelesítési események másodpercenkénti stb ATQ szálak teljes ADDomain szolgáltatáshoz.<br>Profil késést, az TCP-kapcsolatok létrehozása Insights Agent a saját memória, Exportálás futtatása statisztikák az Azure AD Sync szolgáltatás. |
> | Művelet | Microsoft.ADHybridHealthService/services/servicemembers/read | Olvassa be a szolgáltatás a server-példányra. |
> | Művelet | Microsoft.ADHybridHealthService/services/servicemembers/serviceconfiguration/read | Lekéri egy adott bérlő szolgáltatás konfigurációját. |
> | Művelet | Microsoft.ADHybridHealthService/services/tenantwhitelisting/read | Lekéri egy adott bérlő az funkció engedélyezett állapotát. |
> | Művelet | Microsoft.ADHybridHealthService/services/write | Hozza létre a szolgáltatáspéldány, a bérlő. |
> | Művelet | Microsoft.ADHybridHealthService/unregister/action | Az előfizetés ADHybrid Állapotfigyelő szolgáltatás erőforrás-szolgáltató regisztrációjának törlése. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.Advisor/configurations/read | Konfigurációk beolvasása |
> | Művelet | Microsoft.Advisor/configurations/write | Létrehozza vagy frissítések konfigurációja |
> | Művelet | Microsoft.Advisor/generateRecommendations/action | Ajánlatok generálása |
> | Művelet | Microsoft.Advisor/generateRecommendations/read | Lekérdezi készítése javaslatok állapota |
> | Művelet | Microsoft.Advisor/operations/read | Lekérdezi a műveleteket, a Microsoft Advisor |
> | Művelet | Microsoft.Advisor/recommendations/read | Javaslatok beolvasása |
> | Művelet | Microsoft.Advisor/recommendations/suppressions/delete | Tiltási törlése |
> | Művelet | Microsoft.Advisor/recommendations/suppressions/read | Lekérdezi a suppressions |
> | Művelet | Microsoft.Advisor/recommendations/suppressions/write | Suppressions hoz létre, frissítések |
> | Művelet | Microsoft.Advisor/register/action | A Microsoft Advisor előfizetésének regisztrálása |
> | Művelet | Microsoft.Advisor/suppressions/delete | Tiltási törlése |
> | Művelet | Microsoft.Advisor/suppressions/read | Lekérdezi a suppressions |
> | Művelet | Microsoft.Advisor/suppressions/write | Suppressions hoz létre, frissítések |
> | Művelet | Microsoft.Advisor/unregister/action | A Microsoft Advisor az előfizetés regisztrációjának törlése |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.AlertsManagement/alerts/changestate/action | Módosítsa a riasztás állapotát. |
> | Művelet | Microsoft.AlertsManagement/alerts/read | A riasztások lekérése a bemeneti szűrőket. |
> | Művelet | Microsoft.AlertsManagement/alertsSummary/read | A riasztások összefoglaló adatait beolvasása |
> | Művelet | Microsoft.AlertsManagement/Operations/read | A megadott műveletek beolvasása |
> | Művelet | Microsoft.AlertsManagement/smartGroups/changestate/action | Az intelligens csoport állapotának módosítása |
> | Művelet | Microsoft.AlertsManagement/smartGroups/read | Az intelligens csoportok lekérése a bemeneti szűrők |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.AnalysisServices/locations/checkNameAvailability/action | Ellenőrzi, hogy a megadott Analysis Server neve érvényes, és nincs használatban. |
> | Művelet | Microsoft.AnalysisServices/locations/operationresults/read | A megadott művelet eredményének adatainak beolvasása. |
> | Művelet | Microsoft.AnalysisServices/locations/operationstatuses/read | A megadott műveleti állapotának adatainak beolvasása. |
> | Művelet | Microsoft.AnalysisServices/operations/read | A műveletek adatainak beolvasása |
> | Művelet | Microsoft.AnalysisServices/register/action | Regisztrálja az Analysis Services erőforrás-szolgáltató. |
> | Művelet | Microsoft.AnalysisServices/servers/delete | Az elemzési kiszolgáló törlése. |
> | Művelet | Microsoft.AnalysisServices/servers/listGatewayStatus/action | Az átjáró a kiszolgálóhoz társított állapotát listázza. |
> | Művelet | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/diagnosticSettings/read | Az Analysis Server diagnosztikai beállításának beolvasása. |
> | Művelet | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti az Analysis Server diagnosztikai beállításának |
> | Művelet | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/logDefinitions/read | A kiszolgálók naplók beolvasása. |
> | Művelet | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/metricDefinitions/read | Az elemzési kiszolgáló elérhető metrikai meghatározások beolvasása |
> | Művelet | Microsoft.AnalysisServices/servers/read | A megadott elemzési kiszolgáló adatainak beolvasása. |
> | Művelet | Microsoft.AnalysisServices/servers/resume/action | Az elemzési kiszolgáló folytatja. |
> | Művelet | Microsoft.AnalysisServices/servers/skus/read | A kiszolgáló elérhető SKU információinak lekérése |
> | Művelet | Microsoft.AnalysisServices/servers/suspend/action | Felfüggeszti az elemzési kiszolgáló. |
> | Művelet | Microsoft.AnalysisServices/servers/write | Létrehozza vagy frissíti a megadott Analysis-kiszolgálóhoz. |
> | Művelet | Microsoft.AnalysisServices/skus/read | Az SKU adatainak beolvasása |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.ApiManagement/checkNameAvailability/read | Ellenőrzi, hogy a megadott szolgáltatásnév érhető el |
> | Művelet | Microsoft.ApiManagement/operations/read | Minden API-műveleteket elérhető Microsoft.ApiManagement erőforrás olvasása |
> | Művelet | Microsoft.ApiManagement/register/action | Előfizetés regisztrálása a Microsoft.ApiManagement erőforrás-szolgáltató |
> | Művelet | Microsoft.ApiManagement/reports/read | Összesítve időszakokat, földrajzi régiót, fejlesztők, termékek, API-k, műveletek, előfizetés és byRequest jelentések lekérése. |
> | Művelet | Microsoft.ApiManagement/service/apis/delete | Távolítsa el a meglévő API |
> | Művelet | Microsoft.ApiManagement/service/apis/diagnostics/delete | Távolítsa el a meglévő diagnosztikai |
> | Művelet | Microsoft.ApiManagement/service/apis/diagnostics/loggers/delete | Távolítsa el a naplózó diagnosztikai beállítással leképezése |
> | Művelet | Microsoft.ApiManagement/service/apis/diagnostics/loggers/read | Meglévő diagnosztikai figyelő szoftverek listájának beolvasása |
> | Művelet | Microsoft.ApiManagement/service/apis/diagnostics/loggers/write | Térkép naplózó diagnosztikai beállítások |
> | Művelet | Microsoft.ApiManagement/service/apis/diagnostics/read | Diagnosztika vagy a diagnosztika Get részleteinek listáját |
> | Művelet | Microsoft.ApiManagement/service/apis/diagnostics/write | Új diagnosztikai hozzáadásakor vagy módosításakor a meglévő diagnosztikai részletek |
> | Művelet | Microsoft.ApiManagement/service/apis/issues/attachments/delete | Eltávolítja a meglévő melléklet |
> | Művelet | Microsoft.ApiManagement/service/apis/issues/attachments/read | Ezzel a melléklet adatokat probléma mellékletek vagy lekérdezi az API Management probléma |
> | Művelet | Microsoft.ApiManagement/service/apis/issues/attachments/write | Api probléma melléklet hozzáadása |
> | Művelet | Microsoft.ApiManagement/service/apis/issues/comments/delete | Eltávolítja a meglévő megjegyzés |
> | Művelet | Microsoft.ApiManagement/service/apis/issues/comments/read | Lekérdezi probléma megjegyzések vagy lekérdezi az API Management probléma Megjegyzés részletei |
> | Művelet | Microsoft.ApiManagement/service/apis/issues/comments/write | Api probléma Megjegyzés hozzáadása |
> | Művelet | Microsoft.ApiManagement/service/apis/issues/delete | Eltávolítja a meglévő probléma |
> | Művelet | Microsoft.ApiManagement/service/apis/issues/read | Probléma merül fel az API-t vagy lekérdezi az API Management probléma részletei beolvasása |
> | Művelet | Microsoft.ApiManagement/service/apis/issues/write | Api-probléma hozzáadásakor vagy módosításakor api probléma |
> | Művelet | Microsoft.ApiManagement/service/apis/operations/delete | Távolítsa el a meglévő API-művelet |
> | Művelet | Microsoft.ApiManagement/service/apis/operations/policies/delete | API-művelet házirendek házirend-konfiguráció eltávolítása |
> | Művelet | Microsoft.ApiManagement/service/apis/operations/policies/read | API-művelet vagy Get házirend konfigurációs adatait az API-művelet házirendek lekéréséhez |
> | Művelet | Microsoft.ApiManagement/service/apis/operations/policies/write | Állítsa be a házirend konfigurációs adatait az API-művelet |
> | Művelet | Microsoft.ApiManagement/service/apis/operations/policy/delete | Távolítsa el a házirend-konfigurációt a művelet |
> | Művelet | Microsoft.ApiManagement/service/apis/operations/policy/read | Ezzel a házirend konfigurációs adatokat művelet |
> | Művelet | Microsoft.ApiManagement/service/apis/operations/policy/write | Adja meg a művelet házirend konfigurációs részleteit |
> | Művelet | Microsoft.ApiManagement/service/apis/operations/read | A meglévő API műveletek listájának vagy részletes API művelet |
> | Művelet | Microsoft.ApiManagement/service/apis/operations/tags/delete | Meglévő címke meglévő műveletet a társítás törlése |
> | Művelet | Microsoft.ApiManagement/service/apis/operations/tags/read | A művelet vagy a kód első részletek társított címkék beolvasása |
> | Művelet | Microsoft.ApiManagement/service/apis/operations/tags/write | Meglévő címke társítása meglévő művelet |
> | Művelet | Microsoft.ApiManagement/service/apis/operations/write | Új API-művelet létrehozni vagy frissíteni a meglévő API-művelet |
> | Művelet | Microsoft.ApiManagement/service/apis/operationsByTags/read | Művelet/címke társítások listájának beolvasása |
> | Művelet | Microsoft.ApiManagement/service/apis/policies/delete | Távolítsa el a házirend-konfigurációt a API-házirendek |
> | Művelet | Microsoft.ApiManagement/service/apis/policies/read | Házirendek lekérése az API-t vagy a Get házirend konfigurációs adatait az API-hoz |
> | Művelet | Microsoft.ApiManagement/service/apis/policies/write | Állítsa be a házirend-konfigurációs adatait az API-hoz |
> | Művelet | Microsoft.ApiManagement/service/apis/policy/delete | Távolítsa el a házirend-konfigurációs API |
> | Művelet | Microsoft.ApiManagement/service/apis/policy/read | Ezzel a házirend konfigurációs adatokat az API-hoz |
> | Művelet | Microsoft.ApiManagement/service/apis/policy/write | Állítsa be a házirend-konfigurációs adatait az API-hoz |
> | Művelet | Microsoft.ApiManagement/service/apis/products/read | Az API-t részét képező összes termék beszerzése |
> | Művelet | Microsoft.ApiManagement/service/apis/read | Szerezze be az összes regisztrált API-k vagy Get részleteit API listát |
> | Művelet | Microsoft.ApiManagement/service/apis/releases/delete | Eltávolítja az összes kiadásaiban az API-t vagy API eltávolítása kiadás |
> | Művelet | Microsoft.ApiManagement/service/apis/releases/read | Lekérni kiadások API reelase API-t vagy a Get részleteit |
> | Művelet | Microsoft.ApiManagement/service/apis/releases/write | Új API-verziót létrehozása vagy meglévő API-kiadás frissítése |
> | Művelet | Microsoft.ApiManagement/service/apis/revisions/delete | Eltávolítja az összes változatának az API-k |
> | Művelet | Microsoft.ApiManagement/service/apis/revisions/read | Az API-k tartozó változatok beolvasása |
> | Művelet | Microsoft.ApiManagement/service/apis/schemas/delete | Eltávolítja a meglévő séma |
> | Művelet | Microsoft.ApiManagement/service/apis/schemas/document/read | A dokumentum a séma leíró lekérése |
> | Művelet | Microsoft.ApiManagement/service/apis/schemas/document/write | A dokumentum leíró a séma frissítése |
> | Művelet | Microsoft.ApiManagement/service/apis/schemas/read | Lekérdezi a sémák az API által használt vagy a sémák lekérdezi a megadott API-hoz. |
> | Művelet | Microsoft.ApiManagement/service/apis/schemas/write | Beállítja a az API által használt sémák |
> | Művelet | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | Távolítsa el az API-t címke leírása |
> | Művelet | Microsoft.ApiManagement/service/apis/tagDescriptions/read | Címkék leírások beszerzése API-t vagy az beszerzése címke leírás hatókörében API-hatóköre |
> | Művelet | Microsoft.ApiManagement/service/apis/tagDescriptions/write | Hatókör leírása létrehozása/módosítása címke API |
> | Művelet | Microsoft.ApiManagement/service/apis/tags/delete | Meglévő API/címke társításának megszüntetése |
> | Művelet | Microsoft.ApiManagement/service/apis/tags/read | Minden API/címke társítás API/címke társítás API-t vagy a Get részleteinek beolvasása |
> | Művelet | Microsoft.ApiManagement/service/apis/tags/write | Új API/címke társítás hozzáadása |
> | Művelet | Microsoft.ApiManagement/service/apis/write | Hozzon létre új API-JÁVAL vagy a meglévő API részleteinek frissítése |
> | Művelet | Microsoft.ApiManagement/service/apisByTags/read | Szerezze be az API/címke társítások listát |
> | Művelet | Microsoft.ApiManagement/service/api-version-sets/delete | Távolítsa el a meglévő VersionSet |
> | Művelet | Microsoft.ApiManagement/service/api-version-sets/read | Itt olvashatók verzió csoport entitásokat vagy egy VersionSet lekérdezi részleteit listája |
> | Művelet | Microsoft.ApiManagement/service/api-version-sets/versions/read | Verzió entitások listájának beolvasása |
> | Művelet | Microsoft.ApiManagement/service/api-version-sets/write | Új VersionSet létrehozása vagy meglévő VersionSet részleteinek frissítése |
> | Művelet | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | Frissíti a Microsoft.ApiManagement erőforrásaihoz, a virtuális hálózatot válasszon frissített hálózati beállításokat. |
> | Művelet | Microsoft.ApiManagement/service/authorizationServers/delete | Távolítsa el a meglévő engedélyezési kiszolgáló |
> | Művelet | Microsoft.ApiManagement/service/authorizationServers/read | Engedélyezési kiszolgálók listája vagy az engedélyezési kiszolgáló adatait az beszerzése |
> | Művelet | Microsoft.ApiManagement/service/authorizationServers/write | Hozzon létre egy új hitelesítési kiszolgáló vagy egy meglévő engedélyezési kiszolgáló frissítés részletei |
> | Művelet | Microsoft.ApiManagement/service/backends/delete | Távolítsa el a létező háttérrendszerek |
> | Művelet | Microsoft.ApiManagement/service/backends/read | Háttérkiszolgálókon listáját, vagy a részletek a háttér |
> | Művelet | Microsoft.ApiManagement/service/backends/reconnect/action | Újracsatlakozás kérelem létrehozása |
> | Művelet | Microsoft.ApiManagement/service/backends/write | Adja hozzá egy új háttér vagy a meglévő háttér részleteinek frissítése |
> | Művelet | Microsoft.ApiManagement/service/backup/action | A biztonsági mentési API Management szolgáltatás a felhasználó a megadott tárolóhoz megadott tárfiók |
> | Művelet | Microsoft.ApiManagement/service/certificates/delete | Távolítsa el a meglévő tanúsítvány |
> | Művelet | Microsoft.ApiManagement/service/certificates/read | A tanúsítványok listájának vagy a tanúsítvány adatainak lekérése |
> | Művelet | Microsoft.ApiManagement/service/certificates/write | Új tanúsítvány felvétele |
> | Művelet | Microsoft.ApiManagement/service/delete | Törli az API Management Service-példány |
> | Művelet | Microsoft.ApiManagement/service/diagnostics/delete | Távolítsa el a meglévő diagnosztikai |
> | Művelet | Microsoft.ApiManagement/service/diagnostics/loggers/delete | Távolítsa el a naplózó diagnosztikai beállítással leképezése |
> | Művelet | Microsoft.ApiManagement/service/diagnostics/loggers/read | Meglévő diagnosztikai figyelő szoftverek listájának beolvasása |
> | Művelet | Microsoft.ApiManagement/service/diagnostics/loggers/write | Térkép naplózó diagnosztikai beállítások |
> | Művelet | Microsoft.ApiManagement/service/diagnostics/read | Diagnosztika vagy a diagnosztika Get részleteinek listáját |
> | Művelet | Microsoft.ApiManagement/service/diagnostics/write | Új diagnosztikai hozzáadásakor vagy módosításakor a meglévő diagnosztikai részletek |
> | Művelet | Microsoft.ApiManagement/service/getssotoken/action | Lekérdezi SSO token használható API Management szolgáltatás örökölt portáljára rendszergazdaként történő bejelentkezéshez |
> | Művelet | Microsoft.ApiManagement/service/groups/delete | Távolítsa el a meglévő csoporthoz |
> | Művelet | Microsoft.ApiManagement/service/groups/read | Csoportok vagy egy csoport lekérdezi részleteinek listáját |
> | Művelet | Microsoft.ApiManagement/service/groups/users/delete | Távolítsa el a meglévő felhasználói meglévő csoportból |
> | Művelet | Microsoft.ApiManagement/service/groups/users/read | Csoport felhasználók listájának beolvasása |
> | Művelet | Microsoft.ApiManagement/service/groups/users/write | Meglévő felhasználó hozzáadása meglévő csoporthoz |
> | Művelet | Microsoft.ApiManagement/service/groups/write | Új csoport létrehozása vagy meglévő csoport részleteinek frissítése |
> | Művelet | Microsoft.ApiManagement/service/identityProviders/delete | Távolítsa el a meglévő identitásszolgáltató |
> | Művelet | Microsoft.ApiManagement/service/identityProviders/read | Az identitás-szolgáltatóktól vagy identitásszolgáltató Get részleteinek listáját beszerzése |
> | Művelet | Microsoft.ApiManagement/service/identityProviders/write | Hozzon létre egy új identitásszolgáltató vagy a frissítés részleteit egy meglévő identitásszolgáltató |
> | Művelet | Microsoft.ApiManagement/service/locations/networkstatus/read | Lekérdezi a hálózati hozzáférési állapota az erőforrások, amelyen a szolgáltatás függ a helyen. |
> | Művelet | Microsoft.ApiManagement/service/loggers/delete | Távolítsa el a meglévő naplózó |
> | Művelet | Microsoft.ApiManagement/service/loggers/read | Figyelő szoftverek listája vagy az naplózó részleteinek beolvasása |
> | Művelet | Microsoft.ApiManagement/service/loggers/write | Adja hozzá az új naplózó vagy meglévő naplózó részleteinek frissítése |
> | Művelet | Microsoft.ApiManagement/service/managedeployments/action | Módosítsa a Termékváltozat jegyek, hozzáadása regionális egy szolgáltatás üzemelő példányainak API Management |
> | Művelet | Microsoft.ApiManagement/service/networkstatus/read | Lekérdezi a hálózati hozzáférési állapota az erőforrások, amelyen a szolgáltatás függ. |
> | Művelet | Microsoft.ApiManagement/service/notifications/action | Értesítést küld egy adott felhasználó |
> | Művelet | Microsoft.ApiManagement/service/notifications/read | Lekérdezi az összes API Management publisher értesítések vagy beszerzése API Management publisher értesítési részletei |
> | Művelet | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | Eltávolítja a meglévő E-mail értesítést társított |
> | Művelet | Microsoft.ApiManagement/service/notifications/recipientEmails/read | Társított API Management Publisher értesítési e-mailek címzettjeinek beolvasása |
> | Művelet | Microsoft.ApiManagement/service/notifications/recipientEmails/write | Hozzon létre új E-mail címzettet az értesítés |
> | Művelet | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | Az értesítési címzettek társított felhasználó eltávolítása |
> | Művelet | Microsoft.ApiManagement/service/notifications/recipientUsers/read | Az értesítés társított címzett felhasználók |
> | Művelet | Microsoft.ApiManagement/service/notifications/recipientUsers/write | Felhasználó hozzáadása az értesítési címzettek |
> | Művelet | Microsoft.ApiManagement/service/notifications/write | Hozzon létre vagy az Update API Management publisher értesítés |
> | Művelet | Microsoft.ApiManagement/service/openidConnectProviders/delete | Távolítsa el a meglévő OpenID Connect szolgáltató |
> | Művelet | Microsoft.ApiManagement/service/openidConnectProviders/read | Szerezze be az OpenID Connect-szolgáltatókkal vagy az OpenID Connect szolgáltató Get részleteit listát |
> | Művelet | Microsoft.ApiManagement/service/openidConnectProviders/write | Hozzon létre egy új OpenID Connect szolgáltató vagy a frissítés adatait egy meglévő OpenID Connect-szolgáltató |
> | Művelet | Microsoft.ApiManagement/service/operationresults/read | Hosszú ideig futó művelet aktuális állapotának beolvasása |
> | Művelet | Microsoft.ApiManagement/service/policies/delete | Távolítsa el a házirend-konfigurációt a bérlő házirendek |
> | Művelet | Microsoft.ApiManagement/service/policies/read | Házirendek bérlő bérlői vagy Get házirend konfigurációs részletek lekérése |
> | Művelet | Microsoft.ApiManagement/service/policies/write | Állítsa be a házirend-konfiguráció részletei bérlő számára |
> | Művelet | Microsoft.ApiManagement/service/policySnippets/read | Az összes házirend kódtöredékek beolvasása |
> | Művelet | Microsoft.ApiManagement/service/portalsettings/read | Bejelentkezési beállítások beszerezni a portál vagy a Get jelentkezzen be a portál beállításait, vagy a portál delegálási beállítások beolvasása |
> | Művelet | Microsoft.ApiManagement/service/portalsettings/write | Regisztráció beállítások vagy frissítés regisztráció beállítások vagy frissítés bejelentkezés beállítások vagy frissítés bejelentkezés beállítások vagy frissítés delegálási beállításokat vagy frissítés delegálási beállítások frissítése |
> | Művelet | Microsoft.ApiManagement/service/products/apis/delete | Távolítsa el a meglévő API a meglévő termék |
> | Művelet | Microsoft.ApiManagement/service/products/apis/read | Szerezze be az API-k meglévő termékhez hozzáadott listát |
> | Művelet | Microsoft.ApiManagement/service/products/apis/write | Meglévő API hozzáadása meglévő termék |
> | Művelet | Microsoft.ApiManagement/service/products/delete | Távolítsa el a meglévő termék |
> | Művelet | Microsoft.ApiManagement/service/products/groups/delete | Meglévő termék társítás meglévő fejlesztői csoport törlése |
> | Művelet | Microsoft.ApiManagement/service/products/groups/read | Termék társított fejlesztői csoportok listájának beolvasása |
> | Művelet | Microsoft.ApiManagement/service/products/groups/write | Meglévő fejlesztői csoport társítása meglévő termék |
> | Művelet | Microsoft.ApiManagement/service/products/policies/delete | Távolítsa el a házirend-konfigurációt a termék házirendek |
> | Művelet | Microsoft.ApiManagement/service/products/policies/read | A termék vagy a Get házirend konfigurációs részletek termék házirendek lekérése |
> | Művelet | Microsoft.ApiManagement/service/products/policies/write | Állítsa be a házirend-konfiguráció részletei termékhez |
> | Művelet | Microsoft.ApiManagement/service/products/policy/delete | Távolítsa el a házirend-konfigurációt a meglévő termék |
> | Művelet | Microsoft.ApiManagement/service/products/policy/read | Házirend-konfigurációt meglévő termék beszerzése |
> | Művelet | Microsoft.ApiManagement/service/products/policy/write | A termék meglévő házirend-konfiguráció |
> | Művelet | Microsoft.ApiManagement/service/products/read | Termékek listáját, vagy a részletek a termék |
> | Művelet | Microsoft.ApiManagement/service/products/subscriptions/read | Termék előfizetések listájának beolvasása |
> | Művelet | Microsoft.ApiManagement/service/products/tags/delete | Meglévő címke meglévő termékkel társítás törlése |
> | Művelet | Microsoft.ApiManagement/service/products/tags/read | A termék vagy az beszerzése címke részletek társított címkék beolvasása |
> | Művelet | Microsoft.ApiManagement/service/products/tags/write | Meglévő címke társítása meglévő termék |
> | Művelet | Microsoft.ApiManagement/service/products/write | Új termék létrehozása vagy meglévő termékadatok frissítése |
> | Művelet | Microsoft.ApiManagement/service/properties/delete | Eltávolítja a meglévő tulajdonság |
> | Művelet | Microsoft.ApiManagement/service/properties/read | Beolvassa az összes tulajdonság listáját, és lekérdezi a megadott tulajdonság részleteit |
> | Művelet | Microsoft.ApiManagement/service/properties/write | Új tulajdonság létrehozása vagy frissítése a megadott tulajdonság értéke |
> | Művelet | Microsoft.ApiManagement/service/providers/Microsoft.Insights/diagnosticSettings/read | Az API Management szolgáltatás diagnosztikai beállításának beolvasása. |
> | Művelet | Microsoft.ApiManagement/service/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti az API-kezelés szolgáltatás a diagnosztikai beállítása |
> | Művelet | Microsoft.ApiManagement/service/providers/Microsoft.Insights/logDefinitions/read | Az API Management szolgáltatás naplók beolvasása. |
> | Művelet | Microsoft.ApiManagement/service/providers/Microsoft.Insights/metricDefinitions/read | Az API Management szolgáltatáshoz elérhető metrikai meghatározások beolvasása |
> | Művelet | Microsoft.ApiManagement/service/quotas/periods/read | Kvóta számlálóérték beolvasása időszak |
> | Művelet | Microsoft.ApiManagement/service/quotas/periods/write | Kvóta számláló aktuális érték |
> | Művelet | Microsoft.ApiManagement/service/quotas/read | Értékek lekérése kvóta |
> | Művelet | Microsoft.ApiManagement/service/quotas/write | Kvóta számláló aktuális érték |
> | Művelet | Microsoft.ApiManagement/service/read | Olvassa el a metaadatokat az API Management szolgáltatáspéldány |
> | Művelet | Microsoft.ApiManagement/service/reports/read | Összesítve időszakra vagy a földrajzi régió vagy fejlesztők összesítve Get jelentés összesítve Get jelentés jelentés lekérése.<br>vagy a jelentés megtekintése a termékek alapján összesíti.<br>vagy a műveletek vagy Get jelentés-előfizetés összesítve összesítve API-k vagy Get jelentés összesítve jelentés megtekintése.<br>vagy a jelentés adatainak kérelmek |
> | Művelet | Microsoft.ApiManagement/service/restore/action | Állítsa vissza egy felhasználó által megadott tárfiók a megadott tároló API Management szolgáltatás |
> | Művelet | Microsoft.ApiManagement/service/subscriptions/delete | Előfizetés törlése. Ez a művelet segítségével előfizetés törlése |
> | Művelet | Microsoft.ApiManagement/service/subscriptions/read | A termék előfizetések listáját, vagy a részletek a termék előfizetés |
> | Művelet | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | Előfizetés elsődleges kulcs újragenerálása |
> | Művelet | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | Előfizetés másodlagos kulcs újragenerálása |
> | Művelet | Microsoft.ApiManagement/service/subscriptions/write | Egy meglévő felhasználó számára a meglévő termék előfizetés, vagy frissítse a meglévő előfizetés részletei. Ez a művelet használható előfizetés megújításához |
> | Művelet | Microsoft.ApiManagement/service/tagResources/read | Társított erőforrásokkal rendelkező címkék listájának beolvasása |
> | Művelet | Microsoft.ApiManagement/service/tags/delete | Meglévő címke eltávolítása |
> | Művelet | Microsoft.ApiManagement/service/tags/read | Címkék listáját és a részletek a címke |
> | Művelet | Microsoft.ApiManagement/service/tags/write | Új címke hozzáadása vagy meglévő címke részleteinek frissítése |
> | Művelet | Microsoft.ApiManagement/service/templates/delete | Alapértelmezett API Management e-mail sablon alaphelyzetbe állítása |
> | Művelet | Microsoft.ApiManagement/service/templates/read | Lekérdezi az összes e-mail-sablonokkal vagy lekérdezi az API Management e-mail sablon részletei |
> | Művelet | Microsoft.ApiManagement/service/templates/write | Létrehozni vagy frissíteni az API Management e-mail sablon vagy frissíti az API Management e-mail sablon |
> | Művelet | Microsoft.ApiManagement/service/tenant/delete | A bérlőhöz tartozó házirend-konfiguráció eltávolítása |
> | Művelet | Microsoft.ApiManagement/service/tenant/deploy/action | A megadott git fiókirodai módosítások alkalmazása a konfigurációs adatbázis telepítési feladat fut. |
> | Művelet | Microsoft.ApiManagement/service/tenant/operationResults/read | A művelet eredménye listája vagy az beszerzése az adott művelet eredménye |
> | Művelet | Microsoft.ApiManagement/service/tenant/read | Házirend-konfiguráció beolvasása a bérlő vagy a Get-bérlő hozzáférés adatai |
> | Művelet | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | Elsődleges elérési kulcs újragenerálása |
> | Művelet | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | Másodlagos elérési kulcs újragenerálása |
> | Művelet | Microsoft.ApiManagement/service/tenant/save/action | A tárházban található a megadott ágra konfigurációs pillanatképet hoz létre a véglegesítési |
> | Művelet | Microsoft.ApiManagement/service/tenant/syncState/read | Az utolsó git szinkronizálás állapotának beolvasása |
> | Művelet | Microsoft.ApiManagement/service/tenant/validate/action | Ellenőrzi a megadott git fiókirodai módosítások |
> | Művelet | Microsoft.ApiManagement/service/tenant/write | A bérlő vagy az Update bérlői hozzáférés adatai házirend-konfiguráció |
> | Művelet | Microsoft.ApiManagement/service/updatecertificate/action | Az API Management szolgáltatás SSL-tanúsítvány feltöltése |
> | Művelet | Microsoft.ApiManagement/service/updatehostname/action | A telepítő, frissítésére, vagy távolítsa el az API Management szolgáltatás egyéni tartománynevek |
> | Művelet | Microsoft.ApiManagement/service/users/action | Új felhasználó regisztrálása |
> | Művelet | Microsoft.ApiManagement/service/users/applications/attachments/delete | Eltávolít egy melléklet |
> | Művelet | Microsoft.ApiManagement/service/users/applications/attachments/read | Lekérdezi az alkalmazás mellékletek vagy lekérdezi melléklet |
> | Művelet | Microsoft.ApiManagement/service/users/applications/attachments/write | Melléklet alkalmazás hozzáadása |
> | Művelet | Microsoft.ApiManagement/service/users/applications/delete | Eltávolítja a meglévő alkalmazáshoz |
> | Művelet | Microsoft.ApiManagement/service/users/applications/read | Minden felhasználó az alkalmazások és az API Management lekérdezi az alkalmazás részleteinek listájának |
> | Művelet | Microsoft.ApiManagement/service/users/applications/write | Az API Management és a frissítéseket az alkalmazás részleteinek alkalmazás regisztrálása |
> | Művelet | Microsoft.ApiManagement/service/users/delete | Felhasználói fiók eltávolítása |
> | Művelet | Microsoft.ApiManagement/service/users/generateSsoUrl/action | Egyszeri bejelentkezési URL-címet létrehozni. Az URL-cím használható felügyeleti portál eléréséhez |
> | Művelet | Microsoft.ApiManagement/service/users/groups/read | Felhasználói csoportok listájának beolvasása |
> | Művelet | Microsoft.ApiManagement/service/users/keys/read | Felhasználói kulcsok listájának beolvasása |
> | Művelet | Microsoft.ApiManagement/service/users/read | A regisztrált felhasználók listáját, vagy felhasználói fiók adatainak lekérése |
> | Művelet | Microsoft.ApiManagement/service/users/subscriptions/read | Felhasználói előfizetések listájának beolvasása |
> | Művelet | Microsoft.ApiManagement/service/users/token/action | Hozzáférés a jogkivonatokhoz-token beszerzése a felhasználó számára |
> | Művelet | Microsoft.ApiManagement/service/users/write | Új felhasználó vagy egy meglévő felhasználó frissítés fiókadatok regisztrálása |
> | Művelet | Microsoft.ApiManagement/service/write | Az API Management szolgáltatást egy új példányának létrehozása |
> | Művelet | Microsoft.ApiManagement/unregister/action | Előfizetés regisztrációjának Microsoft.ApiManagement erőforrás-szolgáltató |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.Authorization/checkAccess/action | Ellenőrzi, hogy a hívó jogosult-e egy adott művelet végrehajtására |
> | Művelet | Microsoft.Authorization/classicAdministrators/delete | Rendszergazda eltávolítása az előfizetésből. |
> | Művelet | Microsoft.Authorization/classicAdministrators/read | Az előfizetés rendszergazdáinak beolvasása. |
> | Művelet | Microsoft.Authorization/classicAdministrators/write | Előfizetés rendszergazdájának hozzáadása vagy módosítása. |
> | Művelet | Microsoft.Authorization/elevateAccess/action | Felhasználói hozzáférés rendszergazdai szerepkörének megadása a hívónak a bérlői hatókörben |
> | Művelet | Microsoft.Authorization/locks/delete | A megadott hatókör zárolásainak törlése. |
> | Művelet | Microsoft.Authorization/locks/read | A megadott hatókör zárolásainak beolvasása. |
> | Művelet | Microsoft.Authorization/locks/write | Zárolások hozzáadása a megadott hatókörben. |
> | Művelet | Microsoft.Authorization/permissions/read | A hívó adott tartományban meglévő összes engedélyének listázása. |
> | Művelet | Microsoft.Authorization/policyAssignments/delete | Szabályzat-hozzárendelés törlése a megadott hatókörből. |
> | Művelet | Microsoft.Authorization/policyAssignments/read | Szabályzat-hozzárendelés adatainak beolvasása. |
> | Művelet | Microsoft.Authorization/policyAssignments/write | Szabályzat-hozzárendelés létrehozása a megadott hatókörben. |
> | Művelet | Microsoft.Authorization/policyDefinitions/delete | Szabályzat-definíció törlése. |
> | Művelet | Microsoft.Authorization/policyDefinitions/read | Szabályzatdefiníció adatainak beolvasása. |
> | Művelet | Microsoft.Authorization/policyDefinitions/write | Egyéni szabályzat-definíció létrehozása. |
> | Művelet | Microsoft.Authorization/policySetDefinitions/delete | Szabályzatkészlet-definíció törlése. |
> | Művelet | Microsoft.Authorization/policySetDefinitions/read | Egy szabályzatkészlet-definíció adatainak beolvasása. |
> | Művelet | Microsoft.Authorization/policySetDefinitions/write | Egyéni szabályzatkészlet-definíció létrehozása. |
> | Művelet | Microsoft.Authorization/providerOperations/read | Az összes, szerepkör-definíciókban használható erőforrás-szolgáltató műveleteinek beolvasása. |
> | Művelet | Microsoft.Authorization/roleAssignments/delete | Szerepkör-hozzárendelés törlése a megadott hatókörből. |
> | Művelet | Microsoft.Authorization/roleAssignments/read | Információk beolvasása a szerepkör-hozzárendelésről. |
> | Művelet | Microsoft.Authorization/roleAssignments/write | Szerepkör-hozzárendelés létrehozása a megadott hatókörben. |
> | Művelet | Microsoft.Authorization/roleDefinitions/delete | A megadott egyéni szerepkör-definíció törlése. |
> | Művelet | Microsoft.Authorization/roleDefinitions/read | Szerepkör-definíció adatainak beolvasása. |
> | Művelet | Microsoft.Authorization/roleDefinitions/write | Egy egyéni szerepkör-definíció létrehozása vagy módosítása a megadott engedélyekkel és hozzárendelhető hatókörökkel. |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | Olvassa el a regisztrációs adatokat egy Azure Automation DSC |
> | Művelet | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | Azure Automation DSC kulcsok újragenerálása kérelem írási műveletek |
> | Művelet | Microsoft.Automation/automationAccounts/certificates/delete | Egy Azure Automation szolgáltatásbeli tanúsítványeszköz törlése |
> | Művelet | Microsoft.Automation/automationAccounts/certificates/getCount/action | Olvassa be a tanúsítványok száma |
> | Művelet | Microsoft.Automation/automationAccounts/certificates/read | Egy Azure Automation szolgáltatásbeli tanúsítványeszköz beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/certificates/write | Létrehozza vagy frissíti az Azure Automation szolgáltatásbeli tanúsítványeszköz |
> | Művelet | Microsoft.Automation/automationAccounts/compilationjobs/read | Egy Azure Automation DSC-fordítási beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/compilationjobs/read | Egy Azure Automation DSC-fordítási beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/compilationjobs/write | Egy Azure Automation DSC-fordítási írási műveletek |
> | Művelet | Microsoft.Automation/automationAccounts/compilationjobs/write | Egy Azure Automation DSC-fordítási írási műveletek |
> | Művelet | Microsoft.Automation/automationAccounts/configurations/content/read | A konfigurációs hordozó tartalmának beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/configurations/delete | Egy Azure Automation DSC tartalmának törlése |
> | Művelet | Microsoft.Automation/automationAccounts/configurations/getCount/action | A szám egy Azure Automation DSC tartalmának beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/configurations/read | Egy Azure Automation DSC tartalmának beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/configurations/write | Egy Azure Automation DSC tartalmának írása |
> | Művelet | Microsoft.Automation/automationAccounts/connections/delete | Azure Automation szolgáltatásbeli kapcsolódási eszköz törlése |
> | Művelet | Microsoft.Automation/automationAccounts/connections/getCount/action | Beolvassa a kapcsolatok száma |
> | Művelet | Microsoft.Automation/automationAccounts/connections/read | Azure Automation szolgáltatásbeli kapcsolódási eszköz beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/connections/write | Létrehozza vagy frissíti az Azure Automation szolgáltatásbeli kapcsolódási eszköz |
> | Művelet | Microsoft.Automation/automationAccounts/connectionTypes/delete | Egy Azure Automation szolgáltatásbeli kapcsolattípus-eszköz törlése |
> | Művelet | Microsoft.Automation/automationAccounts/connectionTypes/read | Egy Azure Automation szolgáltatásbeli kapcsolattípus-eszköz beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/connectionTypes/write | Egy Azure Automation szolgáltatásbeli kapcsolattípus-eszköz létrehozása |
> | Művelet | Microsoft.Automation/automationAccounts/credentials/delete | Egy Azure Automation szolgáltatásbeli hitelesítőadat-eszköz törlése |
> | Művelet | Microsoft.Automation/automationAccounts/credentials/getCount/action | Olvassa be a hitelesítő adatok száma |
> | Művelet | Microsoft.Automation/automationAccounts/credentials/read | Egy Azure Automation szolgáltatásbeli hitelesítőadat-eszköz beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/credentials/write | Létrehozza vagy frissíti egy Azure Automation szolgáltatásbeli hitelesítőadat-eszköz |
> | Művelet | Microsoft.Automation/automationAccounts/delete | Egy Azure Automation-fiók törlése |
> | Művelet | Microsoft.Automation/automationAccounts/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/diagnosticSettings/write | Az erőforrás diagnosztikai beállításának |
> | Művelet | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | Hibrid forgatókönyv-feldolgozó erőforrások törlése |
> | Művelet | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Beolvassa a hibrid forgatókönyv-feldolgozó erőforrások |
> | Művelet | Microsoft.Automation/automationAccounts/jobs/output/read | Egy feladat kimenetének beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/jobs/read | Egy Azure Automation-feladat beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/jobs/resume/action | Egy Azure Automation-feladat folytatása |
> | Művelet | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | A feladat végrehajtásának időpontjában az Azure Automation-runbook tartalmának beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/jobs/stop/action | Egy Azure Automation-feladat leállítása |
> | Művelet | Microsoft.Automation/automationAccounts/jobs/streams/read | Egy Azure Automation-feladatstream beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/jobs/streams/read | Egy Azure Automation-feladatstream beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/jobs/suspend/action | Egy Azure Automation-feladat felfüggesztése |
> | Művelet | Microsoft.Automation/automationAccounts/jobs/write | Egy Azure Automation-feladat létrehozása |
> | Művelet | Microsoft.Automation/automationAccounts/jobSchedules/delete | Egy Azure Automation-feladatütemezés törlése |
> | Művelet | Microsoft.Automation/automationAccounts/jobSchedules/read | Egy Azure Automation-feladatütemezés beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/jobSchedules/write | Egy Azure Automation-feladatütemezés létrehozása |
> | Művelet | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Lekérdezi a munkaterület az automation-fiókhoz csatolva. |
> | Művelet | Microsoft.Automation/automationAccounts/listKeys/action | Beolvassa a kulcsokat az automation-fiók |
> | Művelet | Microsoft.Automation/automationAccounts/logDefinitions/read | Az automatizálási fiók naplók beolvasása. |
> | Művelet | Microsoft.Automation/automationAccounts/modules/activities/read | Lekérdezi az Azure Automation-tevékenységek |
> | Művelet | Microsoft.Automation/automationAccounts/modules/delete | Egy Azure Automation-modul törlése |
> | Művelet | Microsoft.Automation/automationAccounts/modules/getCount/action | Lekérdezi az Automation-fiók modulok száma |
> | Művelet | Microsoft.Automation/automationAccounts/modules/read | Egy Azure Automation-modul beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/modules/write | Létrehozza vagy frissíti az Azure Automation-modul |
> | Művelet | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | Egy Azure Automation DSC-csomópont-konfiguráció törlése |
> | Művelet | Microsoft.Automation/automationAccounts/nodeConfigurations/rawContent/action | Egy Azure Automation DSC-csomópont konfigurációjának tartalma beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/nodeConfigurations/read | Egy Azure Automation DSC-csomópont-konfiguráció beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/nodeConfigurations/write | Ír egy Azure Automation DSC-csomópont-konfiguráció |
> | Művelet | Microsoft.Automation/automationAccounts/nodecounts/read | Beolvassa a megadott típus összesített száma |
> | Művelet | Microsoft.Automation/automationAccounts/nodes/delete | Azure Automation DSC-csomópontok törlése |
> | Művelet | Microsoft.Automation/automationAccounts/nodes/read | Azure Automation DSC-csomópontok beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/nodes/reports/content/read | Azure Automation DSC-jelentés tartalmának beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/nodes/reports/read | Azure Automation DSC-jelentéseket beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/nodes/write | Létrehozza vagy frissíti az Azure Automation DSC-csomópontok |
> | Művelet | Microsoft.Automation/automationAccounts/objectDataTypes/fields/read | Azure Automation szolgáltatásbeli TypeFields beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/providers/Microsoft.Insights/metricDefinitions/read | Automatizálási metrikai meghatározásainak beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/read | Egy Azure Automation-fiók beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/runbooks/content/read | Egy Azure Automation-runbook tartalmának beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/runbooks/delete | Egy Azure Automation-runbook törlése |
> | Művelet | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | Egy Azure Automation-runbookvázlat tartalmának létrehozása |
> | Művelet | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | Azure Automation-runbook beolvasása vázlat a művelet eredménye |
> | Művelet | Microsoft.Automation/automationAccounts/runbooks/draft/publish/action | Egy Azure Automation-runbookvázlat közzététele |
> | Művelet | Microsoft.Automation/automationAccounts/runbooks/draft/read | Egy Azure Automation-runbookvázlat beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | Egy Azure Automation runbookvázlat-tesztelési feladat beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | Egy Azure Automation runbookvázlat-tesztelési feladat folytatása |
> | Művelet | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/stop/action | Egy Azure Automation runbookvázlat-tesztelési feladat leállítása |
> | Művelet | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | Egy Azure Automation runbookvázlat-tesztelési feladat felfüggesztése |
> | Művelet | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | Egy Azure Automation runbookvázlat-tesztelési feladat létrehozása |
> | Művelet | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | Egy Azure Automation-runbookvázlat szerkesztésének visszavonása |
> | Művelet | Microsoft.Automation/automationAccounts/runbooks/getCount/action | Azure Automation-runbook számának beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/runbooks/read | Egy Azure Automation-runbook beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/runbooks/write | Létrehozza vagy frissíti az Azure Automation-runbook |
> | Művelet | Microsoft.Automation/automationAccounts/schedules/delete | Egy Azure Automation szolgáltatásbeli ütemezéseszköz törlése |
> | Művelet | Microsoft.Automation/automationAccounts/schedules/getCount/action | Lekérdezi az Azure Automation ütemezések száma |
> | Művelet | Microsoft.Automation/automationAccounts/schedules/read | Egy Azure Automation szolgáltatásbeli ütemezési eszköz beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/schedules/write | Létrehozza vagy frissíti az Azure Automation szolgáltatásbeli ütemezési eszköz |
> | Művelet | Microsoft.Automation/automationAccounts/statistics/read | Azure Automation szolgáltatásbeli statisztika beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | Egy Azure Automation-frissítés központi telepítési gép beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | Egy Azure Automation frissítés felügyeleti javítás feladat beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/usages/read | Lekérdezi az Azure Automation-használat |
> | Művelet | Microsoft.Automation/automationAccounts/variables/delete | Egy Azure Automation szolgáltatásbeli változóeszköz törlése |
> | Művelet | Microsoft.Automation/automationAccounts/variables/read | Egy Azure Automation szolgáltatásbeli változóeszköz beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/variables/write | Létrehozza vagy frissíti egy Azure Automation szolgáltatásbeli változóeszköz |
> | Művelet | Microsoft.Automation/automationAccounts/watchers/delete | Egy Azure Automation-figyelő feladat törlése |
> | Művelet | Microsoft.Automation/automationAccounts/watchers/read | Egy Azure Automation-figyelő feladat beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/watchers/start/action | Egy Azure Automation-figyelő feladat indítása |
> | Művelet | Microsoft.Automation/automationAccounts/watchers/stop/action | Egy Azure Automation-figyelő feladat leállítása |
> | Művelet | Microsoft.Automation/automationAccounts/watchers/streams/read | Egy Azure Automation-megfigyelő feladatstream beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/watchers/watcherActions/delete | Egy Azure Automation-figyelő feladat műveletek törlése |
> | Művelet | Microsoft.Automation/automationAccounts/watchers/watcherActions/read | Lekérdezi az Azure Automation-figyelőjét Feladatműveletek |
> | Művelet | Microsoft.Automation/automationAccounts/watchers/watcherActions/write | Hozzon létre egy Azure Automation-figyelő feladat műveletek |
> | Művelet | Microsoft.Automation/automationAccounts/watchers/write | Létrehoz egy Azure Automation-figyelő feladat |
> | Művelet | Microsoft.Automation/automationAccounts/webhooks/action | URI generálása Azure Automation-webhook |
> | Művelet | Microsoft.Automation/automationAccounts/webhooks/delete | Egy Azure Automation-webhook törlése  |
> | Művelet | Microsoft.Automation/automationAccounts/webhooks/read | Egy Azure Automation-webhook beolvasása |
> | Művelet | Microsoft.Automation/automationAccounts/webhooks/write | Létrehozza vagy frissíti az Azure Automation-webhook |
> | Művelet | Microsoft.Automation/automationAccounts/write | Létrehozza vagy frissíti az Azure Automation-fiók |
> | Művelet | Microsoft.Automation/operations/read | Azure Automation-erőforrások elérhető műveletek beolvasása |
> | Művelet | Microsoft.Automation/register/action | Az Azure Automation előfizetés regisztrálása |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.AzureActiveDirectory/b2cDirectories/delete | B2C-címtárerőforrás törlése |
> | Művelet | Microsoft.AzureActiveDirectory/b2cDirectories/read | B2C-címtárerőforrás megtekintése |
> | Művelet | Microsoft.AzureActiveDirectory/b2cDirectories/write | B2C-címtárerőforrás létrehozása vagy frissítése |
> | Művelet | Microsoft.AzureActiveDirectory/operations/read | A Microsoft.AzureActiveDirectory erőforrás-szolgáltatóhoz választható összes API-művelet olvasása |
> | Művelet | Microsoft.AzureActiveDirectory/register/action | A Microsoft.AzureActiveDirectory erőforrás-szolgáltatóhoz tartozó előfizetés regisztrálása |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.AzureStack/Operations/read | Egy erőforrás-szolgáltató művelet tulajdonságait olvassa be |
> | Művelet | Microsoft.AzureStack/register/action | Az előfizetés regisztrálása a Microsoft.AzureStack erőforrás-szolgáltató |
> | Művelet | Microsoft.AzureStack/registrations/customerSubscriptions/delete | Egy Azure verem ügyfél-előfizetést törlése |
> | Művelet | Microsoft.AzureStack/registrations/customerSubscriptions/read | Az Azure-verem felhasználói előfizetés tulajdonságainak beolvasása |
> | Művelet | Microsoft.AzureStack/registrations/customerSubscriptions/write | Létrehozza vagy frissíti az Azure-verem felhasználói előfizetéssel |
> | Művelet | Microsoft.AzureStack/registrations/delete | Egy Azure verem regisztráció törlése |
> | Művelet | Microsoft.AzureStack/registrations/getActivationKey/action | A legújabb Azure verem használható aktiválási kulcs beolvasása |
> | Művelet | Microsoft.AzureStack/registrations/products/listDetails/action | Lekéri a kiterjesztett egy Azure verem piactér megoldás részletei |
> | Művelet | Microsoft.AzureStack/registrations/products/read | Egy Azure verem piactér termék tulajdonságainak beolvasása |
> | Művelet | Microsoft.AzureStack/registrations/read | Egy Azure verem regisztrációs tulajdonságait olvassa be |
> | Művelet | Microsoft.AzureStack/registrations/write | Létrehozza vagy frissíti az Azure-verem regisztrációs |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.Batch/batchAccounts/applications/delete | Az alkalmazás törlése |
> | Művelet | Microsoft.Batch/batchAccounts/applications/read | Megjeleníti azokat alkalmazásokat, vagy az alkalmazás tulajdonságainak beolvasása |
> | Művelet | Microsoft.Batch/batchAccounts/applications/versions/activate/action | Az alkalmazáscsomag aktiválása |
> | Művelet | Microsoft.Batch/batchAccounts/applications/versions/delete | Az alkalmazáscsomag törlése |
> | Művelet | Microsoft.Batch/batchAccounts/applications/versions/read | Alkalmazáscsomag tulajdonságait olvassa be |
> | Művelet | Microsoft.Batch/batchAccounts/applications/versions/write | Létrehoz egy új alkalmazás-csomagot, vagy frissíti a meglévő alkalmazáscsomag |
> | Művelet | Microsoft.Batch/batchAccounts/applications/write | Létrehoz egy új alkalmazást, vagy egy meglévő alkalmazás frissítése |
> | Művelet | Microsoft.Batch/batchAccounts/certificateOperationResults/read | Batch-fiók tanúsítvány egy hosszú ideig futó művelet eredményének beolvasása |
> | Művelet | Microsoft.Batch/batchAccounts/certificates/cancelDelete/action | A tanúsítvány a Batch-fiók törlésére tett sikertelen kísérlet megszakítása |
> | Művelet | Microsoft.Batch/batchAccounts/certificates/delete | Töröl egy tanúsítványt a Batch-fiókból |
> | Művelet | Microsoft.Batch/batchAccounts/certificates/read | A Batch-fiók-tanúsítványokat sorolja fel, vagy a tanúsítvány tulajdonságainak beolvasása |
> | Művelet | Microsoft.Batch/batchAccounts/certificates/write | Létrehoz egy új tanúsítványt a Batch-fiók, vagy egy meglévő tanúsítvány frissítése |
> | Művelet | Microsoft.Batch/batchAccounts/delete | Batch-fiók törlése |
> | Művelet | Microsoft.Batch/batchAccounts/listkeys/action | Listák hívóbetűk Batch-fiókhoz |
> | Művelet | Microsoft.Batch/batchAccounts/operationResults/read | A Batch-fiók hosszú ideig futó művelet eredményének beolvasása |
> | Művelet | Microsoft.Batch/batchAccounts/poolOperationResults/read | Batch-fiók készlet egy hosszú ideig futó művelet eredményének beolvasása |
> | Művelet | Microsoft.Batch/batchAccounts/pools/delete | Egy készlet törlése Batch-fiók |
> | Művelet | Microsoft.Batch/batchAccounts/pools/disableAutoscale/action | A Batch-fiók készlet automatikus skálázás letiltása |
> | Művelet | Microsoft.Batch/batchAccounts/pools/read | A Batch-fiók készletek sorolja fel, vagy a készlet tulajdonságait olvassa be |
> | Művelet | Microsoft.Batch/batchAccounts/pools/stopResize/action | Leállítja egy folyamatban lévő műveletet a Batch-fiók készlet átméretezése |
> | Művelet | Microsoft.Batch/batchAccounts/pools/upgradeOs/action | Frissíti az operációs rendszert, a Batch-fiók címkészlet |
> | Művelet | Microsoft.Batch/batchAccounts/pools/write | Új készlet létrehozása a Batch-fiók, vagy egy meglévő készlet frissítése |
> | Művelet | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Művelet | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Művelet | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/logDefinitions/read | A Batch szolgáltatás naplók beolvasása. |
> | Művelet | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/metricDefinitions/read | A Batch szolgáltatás elérhető metrikai meghatározások beolvasása |
> | Művelet | Microsoft.Batch/batchAccounts/read | Batch-fiókok listája, vagy a Batch-fiók tulajdonságait olvassa be |
> | Művelet | Microsoft.Batch/batchAccounts/regeneratekeys/action | A Batch-fiókhoz hívóbetűk újragenerálása |
> | Művelet | Microsoft.Batch/batchAccounts/syncAutoStorageKeys/action | Szinkronizálja a Batch-fiók beállítása automatikus tárfiók hozzáférési kulcsainak listázása |
> | Művelet | Microsoft.Batch/batchAccounts/write | Új Batch-fiók létrehozása vagy frissítése egy meglévő Batch-fiók |
> | Művelet | Microsoft.Batch/locations/checkNameAvailability/action | Ellenőrzi, hogy a fiók neve érvényes, és nincs használatban. |
> | Művelet | Microsoft.Batch/locations/quotas/read | A megadott Azure-régiót kötegelt a megadott előfizetés kvótájának beolvasása |
> | Művelet | Microsoft.Batch/register/action | A kötegelt erőforrás-szolgáltató előfizetésének regisztrálása, és lehetővé teszi, hogy a Batch-fiókok létrehozása |
> | Művelet | Microsoft.Batch/unregister/action | A Batch-fiókok megelőzése kötegelt erőforrás-szolgáltató az előfizetés regisztrációjának törlése |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.BatchAI/clusters/delete | A kötegelt AI fürt törlése |
> | Művelet | Microsoft.BatchAI/clusters/read | Felsorolja a kötegelt AI fürtök vagy egy kötegelt AI fürt tulajdonságait olvassa be |
> | Művelet | Microsoft.BatchAI/clusters/remoteLoginInformation/action | Távoli-bejelentkezési adatok kötegelt AI fürt listája |
> | Művelet | Microsoft.BatchAI/clusters/write | Egy új kötegelt AI fürtöt hoz létre, vagy kötegelt AI meglévő fürt frissítése |
> | Művelet | Microsoft.BatchAI/fileservers/delete | A kötegelt AI fájlkiszolgáló törlése |
> | Művelet | Microsoft.BatchAI/fileservers/read | Kötegelt AI fileservers sorolja fel, vagy egy kötegelt AI fájlkiszolgálóhoz tulajdonságait olvassa be |
> | Művelet | Microsoft.BatchAI/fileservers/resume/action | A kötegelt AI fájlkiszolgáló folytatása |
> | Művelet | Microsoft.BatchAI/fileservers/suspend/action | A kötegelt AI fájlkiszolgáló felfüggesztése |
> | Művelet | Microsoft.BatchAI/fileservers/write | Egy új kötegelt AI fájlkiszolgálóhoz létrehoz vagy frissít egy meglévő kötegelt AI fájlkiszolgáló |
> | Művelet | Microsoft.BatchAI/jobs/delete | A kötegelt AI feladatok törlése |
> | Művelet | Microsoft.BatchAI/jobs/read | Felsorolja a kötegelt AI feladatokat, vagy egy kötegelt AI feladat tulajdonságait olvassa be |
> | Művelet | Microsoft.BatchAI/jobs/remoteLoginInformation/action | Távoli-bejelentkezési adatok kötegelt AI feladat listája |
> | Művelet | Microsoft.BatchAI/jobs/terminate/action | A kötegelt AI feladat leállítása |
> | Művelet | Microsoft.BatchAI/jobs/write | Létrehoz egy új kötegelt AI feladatot, vagy egy meglévő kötegelt AI feladat frissítése |
> | Művelet | Microsoft.BatchAI/register/action | A kötegelt AI erőforrás-szolgáltató előfizetésének regisztrálása és létrehozását lehetővé tevő kötegelt AI erőforrások |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.Billing/billingPeriods/read | Megjeleníti az elérhető elszámolási időszakok |
> | Művelet | Microsoft.Billing/invoices/read | Rendelkezésre álló listák számlák |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.BingMaps/mapApis/Delete | Törlési művelet |
> | Művelet | Microsoft.BingMaps/mapApis/listSecrets/action | A titkos kulcsok listázása |
> | Művelet | Microsoft.BingMaps/mapApis/listSingleSignOnToken/action | Az erőforráshoz tartozó egyszeri bejelentkezési engedélyezési jogkivonat beolvasása |
> | Művelet | Microsoft.BingMaps/mapApis/Read | Olvasási művelet |
> | Művelet | Microsoft.BingMaps/mapApis/regenerateKey/action | A kulcs újragenerálása |
> | Művelet | Microsoft.BingMaps/mapApis/Write | Írási művelet |
> | Művelet | Microsoft.BingMaps/Operations/read | A művelet leírását. |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.Cache/checknameavailability/action | Annak ellenőrzése, hogy a név rendelkezésre áll-e egy új Redis Cache-hez való használatra |
> | Művelet | Microsoft.Cache/locations/operationresults/read | Egy olyan hosszú ideje futó művelet eredményének beolvasása, amelynek esetében a Location (Hely) fejléc vissza lett küldve az ügyfélnek |
> | Művelet | Microsoft.Cache/operations/read | A Microsoft.Cache szolgáltató által támogatott műveletek listázása. |
> | Művelet | Microsoft.Cache/redis/delete | Teljes Redis gyorsítótár törlése |
> | Művelet | Microsoft.Cache/redis/export/action | Redis-adatok exportálása előre megadott tárolási blobokba meghatározott formátumban |
> | Művelet | Microsoft.Cache/redis/firewallRules/delete | Egy Redis Cache IP-tűzfalszabályainak törlése |
> | Művelet | Microsoft.Cache/redis/firewallRules/read | Egy Redis Cache IP-tűzfalszabályainak beolvasása |
> | Művelet | Microsoft.Cache/redis/firewallRules/write | Egy Redis Cache IP-tűzfalszabályainak szerkesztése |
> | Művelet | Microsoft.Cache/redis/forceReboot/action | Egy gyorsítótárpéldány kényszerített újraindítása, mely adatvesztést okozhat. |
> | Művelet | Microsoft.Cache/redis/import/action | Meghatározott formátumú adatok importálása a Redis szolgáltatásba több blobból |
> | Művelet | Microsoft.Cache/redis/linkedservers/delete | Egy Redis gyorsítótárhoz csatolt kiszolgáló törlése |
> | Művelet | Microsoft.Cache/redis/linkedservers/read | Egy Redis gyorsítótárhoz hozzárendelt csatolt kiszolgálók beolvasása. |
> | Művelet | Microsoft.Cache/redis/linkedservers/write | Csatolt kiszolgáló hozzáadása egy Redis gyorsítótárhoz |
> | Művelet | Microsoft.Cache/redis/listKeys/action | Redis gyorsítótár elérésikulcs-értékeinek megtekintése a felügyeleti portálon |
> | Művelet | Microsoft.Cache/redis/listUpgradeNotifications/read | A gyorsítótár bérlője legutóbbi csomagváltási értesítéseinek listázása. |
> | Művelet | Microsoft.Cache/redis/metricDefinitions/read | Egy Redis gyorsítótárhoz elérhető metrikai meghatározások beolvasása |
> | Művelet | Microsoft.Cache/redis/patchSchedules/delete | Egy Redis Cache javítási ütemezésének törlése |
> | Művelet | Microsoft.Cache/redis/patchSchedules/read | Egy Redis Cache javítási ütemezésének beolvasása |
> | Művelet | Microsoft.Cache/redis/patchSchedules/write | Egy Redis Cache javítási ütemezésének módosítása |
> | Művelet | Microsoft.Cache/redis/read | Redis gyorsítótár beállításainak és konfigurációjának megtekintése a felügyeleti portálon |
> | Művelet | Microsoft.Cache/redis/regenerateKey/action | Redis gyorsítótár elérésikulcs-értékeinek módosítása a felügyeleti portálon |
> | Művelet | Microsoft.Cache/redis/start/action | Gyorsítótárpéldány elindítása. |
> | Művelet | Microsoft.Cache/redis/stop/action | Gyorsítótárpéldány leállítása. |
> | Művelet | Microsoft.Cache/redis/write | Redis gyorsítótár beállításainak és konfigurációjának módosítása a felügyeleti portálon |
> | Művelet | Microsoft.Cache/register/action | A Microsoft.Cache erőforrás-szolgáltató regisztrálása egy előfizetéshez |
> | Művelet | Microsoft.Cache/unregister/action | A Microsoft.Cache erőforrás-szolgáltató egy előfizetéshez tartozó regisztrációjának törlése |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.Capacity/register/action | A kapacitás erőforrás-szolgáltató regisztrálja, és lehetővé teszi a kapacitás erőforrások létrehozását. |
> | Művelet | Microsoft.Capacity/reservationorders/action | Bármely foglalásának frissítése |
> | Művelet | Microsoft.Capacity/reservationorders/delete | A foglalás törlése |
> | Művelet | Microsoft.Capacity/reservationorders/read | Olvassa el az összes foglalások |
> | Művelet | Microsoft.Capacity/reservationorders/reservations/action | Bármely foglalásának frissítése |
> | Művelet | Microsoft.Capacity/reservationorders/reservations/delete | A foglalás törlése |
> | Művelet | Microsoft.Capacity/reservationorders/reservations/read | Olvassa el az összes foglalások |
> | Művelet | Microsoft.Capacity/reservationorders/reservations/revisions/read | Olvassa el az összes foglalások |
> | Művelet | Microsoft.Capacity/reservationorders/reservations/write | A foglalás létrehozása |
> | Művelet | Microsoft.Capacity/reservationorders/write | A foglalás létrehozása |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.Cdn/CheckNameAvailability/action |  |
> | Művelet | Microsoft.Cdn/CheckResourceUsage/action |  |
> | Művelet | Microsoft.Cdn/edgenodes/delete |  |
> | Művelet | Microsoft.Cdn/edgenodes/read |  |
> | Művelet | Microsoft.Cdn/edgenodes/write |  |
> | Művelet | Microsoft.Cdn/operationresults/delete |  |
> | Művelet | Microsoft.Cdn/operationresults/profileresults/CheckResourceUsage/action |  |
> | Művelet | Microsoft.Cdn/operationresults/profileresults/delete |  |
> | Művelet | Microsoft.Cdn/operationresults/profileresults/endpointresults/CheckResourceUsage/action |  |
> | Művelet | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/delete |  |
> | Művelet | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/action |  |
> | Művelet | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/action |  |
> | Művelet | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/read |  |
> | Művelet | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/write |  |
> | Művelet | Microsoft.Cdn/operationresults/profileresults/endpointresults/delete |  |
> | Művelet | Microsoft.Cdn/operationresults/profileresults/endpointresults/Load/action |  |
> | Művelet | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/delete |  |
> | Művelet | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/read |  |
> | Művelet | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/write |  |
> | Művelet | Microsoft.Cdn/operationresults/profileresults/endpointresults/Purge/action |  |
> | Művelet | Microsoft.Cdn/operationresults/profileresults/endpointresults/read |  |
> | Művelet | Microsoft.Cdn/operationresults/profileresults/endpointresults/Start/action |  |
> | Művelet | Microsoft.Cdn/operationresults/profileresults/endpointresults/Stop/action |  |
> | Művelet | Microsoft.Cdn/operationresults/profileresults/endpointresults/ValidateCustomDomain/action |  |
> | Művelet | Microsoft.Cdn/operationresults/profileresults/endpointresults/write |  |
> | Művelet | Microsoft.Cdn/operationresults/profileresults/GenerateSsoUri/action |  |
> | Művelet | Microsoft.Cdn/operationresults/profileresults/GetSupportedOptimizationTypes/action |  |
> | Művelet | Microsoft.Cdn/operationresults/profileresults/read |  |
> | Művelet | Microsoft.Cdn/operationresults/profileresults/write |  |
> | Művelet | Microsoft.Cdn/operationresults/read |  |
> | Művelet | Microsoft.Cdn/operationresults/write |  |
> | Művelet | Microsoft.Cdn/operations/read |  |
> | Művelet | Microsoft.Cdn/profiles/CheckResourceUsage/action |  |
> | Művelet | Microsoft.Cdn/profiles/delete |  |
> | Művelet | Microsoft.Cdn/profiles/endpoints/CheckResourceUsage/action |  |
> | Művelet | Microsoft.Cdn/profiles/endpoints/customdomains/delete |  |
> | Művelet | Microsoft.Cdn/profiles/endpoints/customdomains/DisableCustomHttps/action |  |
> | Művelet | Microsoft.Cdn/profiles/endpoints/customdomains/EnableCustomHttps/action |  |
> | Művelet | Microsoft.Cdn/profiles/endpoints/customdomains/read |  |
> | Művelet | Microsoft.Cdn/profiles/endpoints/customdomains/write |  |
> | Művelet | Microsoft.Cdn/profiles/endpoints/delete |  |
> | Művelet | Microsoft.Cdn/profiles/endpoints/Load/action |  |
> | Művelet | Microsoft.Cdn/profiles/endpoints/origins/delete |  |
> | Művelet | Microsoft.Cdn/profiles/endpoints/origins/read |  |
> | Művelet | Microsoft.Cdn/profiles/endpoints/origins/write |  |
> | Művelet | Microsoft.Cdn/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Művelet | Microsoft.Cdn/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti az erőforrás diagnosztikai beállításait |
> | Művelet | Microsoft.Cdn/profiles/endpoints/providers/Microsoft.Insights/logDefinitions/read | Microsoft.Cdn naplók beolvasása. |
> | Művelet | Microsoft.Cdn/profiles/endpoints/Purge/action |  |
> | Művelet | Microsoft.Cdn/profiles/endpoints/read |  |
> | Művelet | Microsoft.Cdn/profiles/endpoints/Start/action |  |
> | Művelet | Microsoft.Cdn/profiles/endpoints/Stop/action |  |
> | Művelet | Microsoft.Cdn/profiles/endpoints/ValidateCustomDomain/action |  |
> | Művelet | Microsoft.Cdn/profiles/endpoints/write |  |
> | Művelet | Microsoft.Cdn/profiles/GenerateSsoUri/action |  |
> | Művelet | Microsoft.Cdn/profiles/GetSupportedOptimizationTypes/action |  |
> | Művelet | Microsoft.Cdn/profiles/read |  |
> | Művelet | Microsoft.Cdn/profiles/write |  |
> | Művelet | Microsoft.Cdn/register/action | Az előfizetés regisztrálása a CDN erőforrás-szolgáltatónál, és a CDN-profilok létrehozásának engedélyezése. |
> | Művelet | Microsoft.Cdn/ValidateProbe/action |  |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.CertificateRegistration/certificateOrders/certificates/Delete | Meglévő tanúsítvány törlése |
> | Művelet | Microsoft.CertificateRegistration/certificateOrders/certificates/Read | A tanúsítványok listájának lekérdezése |
> | Művelet | Microsoft.CertificateRegistration/certificateOrders/certificates/Write | Adja hozzá az új tanúsítványt, vagy egy meglévő frissítése |
> | Művelet | Microsoft.CertificateRegistration/certificateOrders/Delete | Egy meglévő AppServiceCertificate törlése |
> | Művelet | Microsoft.CertificateRegistration/certificateOrders/Read | A CertificateOrders listájának lekérdezése |
> | Művelet | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | Hajtsa végre újból egy meglévő certificateorder |
> | Művelet | Microsoft.CertificateRegistration/certificateOrders/renew/Action | Egy meglévő certificateorder megújítása |
> | Művelet | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | Tanúsítvány e-mail újraküldése |
> | Művelet | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Küldje el újra a kérelmet e-mailt egy másik e-mail címet |
> | Művelet | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | App Service kiállított tanúsítvány hely lezárása beolvasása |
> | Művelet | Microsoft.CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | A tanúsítvány műveletek listájának beolvasása |
> | Művelet | Microsoft.CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | Tanúsítvány e-mail előzmények beolvasása |
> | Művelet | Microsoft.CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | Tartomány tulajdonjogának ellenőrzése |
> | Művelet | Microsoft.CertificateRegistration/certificateOrders/Write | Adja hozzá egy új certificateOrder vagy egy meglévő frissítése |
> | Művelet | Microsoft.CertificateRegistration/operations/Read | Lista összes műveleteket az app service-tanúsítvány regisztrálása |
> | Művelet | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | Az alkalmazás egyszerű kiépítés egyszerű szolgáltatásnév |
> | Művelet | Microsoft.CertificateRegistration/register/action | Az előfizetés a Microsoft Certificates erőforrás-szolgáltató regisztrálása |
> | Művelet | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | Ellenőrizze a beszerzési tanúsítványobjektum lefolytatása nélkül |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.ClassicCompute/capabilities/read | Képességek megjelenítése |
> | Művelet | Microsoft.ClassicCompute/checkDomainNameAvailability/action | Az adott tartománynév elérhetőségének ellenőrzése. |
> | Művelet | Microsoft.ClassicCompute/domainNames/active/write | Az aktív tartománynév beállítása. |
> | Művelet | Microsoft.ClassicCompute/domainNames/availabilitySets/read | Az erőforrás rendelkezésre állási csoportjának megjelenítése. |
> | Művelet | Microsoft.ClassicCompute/domainNames/capabilities/read | A tartománynév-képességek megjelenítése |
> | Művelet | Microsoft.ClassicCompute/domainNames/delete | Erőforrások tartománynevének eltávolítása. |
> | Művelet | Microsoft.ClassicCompute/domainNames/extensions/delete | A tartománynév-kiterjesztések eltávolítása. |
> | Művelet | Microsoft.ClassicCompute/domainNames/extensions/operationStatuses/read | A tartománynév-kiterjesztések műveleti állapotának beolvasása. |
> | Művelet | Microsoft.ClassicCompute/domainNames/extensions/read | A tartománynév-kiterjesztéseket adja vissza. |
> | Művelet | Microsoft.ClassicCompute/domainNames/extensions/write | Tartománynév-kiterjesztések hozzáadása. |
> | Művelet | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/delete | Új belső terheléselosztás eltávolítása. |
> | Művelet | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | A tartománynevek belső terheléselosztói műveleti állapotának beolvasása. |
> | Művelet | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/read | A belső terheléselosztók beolvasása. |
> | Művelet | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/write | Új belső terheléselosztás létrehozása. |
> | Művelet | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | A tartománynevek elosztott terhelésű végpontcsoportjai műveleti állapotának beolvasása. |
> | Művelet | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/read | Az elosztott terhelésű végpontcsoportok megjelenítése |
> | Művelet | Microsoft.ClassicCompute/domainNames/read | Erőforrások tartománynevének visszaadása. |
> | Művelet | Microsoft.ClassicCompute/domainNames/serviceCertificates/delete | Az alkalmazott szolgáltatási tanúsítványok törlése. |
> | Művelet | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | A tartománynevek szolgáltatási tanúsítványai műveleti állapotának beolvasása. |
> | Művelet | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | Az alkalmazott szolgáltatási tanúsítványok visszaadása. |
> | Művelet | Microsoft.ClassicCompute/domainNames/serviceCertificates/write | Alkalmazott szolgáltatási tanúsítványok hozzáadása vagy módosítása. |
> | Művelet | Microsoft.ClassicCompute/domainNames/slots/delete | A megadott üzembe helyezési pont törlése. |
> | Művelet | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | A tartománynevek üzembe helyezési pontjai műveleti állapotának beolvasása. |
> | Művelet | Microsoft.ClassicCompute/domainNames/slots/read | Üzembe helyezési pontok megjelenítése. |
> | Művelet | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | Eltávolítja az üzembe helyezési ponti szerepkör kiterjesztéshivatkozását. |
> | Művelet | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | A tartománynevek üzembe helyezési ponti szerepkörei kiterjesztéshivatkozásai műveleti állapotának beolvasása. |
> | Művelet | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | Az üzembe helyezési ponti szerepkör kiterjesztéshivatkozását adja vissza. |
> | Művelet | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | Az üzembe helyezési ponti szerepkör kiterjesztéshivatkozásának hozzáadása vagy módosítása. |
> | Művelet | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read | Diagnosztikai beállítások beolvasása. |
> | Művelet | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write | Diagnosztikai beállítások hozzáadása vagy módosítása. |
> | Művelet | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | A metrikai meghatározások beolvasása. |
> | Művelet | Microsoft.ClassicCompute/domainNames/slots/roles/read | Üzembe helyezési pont szerepkörének beolvasása. |
> | Művelet | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | A tartománynevek üzembe helyezési ponti szerepkörei szerepkörpéldányai műveleti állapotának beolvasása. |
> | Művelet | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | Szerepkörpéldány beolvasása. |
> | Művelet | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | A szerepkörpéldány újraépítése. |
> | Művelet | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | Szerepkörpéldány rendszerképének alaphelyzetbe állítása. |
> | Művelet | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | A szerepkörpéldányok újraindítása. |
> | Művelet | Microsoft.ClassicCompute/domainNames/slots/start/action | Üzembe helyezési pont indítása. |
> | Művelet | Microsoft.ClassicCompute/domainNames/slots/state/start/write | Az üzembe helyezési pont állapotának leállítottra állítása. |
> | Művelet | Microsoft.ClassicCompute/domainNames/slots/state/stop/write | Az üzembe helyezési pont állapotának elindítottra állítása. |
> | Művelet | Microsoft.ClassicCompute/domainNames/slots/stop/action | Üzembe helyezési pont felfüggesztése. |
> | Művelet | Microsoft.ClassicCompute/domainNames/slots/upgradeDomain/write | Verzióváltási tartomány feldolgozása. |
> | Művelet | Microsoft.ClassicCompute/domainNames/slots/write | Telepítés létrehozása vagy frissítése. |
> | Művelet | Microsoft.ClassicCompute/domainNames/swap/action | Előkészítési pont cseréje üzemelési pontra. |
> | Művelet | Microsoft.ClassicCompute/domainNames/write | Erőforrások tartománynevének hozzáadása vagy módosítása. |
> | Művelet | Microsoft.ClassicCompute/moveSubscriptionResources/action | Minden hagyományos erőforrás áthelyezése egy másik előfizetésbe. |
> | Művelet | Microsoft.ClassicCompute/operatingSystemFamilies/read | Ez a művelet a Microsoft Azure-ban elérhető vendég operációsrendszer-családokat, valamint az egyes családokon belül elérhető operációsrendszer-verziókat listázza. |
> | Művelet | Microsoft.ClassicCompute/operatingSystems/read | Ez a művelet a Microsoft Azure-ban jelenleg elérhető vendég operációsrendszer-verziókat listázza. |
> | Művelet | Microsoft.ClassicCompute/quotas/read | Az előfizetés kvótájának beolvasása. |
> | Művelet | Microsoft.ClassicCompute/register/action | Regisztrálás a Classic Compute szolgáltatónál |
> | Művelet | Microsoft.ClassicCompute/resourceTypes/skus/read | Lekérdezi a támogatott erőforrástípusok termékváltozat-listáját. |
> | Művelet | Microsoft.ClassicCompute/validateSubscriptionMoveAvailability/action | Annak ellenőrzése, hogy áthelyezhető-e az előfizetés hagyományos áthelyezéssel. |
> | Művelet | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | A virtuális géphez társított hálózati biztonsági csoport törlése. |
> | Művelet | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | A virtuális gépek társított hálózati biztonsági csoportjai műveleti állapotának beolvasása. |
> | Művelet | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | A virtuális géphez társított hálózati biztonsági csoport lekérése. |
> | Művelet | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | A virtuális géphez társított hálózati biztonsági csoport hozzáadása. |
> | Művelet | Microsoft.ClassicCompute/virtualMachines/asyncOperations/read | Lehetséges aszinkron műveletek beolvasása |
> | Művelet | Microsoft.ClassicCompute/virtualMachines/attachDisk/action | Adatlemez csatlakoztatása a virtuális géphez. |
> | Művelet | Microsoft.ClassicCompute/virtualMachines/delete | Virtuális gépek eltávolítása. |
> | Művelet | Microsoft.ClassicCompute/virtualMachines/detachDisk/action | Adatlemez leválasztása a virtuális gépről. |
> | Művelet | Microsoft.ClassicCompute/virtualMachines/disks/read | Adatlemezek listájának beolvasása |
> | Művelet | Microsoft.ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/action | Virtuális gép RDP-fájljának letöltése. |
> | Művelet | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | A virtuálisgép-bővítmények műveleti állapotának beolvasása. |
> | Művelet | Microsoft.ClassicCompute/virtualMachines/extensions/read | Virtuálisgép-bővítmény beolvasása. |
> | Művelet | Microsoft.ClassicCompute/virtualMachines/extensions/write | Virtuálisgép-bővítmény hozzáadása. |
> | Művelet | Microsoft.ClassicCompute/virtualMachines/metrics/read | Metrika beolvasása. |
> | Művelet | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | A hálózati interfészhez társított hálózati biztonsági csoport törlése. |
> | Művelet | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | A virtuális gépek társított hálózati biztonsági csoportjai műveleti állapotának beolvasása. |
> | Művelet | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | A hálózati interfészhez társított hálózati biztonsági csoport lekérése. |
> | Művelet | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | A hálózati interfészhez társított hálózati biztonsági csoport hozzáadása. |
> | Művelet | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | A virtuális gépek műveleti állapotának beolvasása. |
> | Művelet | Microsoft.ClassicCompute/virtualMachines/performMaintenance/action | A művelet elvégzi a virtuális gép karbantartását. |
> | Művelet | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | Diagnosztikai beállítások beolvasása. |
> | Művelet | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write | Diagnosztikai beállítások hozzáadása vagy módosítása. |
> | Művelet | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | A metrikai meghatározások beolvasása. |
> | Művelet | Microsoft.ClassicCompute/virtualMachines/read | Virtuális gépek listájának beolvasása. |
> | Művelet | Microsoft.ClassicCompute/virtualMachines/redeploy/action | A virtuális gép ismételt üzembe helyezése. |
> | Művelet | Microsoft.ClassicCompute/virtualMachines/restart/action | Virtuális gépek újraindítása. |
> | Művelet | Microsoft.ClassicCompute/virtualMachines/shutdown/action | Virtuális gép rendszerének leállítása. |
> | Művelet | Microsoft.ClassicCompute/virtualMachines/start/action | Virtuális gép elindítása. |
> | Művelet | Microsoft.ClassicCompute/virtualMachines/stop/action | Virtuális gép leállítása. |
> | Művelet | Microsoft.ClassicCompute/virtualMachines/write | Virtuális gépek hozzáadása vagy módosítása. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.ClassicNetwork/gatewaySupportedDevices/read | Támogatott eszközök listájának beolvasása. |
> | Művelet | Microsoft.ClassicNetwork/networkSecurityGroups/delete | A hálózati biztonsági csoport törlése. |
> | Művelet | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | A hálózati biztonsági csoport műveleti állapotának beolvasása. |
> | Művelet | Microsoft.ClassicNetwork/networkSecurityGroups/read | A hálózati biztonsági csoport lekérése. |
> | Művelet | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | A biztonsági szabály törlése. |
> | Művelet | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | A hálózati biztonsági csoport biztonsági szabályai műveleti állapotának beolvasása. |
> | Művelet | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | A biztonsági szabály beolvasása. |
> | Művelet | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | Egy biztonsági szabály hozzáadása vagy frissítése. |
> | Művelet | Microsoft.ClassicNetwork/networkSecurityGroups/write | Új hálózati biztonsági csoport hozzáadása. |
> | Művelet | Microsoft.ClassicNetwork/quotas/read | Az előfizetés kvótájának beolvasása. |
> | Művelet | Microsoft.ClassicNetwork/register/action | Regisztrálás a Classic Network szolgáltatónál |
> | Művelet | Microsoft.ClassicNetwork/reservedIps/delete | Fenntartott IP-cím törlése. |
> | Művelet | Microsoft.ClassicNetwork/reservedIps/join/action | Csatlakozás egy fenntartott IP-címhez |
> | Művelet | Microsoft.ClassicNetwork/reservedIps/link/action | Egy fenntartott IP-cím hivatkozása |
> | Művelet | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | A fenntartott IP-címek műveleti állapotának beolvasása. |
> | Művelet | Microsoft.ClassicNetwork/reservedIps/read | A fenntartott IP-címek beolvasása |
> | Művelet | Microsoft.ClassicNetwork/reservedIps/write | Új fenntartott IP-cím felvétele |
> | Művelet | Microsoft.ClassicNetwork/virtualNetworks/capabilities/read | Képességek megjelenítése |
> | Művelet | Microsoft.ClassicNetwork/virtualNetworks/checkIPAddressAvailability/action | Az adott IP-cím virtuális hálózaton belüli elérhetőségét ellenőrzi. |
> | Művelet | Microsoft.ClassicNetwork/virtualNetworks/delete | A virtuális hálózat törlése. |
> | Művelet | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/delete | Az ügyféltanúsítvány visszavonásának megszüntetése. |
> | Művelet | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/read | Visszavont ügyféltanúsítványok beolvasása. |
> | Művelet | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/write | Ügyféltanúsítvány visszavonása. |
> | Művelet | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | A virtuális hálózati átjáró ügyféltanúsítványának törlése. |
> | Művelet | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/download/action | Tanúsítvány letöltése ujjlenyomat útján. |
> | Művelet | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | A virtuális hálózati átjáró tanúsítványcsomagjának listázása. |
> | Művelet | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | Ügyfél főtanúsítványainak megkeresése. |
> | Művelet | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | Ügyfél új főtanúsítványának feltöltése. |
> | Művelet | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/connect/action | Webhelyek közötti átjárókapcsolat létesítése. |
> | Művelet | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/disconnect/action | Webhelyek közötti átjárókapcsolat leválasztása. |
> | Művelet | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/read | Kapcsolatok listájának beolvasása. |
> | Művelet | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/test/action | Webhelyek közötti átjárókapcsolat tesztelése. |
> | Művelet | Microsoft.ClassicNetwork/virtualNetworks/gateways/delete | Virtuális hálózati átjáró törlése. |
> | Művelet | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/action | Eszközkonfigurációs parancsfájl letöltése. |
> | Művelet | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDiagnostics/action | Átjáródiagnosztika letöltése. |
> | Művelet | Microsoft.ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/action | Kör szolgáltatáskulcsának beolvasása. |
> | Művelet | Microsoft.ClassicNetwork/virtualNetworks/gateways/listPackage/action | A virtuális hálózati átjárócsomag listázása. |
> | Művelet | Microsoft.ClassicNetwork/virtualNetworks/gateways/operationStatuses/read | A virtuális hálózati átjárók műveleti állapotának beolvasása. |
> | Művelet | Microsoft.ClassicNetwork/virtualNetworks/gateways/packages/read | A virtuális hálózati átjárócsomag beolvasása. |
> | Művelet | Microsoft.ClassicNetwork/virtualNetworks/gateways/read | Virtuális hálózati átjárók beolvasása. |
> | Művelet | Microsoft.ClassicNetwork/virtualNetworks/gateways/startDiagnostics/action | Virtuális hálózati átjáró diagnosztikájának indítása. |
> | Művelet | Microsoft.ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/action | Virtuális hálózati átjáró diagnosztikájának leállítása. |
> | Művelet | Microsoft.ClassicNetwork/virtualNetworks/gateways/write | Virtuális hálózati átjáró hozzáadása. |
> | Művelet | Microsoft.ClassicNetwork/virtualNetworks/join/action | Csatlakozás a virtuális hálózathoz. |
> | Művelet | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | A virtuális hálózatok műveleti állapotának beolvasása. |
> | Művelet | Microsoft.ClassicNetwork/virtualNetworks/peer/action | Társviszony-létesítés két virtuális hálózat között. |
> | Művelet | Microsoft.ClassicNetwork/virtualNetworks/read | Virtuális hálózat beolvasása. |
> | Művelet | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | Az alhálózathoz társított hálózati biztonsági csoport törlése. |
> | Művelet | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | A virtuális hálózat alhálózatához társított hálózati biztonsági csoport műveleti állapotának beolvasása. |
> | Művelet | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | Az alhálózathoz társított hálózati biztonsági csoport lekérése. |
> | Művelet | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | Az alhálózathoz társított hálózati biztonsági csoport hozzáadása. |
> | Művelet | Microsoft.ClassicNetwork/virtualNetworks/write | Új virtuális hálózat hozzáadása. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.ClassicStorage/capabilities/read | Képességek megjelenítése |
> | Művelet | Microsoft.ClassicStorage/checkStorageAccountAvailability/action | A tárfiók elérhetőségének ellenőrzése. |
> | Művelet | Microsoft.ClassicStorage/disks/read | A tárfióklemez beolvasása. |
> | Művelet | Microsoft.ClassicStorage/images/read | A lemezkép visszaadása. |
> | Művelet | Microsoft.ClassicStorage/osImages/read | A operációs rendszerkép beolvasása. |
> | Művelet | Microsoft.ClassicStorage/publicImages/read | A nyilvános virtuális gép lemezképének beolvasása. |
> | Művelet | Microsoft.ClassicStorage/quotas/read | Az előfizetés kvótájának beolvasása. |
> | Művelet | Microsoft.ClassicStorage/register/action | Regisztrálás egy hagyományos tárolóba. |
> | Művelet | Microsoft.ClassicStorage/storageAccounts/delete | Tárfiók törlése. |
> | Művelet | Microsoft.ClassicStorage/storageAccounts/disks/delete | A megadott tárfióklemez törlése. |
> | Művelet | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | Az erőforrás műveleti állapotának beolvasása. |
> | Művelet | Microsoft.ClassicStorage/storageAccounts/disks/read | A tárfióklemez beolvasása. |
> | Művelet | Microsoft.ClassicStorage/storageAccounts/disks/write | Új tárfióklemez felvétele. |
> | Művelet | Microsoft.ClassicStorage/storageAccounts/images/delete | A megadott tárfióklemezkép törlése. |
> | Művelet | Microsoft.ClassicStorage/storageAccounts/images/read | A tárfióklemezkép beolvasása. |
> | Művelet | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Tárfiókok elérési kulcsainak listázása. |
> | Művelet | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | Az erőforrás műveleti állapotának beolvasása. |
> | Művelet | Microsoft.ClassicStorage/storageAccounts/osImages/delete | A megadott tárfiók operációsrendszer-lemezképének törlése. |
> | Művelet | Microsoft.ClassicStorage/storageAccounts/osImages/read | A tárfiók operációsrendszer-lemezképének visszaadása. |
> | Művelet | Microsoft.ClassicStorage/storageAccounts/read | Tárfiók visszaadása az adott fiókhoz. |
> | Művelet | Microsoft.ClassicStorage/storageAccounts/regenerateKey/action | Tárfiók meglévő elérési kulcsainak újragenerálása. |
> | Művelet | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | Diagnosztikai beállítások beolvasása. |
> | Művelet | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | Diagnosztikai beállítások hozzáadása vagy módosítása. |
> | Művelet | Microsoft.ClassicStorage/storageAccounts/services/metricDefinitions/read | A metrikai meghatározások beolvasása. |
> | Művelet | Microsoft.ClassicStorage/storageAccounts/services/metrics/read | Metrika beolvasása. |
> | Művelet | Microsoft.ClassicStorage/storageAccounts/services/read | Elérhető szolgáltatások beolvasása. |
> | Művelet | Microsoft.ClassicStorage/storageAccounts/write | Új tárfiók hozzáadása. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.CognitiveServices/accounts/delete | API-fiókok törlése |
> | Művelet | Microsoft.CognitiveServices/accounts/listKeys/action | Kulcsok listázása |
> | Művelet | Microsoft.CognitiveServices/accounts/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításának beolvasása. |
> | Művelet | Microsoft.CognitiveServices/accounts/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése. |
> | Művelet | Microsoft.CognitiveServices/accounts/providers/Microsoft.Insights/metricDefinitions/read | A Cognitive Serviceshez elérhető metrikák beolvasása. |
> | Művelet | Microsoft.CognitiveServices/accounts/read | API-fiókok beolvasása. |
> | Művelet | Microsoft.CognitiveServices/accounts/regenerateKey/action | Kulcs újragenerálása |
> | Művelet | Microsoft.CognitiveServices/accounts/skus/read | Az egy adott meglévő erőforráshoz rendelkezésre álló termékváltozatok beolvasása. |
> | Művelet | Microsoft.CognitiveServices/accounts/usages/read | Egy meglévő erőforrás kvótahasználatának beolvasása. |
> | Művelet | Microsoft.CognitiveServices/accounts/write | API-fiókok írása. |
> | Művelet | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Egy előfizetéshez elérhető termékváltozatok beolvasása. |
> | Művelet | Microsoft.CognitiveServices/Operations/read | Az összes elérhető művelet listázása |
> | Művelet | Microsoft.CognitiveServices/register/action | Cognitive Services-előfizetés regisztrálása |
> | Művelet | Microsoft.CognitiveServices/skus/read | A rendelkezésre álló termékváltozatok beolvasása a Cognitive Services számára. |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.Commerce/RateCard/read | Értéket ad vissza adatokat, Erőforrás-kijelző metaadatok és az adott előfizetéshez tartozó díjszabás kínálnak. |
> | Művelet | Microsoft.Commerce/UsageAggregates/read | Lekéri a Microsoft Azure használat előfizetés. Az eredmény összesítések tartalmaz a kapcsolódó információk, egy adott időtartományt a használati adatok, az előfizetés és az erőforrás. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.Compute/availabilitySets/delete | Törli a rendelkezésre állási csoportot |
> | Művelet | Microsoft.Compute/availabilitySets/read | Egy adott rendelkezésre állási csoport tulajdonságainak beolvasása |
> | Művelet | Microsoft.Compute/availabilitySets/vmSizes/read | A virtuális gépek a rendelkezésre állási csoportban való létrehozásához vagy frissítéséhez használható méretek listázása |
> | Művelet | Microsoft.Compute/availabilitySets/write | Létrehoz egy új vagy frissít egy meglévő rendelkezésre állási csoportot |
> | Művelet | Microsoft.Compute/disks/beginGetAccess/action | Lemez SAS URI-jának beolvasása blobhozzáféréshez |
> | Művelet | Microsoft.Compute/disks/delete | A lemez törlése |
> | Művelet | Microsoft.Compute/disks/endGetAccess/action | A lemez SAS URI-jának visszavonása |
> | Művelet | Microsoft.Compute/disks/read | A lemez tulajdonságainak beolvasása |
> | Művelet | Microsoft.Compute/disks/write | Új lemez létrehozása vagy meglévő lemez frissítése |
> | Művelet | Microsoft.Compute/images/delete | A lemezkép törlése |
> | Művelet | Microsoft.Compute/images/read | A lemezkép tulajdonságainak beolvasása |
> | Művelet | Microsoft.Compute/images/write | Új lemezkép létrehozása vagy meglévő frissítése |
> | Művelet | Microsoft.Compute/locations/capsOperations/read | Aszinkron Caps művelet állapotának beolvasása |
> | Művelet | Microsoft.Compute/locations/diskOperations/read | Lemez aszinkron művelet állapotát olvassa be |
> | Művelet | Microsoft.Compute/locations/operations/read | Egy aszinkron művelet állapotát olvassa be |
> | Művelet | Microsoft.Compute/locations/publishers/artifacttypes/offers/read | Platformlemezképet tartalmazó ajánlat tulajdonságainak beolvasása |
> | Művelet | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | Platformlemezképet tartalmazó termékváltozat tulajdonságainak beolvasása |
> | Művelet | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | Platformlemezkép-verzió tulajdonságainak beolvasása |
> | Művelet | Microsoft.Compute/locations/publishers/artifacttypes/types/read | VM-bővítmény-típus tulajdonságainak beolvasása |
> | Művelet | Microsoft.Compute/locations/publishers/artifacttypes/types/versions/read | VM-bővítmény-verzió tulajdonságainak beolvasása |
> | Művelet | Microsoft.Compute/locations/publishers/read | Gyártó tulajdonságainak beolvasása |
> | Művelet | Microsoft.Compute/locations/runCommands/read | A helyen elérhető futtatási parancsok listázása |
> | Művelet | Microsoft.Compute/locations/usages/read | Az előfizetés adott helyen lévő számítási erőforrásaihoz tartozó szolgáltatások korlátait és az aktuális felhasználási mennyiségeket olvassa be |
> | Művelet | Microsoft.Compute/locations/vmSizes/read | Listázza az adott helyen elérhető virtuálisgép-méreteket |
> | Művelet | Microsoft.Compute/operations/read | A Microsoft.Compute erőforrás-szolgáltató elérhető műveleteinek listázása |
> | Művelet | Microsoft.Compute/register/action | Az előfizetés regisztrálása a Microsoft.Compute erőforrás-szolgáltatónál |
> | Művelet | Microsoft.Compute/restorePointCollections/delete | A visszaállításipont-gyűjtemény és az abban található visszaállítási pontok törlése |
> | Művelet | Microsoft.Compute/restorePointCollections/read | Egy visszaállításipont-gyűjtemény tulajdonságainak beolvasása |
> | Művelet | Microsoft.Compute/restorePointCollections/restorePoints/delete | A visszaállítási pont törlése |
> | Művelet | Microsoft.Compute/restorePointCollections/restorePoints/read | Egy visszaállítási pont tulajdonságainak beolvasása |
> | Művelet | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | Egy visszaállítási pont tulajdonságainak beolvasása a blob SAS URI azonosítóival |
> | Művelet | Microsoft.Compute/restorePointCollections/restorePoints/write | Új visszaállítási pont létrehozása |
> | Művelet | Microsoft.Compute/restorePointCollections/write | Egy új visszaállításipont-gyűjtemény létrehozása, vagy egy meglévő módosítása |
> | Művelet | Microsoft.Compute/sharedVMImages/delete | A megosztott VM-lemezkép törlése |
> | Művelet | Microsoft.Compute/sharedVMImages/read | Megosztott VM-lemezkép tulajdonságainak beolvasása |
> | Művelet | Microsoft.Compute/sharedVMImages/versions/delete | A megosztott VM-lemezkép-verzió törlése |
> | Művelet | Microsoft.Compute/sharedVMImages/versions/read | Megosztott VM-lemezkép-verzió tulajdonságainak beolvasása |
> | Művelet | Microsoft.Compute/sharedVMImages/versions/replicate/action | Megosztott VM-lemezkép-verzió replikálása a célrégiókba |
> | Művelet | Microsoft.Compute/sharedVMImages/versions/write | Hozzon létre egy új megosztott VM-lemezkép-verziót, vagy frissítsen egy meglévőt |
> | Művelet | Microsoft.Compute/sharedVMImages/write | Új megosztott VM-lemezkép létrehozása vagy egy meglévő frissítése |
> | Művelet | Microsoft.Compute/skus/read | Az előfizetéssel elérhető Microsoft Compute-termékváltozatok listájának beolvasása |
> | Művelet | Microsoft.Compute/snapshots/beginGetAccess/action | A SAS URI-jának a pillanatkép beolvasása blobhozzáféréshez |
> | Művelet | Microsoft.Compute/snapshots/delete | A pillanatkép törlése |
> | Művelet | Microsoft.Compute/snapshots/endGetAccess/action | A pillanatkép SAS URI visszavonása |
> | Művelet | Microsoft.Compute/snapshots/read | Pillanatkép tulajdonságainak beolvasása |
> | Művelet | Microsoft.Compute/snapshots/write | Új pillanatkép létrehozása vagy meglévő pillanatkép frissítése |
> | Művelet | Microsoft.Compute/virtualMachines/capture/action | Rögzíti a virtuális gépet úgy, hogy másolatot készít a virtuális merevlemezekről és létrehoz egy sablont, mellyel hasonló virtuális gépek hozhatók létre |
> | Művelet | Microsoft.Compute/virtualMachines/convertToManagedDisks/action | A virtuális gép blobalapú lemezeinek konvertálása felügyelt lemezekké |
> | Művelet | Microsoft.Compute/virtualMachines/deallocate/action | Kikapcsolja a virtuális gépet és felszabadítja a számítási erőforrásokat |
> | Művelet | Microsoft.Compute/virtualMachines/delete | Törli a virtuális gépet |
> | Művelet | Microsoft.Compute/virtualMachines/extensions/delete | Törli a virtuálisgép-bővítményt |
> | Művelet | Microsoft.Compute/virtualMachines/extensions/read | Egy virtuálisgép-bővítmény tulajdonságait olvassa be |
> | Művelet | Microsoft.Compute/virtualMachines/extensions/write | Létrehoz egy új vagy frissít egy meglévő virtuálisgép-bővítményt |
> | Művelet | Microsoft.Compute/virtualMachines/generalize/action | Általánosítottra állítja a virtuális gép állapotát és előkészíti a virtuális gépet a rögzítéshez |
> | Művelet | Microsoft.Compute/virtualMachines/instanceView/read | A virtuális gép és az erőforrásai részletes futási állapotát olvassa be |
> | Művelet | Microsoft.Compute/virtualMachines/performMaintenance/action | Karbantartási művelet végrehajtása a virtuális gépen. |
> | Művelet | Microsoft.Compute/virtualMachines/powerOff/action | Kikapcsolja a virtuális gép. Vegye figyelembe, hogy a virtuális gép továbbra is fizetnie kell ezért. |
> | Művelet | Microsoft.Compute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Egy virtuális gép metrikadefinícióinak beolvasása |
> | Művelet | Microsoft.Compute/virtualMachines/read | Egy virtuális gép tulajdonságait olvassa be |
> | Művelet | Microsoft.Compute/virtualMachines/redeploy/action | A virtuális gép újbóli üzembe helyezése |
> | Művelet | Microsoft.Compute/virtualMachines/reimage/action | Különbséglemezt használó virtuális gép rendszerképét állítja alaphelyzetbe. |
> | Művelet | Microsoft.Compute/virtualMachines/restart/action | Újraindítja a virtuális gépet |
> | Művelet | Microsoft.Compute/virtualMachines/runCommand/action | Egy előre definiált parancsfájl végrehajtása a virtuális gépen |
> | Művelet | Microsoft.Compute/virtualMachines/start/action | Elindítja a virtuális gépet |
> | Művelet | Microsoft.Compute/virtualMachines/vmSizes/read | Listázza az elérhető méreteket, melyekre a virtuális gép frissíthető |
> | Művelet | Microsoft.Compute/virtualMachines/write | Létrehoz egy új virtuális gépet vagy frissít egy meglévő virtuális gépet |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/deallocate/action | A virtuálisgép-méretezési csoport példányaihoz hozzárendelt számítási erőforrások kikapcsolása és felszabadítása  |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/delete | A virtuálisgép-méretezési csoport törlése |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/delete/action | A virtuálisgép-méretezési csoport példányainak törlése |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/extensions/delete | A virtuálisgép-méretezési csoport bővítményének törlése |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/extensions/read | Egy virtuálisgép-méretezési csoport bővítménye tulajdonságainak beolvasása |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/extensions/write | Egy virtuálisgép-méretezési csoport egy új bővítményének létrehozása, vagy egy meglévő frissítése |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action | Service Fabric-beli virtuálisgép-méretezési csoport platformfrissítési tartományainak manuális bejárása egy elakadt függőben lévő frissítés befejezéséhez |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/instanceView/read | A virtuálisgép-méretezési csoport példánynézetének beolvasása |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/manualUpgrade/action | A példányok manuális frissítése a virtuálisgép-méretezési csoport legújabb modelljére |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | Egy virtuálisgép-méretezési csoport összes hálózati adaptere tulajdonságainak beolvasása |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | Virtuálisgép-méretezési csoport gépein végrehajtott operációsrendszer-frissítések előzményeinek beolvasása |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/performMaintenance/action | A virtuálisgép-méretezési csoport példányai tervezett karbantartásának végrehajtása |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | A virtuálisgép-méretezési csoport példányainak kikapcsolása |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/providers/Microsoft.Insights/metricDefinitions/read | Egy virtuálisgép-méretezési csoport metrikadefinícióinak beolvasása |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | Egy virtuálisgép-méretezési csoport összes nyilvános IP-címe tulajdonságainak beolvasása |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/read | Virtuálisgép-méretezési csoport tulajdonságainak beolvasása |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | A virtuálisgép-méretezési csoport példányainak ismételt üzembe helyezése |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/reimage/action | Ezzel a művelettel alaphelyzetbe állíthatja a virtuálisgép-méretezési csoport példányainak rendszerképét |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/restart/action | Virtuálisgép-méretezési csoport példányainak újraindítása |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/cancel/action | Ezzel a művelettel megszakítja a virtuálisgép-méretezési csoport működés közbeni frissítését |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | Virtuálisgép-méretezési csoport legutóbbi működés közbeni frissítési állapotának beolvasása |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/scale/action | Annak ellenőrzése, hogy van-e olyan meglévő virtuálisgép-méretezési csoport, mely horizontálisan le- vagy felskálázható a megadott példányszámra |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/skus/read | Egy meglévő virtuálisgép-méretezési csoport érvényes termékváltozatainak listázása |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/start/action | Virtuálisgép-méretezési csoport példányainak elindítása |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/deallocate/action | Kikapcsol egy virtuális gépet egy virtuálisgép-méretezési csoportban, és felszabadítja az ahhoz tartozó számítási erőforrásokat. |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/delete | Töröl egy virtuális gépet egy virtuálisgép-méretezési csoportban. |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | Egy virtuálisgép-méretezési csoport egy virtuális gépének példányait tartalmazó nézetet adja meg. |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | Nyilvános IP-cím létre virtuálisgép-méretezési csoport tulajdonságainak beolvasása. Virtuálisgép-méretezési csoport hozhat létre, legfeljebb egy nyilvános IP-cím ipconfiguration (magánhálózati IP-címe) |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | Egy hálózati adapter virtuálisgép-méretezési készlet használatával létrehozott összes vagy egy IP-konfiguráció tulajdonságainak beolvasása. IP-konfigurációk meghatároznia magánhálózati IP-címek |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | Egy virtuálisgép-méretezési csoportban létrehozott virtuális gép egy vagy minden hálózati adaptere tulajdonságainak beolvasása |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/action | Virtuálisgép-méretezési csoportban lévő virtuálisgép-példány tervezett karbantartásának végrehajtása |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/powerOff/action | Kikapcsol egy virtuálisgép-példányt egy virtuálisgép-méretezési csoportban. |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Egy méretezési csoportbeli virtuális gép metrikadefinícióinak beolvasása |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | Egy virtuálisgép-méretezési csoport egy virtuális gépének tulajdonságait adja meg |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | Virtuálisgép-méretezési csoportban lévő virtuálisgép-példány ismételt üzembe helyezése |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | A művelet alaphelyzetbe állítja a virtuálisgép-méretezési csoportban lévő virtuálisgép-példány rendszerképét. |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/restart/action | Újraindít egy virtuálisgép-méretezési csoportban lévő virtuálisgép-példányt. |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/start/action | Elindít egy virtuálisgép-méretezési csoportban lévő virtuálisgép-példányt. |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write | Egy virtuálisgép-méretezési csoportban lévő virtuális gép tulajdonságainak frissítése |
> | Művelet | Microsoft.Compute/virtualMachineScaleSets/write | Új virtuálisgép-méretezési csoport létrehozása vagy egy meglévő frissítése |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.Consumption/balances/read | A számlázott időszak egy felügyeleti csoport összegzése kihasználtsága a listában. |
> | Művelet | Microsoft.Consumption/budgets/read | Az előfizetés vagy a felügyeleti csoport költségvetések listában. |
> | Művelet | Microsoft.Consumption/budgets/write | Hoz, frissítése és törlése a költségvetések előfizetés vagy a felügyeleti csoport. |
> | Művelet | Microsoft.Consumption/marketplaces/read | Egy hatókör EA és WebDirect előfizetésekhez piactér erőforrás látogatottsági adatait listázza. |
> | Művelet | Microsoft.Consumption/operations/read | Minden támogatott műveleteket Microsoft.Consumption erőforrás-szolgáltató a listában. |
> | Művelet | Microsoft.Consumption/pricesheets/read | Az előfizetés vagy egy felügyeleti csoport Pricesheets adatainak listában. |
> | Művelet | Microsoft.Consumption/reservationDetails/read | A szalagfelhasználási adatok foglalás sorrend, illetve a felügyeleti csoportok által fenntartott példányok felsorolása. A részletek adata nap szintenként példányonként. |
> | Művelet | Microsoft.Consumption/reservationRecommendations/read | Lista egyetlen vagy megosztott javaslatok-előfizetéshez tartozó foglalt példányok. |
> | Művelet | Microsoft.Consumption/reservationSummaries/read | Foglalási sorrend, illetve a felügyeleti csoportok által fenntartott példányok összefoglaló kihasználtsága a listában. Az összefoglaló adatokat vagy a napi vagy havi szinten. |
> | Művelet | Microsoft.Consumption/reservationTransactions/read | A tranzakció előzmények felügyeleti csoportok által fenntartott példányok felsorolása. |
> | Művelet | Microsoft.Consumption/terms/read | Listázza az előfizetés vagy a felügyeleti csoport feltételeit. |
> | Művelet | Microsoft.Consumption/usageDetails/read | A használat részleteiről EA és WebDirect előfizetésekhez hatókör listában. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.ContainerInstance/containerGroups/containers/logs/read | Egy adott tároló naplóinak beolvasása. |
> | Művelet | Microsoft.ContainerInstance/containerGroups/delete | Egy adott tárolócsoport törlése. |
> | Művelet | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read | A tárolócsoport diagnosztikai beállításának beolvasása. |
> | Művelet | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write | A tárolócsoport diagnosztikai beállításának létrehozása vagy frissítése. |
> | Művelet | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/metricDefinitions/read | A tárolócsoport elérhető metrikáinak beolvasása. |
> | Művelet | Microsoft.ContainerInstance/containerGroups/read | Az összes tárolócsoport beolvasása. |
> | Művelet | Microsoft.ContainerInstance/containerGroups/write | Egy adott tárolócsoport létrehozása vagy frissítése. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.ContainerRegistry/checkNameAvailability/read | Ellenőrzi, hogy a tároló neve használható. |
> | Művelet | Microsoft.ContainerRegistry/locations/operationResults/read | Lekérdezi egy aszinkron művelet eredménye |
> | Művelet | Microsoft.ContainerRegistry/operations/read | Felsorolja az összes Azure tároló beállításjegyzék REST API műveletek |
> | Művelet | Microsoft.ContainerRegistry/register/action | A tároló beállításjegyzék erőforrás-szolgáltató előfizetésének regisztrálása, és lehetővé teszi a tároló nyilvántartó létrehozását. |
> | Művelet | Microsoft.ContainerRegistry/registries/delete | Egy tároló beállításjegyzék törli. |
> | Művelet | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | Egy esemény rács szűrő töröl egy tároló beállításjegyzék. |
> | Művelet | Microsoft.ContainerRegistry/registries/eventGridFilters/read | A megadott rács szűrő tulajdonságait olvassa be, vagy a megadott tároló beállításjegyzék összes esemény rács szűrők listája. |
> | Művelet | Microsoft.ContainerRegistry/registries/eventGridFilters/write | Létrehozza vagy frissíti a tároló beállításjegyzék rács esemény szűrőjének a megadott paraméterekkel. |
> | Művelet | Microsoft.ContainerRegistry/registries/importImage/action | A megadott paraméterekkel tároló beállításjegyzék-lemezkép importálása. |
> | Művelet | Microsoft.ContainerRegistry/registries/listCredentials/action | Megjeleníti a megadott tároló beállításjegyzék bejelentkezési hitelesítő adataihoz. |
> | Művelet | Microsoft.ContainerRegistry/registries/listUsages/read | Megjeleníti a megadott tároló beállításjegyzék a a kvóta módjait. |
> | Művelet | Microsoft.ContainerRegistry/registries/operationStatuses/read | A beállításjegyzék aszinkron műveleti állapotának beolvasása |
> | Művelet | Microsoft.ContainerRegistry/registries/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Művelet | Microsoft.ContainerRegistry/registries/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Művelet | Microsoft.ContainerRegistry/registries/providers/Microsoft.Insights/metricDefinitions/read | A Microsoft ContainerRegistry elérhető metrikai meghatározások beolvasása |
> | Művelet | Microsoft.ContainerRegistry/registries/read | A megadott tároló beállításjegyzék tulajdonságait olvassa be, vagy a megadott erőforráscsoport vagy előfizetés összes tároló nyilvántartó sorolja fel. |
> | Művelet | Microsoft.ContainerRegistry/registries/regenerateCredential/action | A bejelentkezési hitelesítő adatait a megadott tároló beállításjegyzék egyik újragenerálása. |
> | Művelet | Microsoft.ContainerRegistry/registries/replications/delete | Egy tároló beállításjegyzék törli a replikációt. |
> | Művelet | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | A replikációs aszinkron műveleti állapotának beolvasása |
> | Művelet | Microsoft.ContainerRegistry/registries/replications/read | A megadott replikációs tulajdonságait olvassa be, vagy a megadott tároló rendszerleíró összes replikációk listáját. |
> | Művelet | Microsoft.ContainerRegistry/registries/replications/write | Létrehozza vagy frissíti a tároló beállításjegyzékbeli replikálása a megadott paraméterekkel. |
> | Művelet | Microsoft.ContainerRegistry/registries/webhooks/delete | A webhook törlése egy tároló beállításjegyzékből. |
> | Művelet | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | A konfigurációs szolgáltatás URI és az egyéni fejlécet lekérdezi a webhook. |
> | Művelet | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | A megadott webhook megőrző legutóbbi események listája. |
> | Művelet | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | A webhook aszinkron műveleti állapotának beolvasása |
> | Művelet | Microsoft.ContainerRegistry/registries/webhooks/ping/action | A webhook küldendő egy ping esemény váltja ki. |
> | Művelet | Microsoft.ContainerRegistry/registries/webhooks/read | A megadott webhook tulajdonságait olvassa be, vagy a megadott tároló rendszerleíró összes webhookok listáját. |
> | Művelet | Microsoft.ContainerRegistry/registries/webhooks/write | Létrehozza vagy frissíti a webhook a tároló beállításjegyzékbeli a megadott paraméterekkel. |
> | Művelet | Microsoft.ContainerRegistry/registries/write | Létrehozza vagy frissíti a tároló beállításjegyzékbeli a megadott paraméterekkel. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.ContainerService/containerServices/delete | A tárolószolgáltatás törlése |
> | Művelet | Microsoft.ContainerService/containerServices/read | A tárolószolgáltatás beolvasása |
> | Művelet | Microsoft.ContainerService/containerServices/write | Létrehoz egy új tároló szolgáltatást, vagy frissít egy meglévő |
> | Művelet | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Egy felügyelt fürt hozzáférési profil szolgáltatáshitelesítést szerepkörnév lista hitelesítő adat segítségével |
> | Művelet | Microsoft.ContainerService/managedClusters/accessProfiles/read | Egy felügyelt fürt hozzáférési profil szolgáltatáshitelesítést szerepkörnév |
> | Művelet | Microsoft.ContainerService/managedClusters/delete | Egy felügyelt fürt törlése |
> | Művelet | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/diagnosticSettings/read | Egy felügyelt fürt erőforrás diagnosztikai beállításának beolvasása |
> | Művelet | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti egy felügyelt fürt erőforrás diagnosztikai beállításának |
> | Művelet | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/metricDefinitions/read | Felügyelt fürt elérhető metrikai meghatározások beolvasása |
> | Művelet | Microsoft.ContainerService/managedClusters/read | Egy felügyelt fürt beolvasása |
> | Művelet | Microsoft.ContainerService/managedClusters/write | Egy új felügyelt fürtöt hoz létre vagy frissít egy meglévő |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.ContentModerator/applications/delete | Törlési művelet |
> | Művelet | Microsoft.ContentModerator/applications/listSecrets/action | Titkos kulcsok listázása |
> | Művelet | Microsoft.ContentModerator/applications/listSingleSignOnToken/action | Olvassa el a jogkivonatok az egyszeri bejelentkezés |
> | Művelet | Microsoft.ContentModerator/applications/read | Olvasási művelet |
> | Művelet | Microsoft.ContentModerator/applications/write | Írási művelet |
> | Művelet | Microsoft.ContentModerator/applications/write | Írási művelet |
> | Művelet | Microsoft.ContentModerator/listCommunicationPreference/action | Lista kommunikációs beállításokat |
> | Művelet | Microsoft.ContentModerator/operations/read | olvasási műveletek |
> | Művelet | Microsoft.ContentModerator/updateCommunicationPreference/action | Update kommunikációs beállítás |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.CustomerInsights/hubs/adobemetadata/action | Létrehozása vagy frissítése bármely Azure felhasználói Insights Adobe metaadatok |
> | Művelet | Microsoft.CustomerInsights/hubs/adobemetadata/read | Minden Azure felhasználói Insights Adobe metaadatot olvasása |
> | Művelet | Microsoft.CustomerInsights/hubs/authorizationPolicies/delete | Bármely Azure felhasználói Insights megosztott hozzáférési aláírást házirend törlése |
> | Művelet | Microsoft.CustomerInsights/hubs/authorizationPolicies/read | Azure felhasználói elemzéseket megosztott hozzáférési aláírást házirend olvasása |
> | Művelet | Microsoft.CustomerInsights/hubs/authorizationPolicies/regeneratePrimaryKey/action | Azure felhasználói Insights megosztott hozzáférési aláírást házirend elsődleges kulcs újragenerálása |
> | Művelet | Microsoft.CustomerInsights/hubs/authorizationPolicies/regenerateSecondaryKey/action | Azure felhasználói Insights megosztott hozzáférési aláírást házirend másodlagos kulcs újragenerálása |
> | Művelet | Microsoft.CustomerInsights/hubs/authorizationPolicies/write | Hozzon létre vagy bármely Azure felhasználói Insights megosztott hozzáférési aláírást házirend frissítése |
> | Művelet | Microsoft.CustomerInsights/hubs/connectors/activate/action | Bármely Azure felhasználói Insights-összekötő aktiválása |
> | Művelet | Microsoft.CustomerInsights/hubs/connectors/activate/action | Bármely Azure felhasználói Insights-összekötő aktiválása |
> | Művelet | Microsoft.CustomerInsights/hubs/connectors/delete | Bármely Azure felhasználói Insights-összekötő törlése |
> | Művelet | Microsoft.CustomerInsights/hubs/connectors/getruntimestatus/action | Bármely Azure felhasználói Insights-összekötő futási állapotának beolvasása |
> | Művelet | Microsoft.CustomerInsights/hubs/connectors/mappings/activate/action | Bármely Azure felhasználói Insights összekötő hozzárendelés aktiválásához |
> | Művelet | Microsoft.CustomerInsights/hubs/connectors/mappings/delete | Bármely Azure felhasználói Insights összekötő leképezés törlése |
> | Művelet | Microsoft.CustomerInsights/hubs/connectors/mappings/operations/read | Bármely Azure felhasználói Insights összekötő leképezési műveleti eredmény olvasása |
> | Művelet | Microsoft.CustomerInsights/hubs/connectors/mappings/read | Olvassa el a bármely Azure felhasználói Insights összekötő leképezése |
> | Művelet | Microsoft.CustomerInsights/hubs/connectors/mappings/write | Hozzon létre vagy frissítési bármely Azure felhasználói Insights összekötő leképezés |
> | Művelet | Microsoft.CustomerInsights/hubs/connectors/operations/read | Bármely Azure felhasználói Insights-összekötő műveleti eredmény olvasása |
> | Művelet | Microsoft.CustomerInsights/hubs/connectors/read | Olvassa el a bármely Azure felhasználói Insights-összekötő |
> | Művelet | Microsoft.CustomerInsights/hubs/connectors/saveauthinfo/action | Létrehozása vagy frissítése bármely Azure felhasználói Insights összekötő hitelesítési átjárókapcsolat adatai |
> | Művelet | Microsoft.CustomerInsights/hubs/connectors/update/action | Bármely Azure felhasználói Insights-összekötő frissítése |
> | Művelet | Microsoft.CustomerInsights/hubs/connectors/write | Létrehozása vagy frissítése bármely Azure felhasználói Insights-összekötő |
> | Művelet | Microsoft.CustomerInsights/hubs/crmmetadata/action | Létrehozni vagy frissíteni az Azure felhasználói Insights Crm-metaadatok |
> | Művelet | Microsoft.CustomerInsights/hubs/crmmetadata/read | Olvassa el az Azure felhasználói Insights Crm-metaadatok |
> | Művelet | Microsoft.CustomerInsights/hubs/delete | Bármely Azure felhasználói Insights Hub törlése |
> | Művelet | Microsoft.CustomerInsights/hubs/gdpr/delete | Bármely Azure felhasználói Insights Gdpr törlése |
> | Művelet | Microsoft.CustomerInsights/hubs/gdpr/read | Bármely Azure felhasználói Insights Gdpr olvasása |
> | Művelet | Microsoft.CustomerInsights/hubs/gdpr/write | Hozzon létre vagy bármely Azure felhasználói Insights Gdpr frissítése |
> | Művelet | Microsoft.CustomerInsights/hubs/getbillingcredits/read | Az Azure felhasználói Insights Hub számlázási jóváírásokat kap |
> | Művelet | Microsoft.CustomerInsights/hubs/getbillinghistory/read | Az Azure felhasználói Insights Hub számlázási előzmények beolvasása |
> | Művelet | Microsoft.CustomerInsights/hubs/images/delete | Bármely Azure felhasználói Insights lemezkép törlése |
> | Művelet | Microsoft.CustomerInsights/hubs/images/read | Olvassa el az Azure felhasználói Insights képet |
> | Művelet | Microsoft.CustomerInsights/hubs/images/write | Létrehozni vagy frissíteni az Azure felhasználói Insights képet |
> | Művelet | Microsoft.CustomerInsights/hubs/interactions/delete | Bármely azure Insights ügyfélkapcsolati törlése |
> | Művelet | Microsoft.CustomerInsights/hubs/interactions/operations/read | Bármely Azure Insights kapcsolat az ügyféllel műveleti eredmény olvasása |
> | Művelet | Microsoft.CustomerInsights/hubs/interactions/read | Olvassa el az Azure felhasználói Insights beavatkozás |
> | Művelet | Microsoft.CustomerInsights/hubs/interactions/suggestrelationshiplinks/action | Bármely Azure Insights ügyfélkapcsolati javasolni kapcsolat hivatkozások |
> | Művelet | Microsoft.CustomerInsights/hubs/interactions/write | Létrehozása vagy frissítése Insights Azure felhasználói beavatkozás |
> | Művelet | Microsoft.CustomerInsights/hubs/kpi/delete | Bármely Azure felhasználói Insights fő teljesítménymutató törlése |
> | Művelet | Microsoft.CustomerInsights/hubs/kpi/operations/read | Bármely Azure felhasználói Insights a fő teljesítménymutatók műveleti eredmény olvasása |
> | Művelet | Microsoft.CustomerInsights/hubs/kpi/read | Bármely Azure felhasználói Insights fő teljesítménymutató olvasása |
> | Művelet | Microsoft.CustomerInsights/hubs/kpi/reprocess/action | Bármely Azure felhasználói Insights fő teljesítménymutatók újból feldolgozza |
> | Művelet | Microsoft.CustomerInsights/hubs/kpi/write | Létrehozása vagy frissítése bármely Azure felhasználói Insights fő teljesítménymutató |
> | Művelet | Microsoft.CustomerInsights/hubs/links/delete | Bármely Azure felhasználói Insights hivatkozások törlése |
> | Művelet | Microsoft.CustomerInsights/hubs/links/operations/read | Bármely Azure felhasználói Insights hivatkozások műveleti eredmény olvasása |
> | Művelet | Microsoft.CustomerInsights/hubs/links/read | Olvassa el az Azure felhasználói Insights hivatkozások |
> | Művelet | Microsoft.CustomerInsights/hubs/links/write | Hozzon létre vagy a frissítés az Azure-ügyfél |
> | Művelet | Microsoft.CustomerInsights/hubs/msemetadata/action | Létrehozása vagy frissítése bármely Azure felhasználói Insights Mse metaadatok |
> | Művelet | Microsoft.CustomerInsights/hubs/msemetadata/read | Minden Azure felhasználói Insights Mse metaadatot olvasása |
> | Művelet | Microsoft.CustomerInsights/hubs/operationresults/read | Az Azure felhasználói Insights Hub művelet eredmények |
> | Művelet | Microsoft.CustomerInsights/hubs/predictions/delete | Bármely Azure felhasználói Insights előrejelzéseket törlése |
> | Művelet | Microsoft.CustomerInsights/hubs/predictions/operations/read | Bármely Azure felhasználói Insights előrejelzéseket műveleti eredmény olvasása |
> | Művelet | Microsoft.CustomerInsights/hubs/predictions/read | Bármely Azure felhasználói Insights előrejelzéseket olvasása |
> | Művelet | Microsoft.CustomerInsights/hubs/predictions/write | Létrehozása vagy frissítése bármely Azure felhasználói előrejelzés |
> | Művelet | Microsoft.CustomerInsights/hubs/predictivematchpolicies/delete | Bármely Azure felhasználói Insights prediktív egyezés házirendek törlése |
> | Művelet | Microsoft.CustomerInsights/hubs/predictivematchpolicies/operations/read | Bármely Azure felhasználói Insights prediktív egyezés házirendek műveleti eredmény olvasása |
> | Művelet | Microsoft.CustomerInsights/hubs/predictivematchpolicies/read | Minden Azure felhasználói Insights prediktív egyezés házirendet olvasása |
> | Művelet | Microsoft.CustomerInsights/hubs/predictivematchpolicies/write | Bármely Azure felhasználói Insights prediktív egyezés irányelveinek létrehozása vagy frissítése |
> | Művelet | Microsoft.CustomerInsights/hubs/profiles/delete | Bármely Azure felhasználói Insights profil törlése |
> | Művelet | Microsoft.CustomerInsights/hubs/profiles/operations/read | Bármely Azure felhasználói Insights profil műveleti eredmény olvasása |
> | Művelet | Microsoft.CustomerInsights/hubs/profiles/read | Bármely Azure felhasználói Insights profil olvasása |
> | Művelet | Microsoft.CustomerInsights/hubs/profiles/write | Minden Azure felhasználói Insights profil írása |
> | Művelet | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Művelet | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Művelet | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/logDefinitions/read | Naplók erőforrás beolvasása |
> | Művelet | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/metricDefinitions/read | Az erőforrás elérhető metrikai meghatározások beolvasása |
> | Művelet | Microsoft.CustomerInsights/hubs/read | Bármely Azure felhasználói Insights Hub olvasása |
> | Művelet | Microsoft.CustomerInsights/hubs/relationshiplinks/delete | Bármely Azure felhasználói Insights kapcsolat hivatkozások törlése |
> | Művelet | Microsoft.CustomerInsights/hubs/relationshiplinks/operations/read | Bármely Azure felhasználói Insights kapcsolat hivatkozások műveleti eredmény olvasása |
> | Művelet | Microsoft.CustomerInsights/hubs/relationshiplinks/read | Olvassa el az Azure felhasználói Insights kapcsolat hivatkozások |
> | Művelet | Microsoft.CustomerInsights/hubs/relationshiplinks/write | Hozzon létre vagy a frissítés az Azure felhasználói Insights kapcsolat |
> | Művelet | Microsoft.CustomerInsights/hubs/relationships/delete | Minden Azure felhasználói Insights kapcsolat törlése |
> | Művelet | Microsoft.CustomerInsights/hubs/relationships/operations/read | Bármely Azure Insights ügyfélkapcsolatok műveleti eredmény olvasása |
> | Művelet | Microsoft.CustomerInsights/hubs/relationships/read | Bármely Azure Insights ügyfélkapcsolatok olvasása |
> | Művelet | Microsoft.CustomerInsights/hubs/relationships/write | Létrehozása vagy frissítése bármely Azure Insights ügyfélkapcsolatok |
> | Művelet | Microsoft.CustomerInsights/hubs/roleAssignments/delete | Bármely Azure felhasználói Insights Rbac-hozzárendelés törlése |
> | Művelet | Microsoft.CustomerInsights/hubs/roleAssignments/operations/read | Bármely Azure felhasználói Insights Rbac-hozzárendelés műveleti eredmény olvasása |
> | Művelet | Microsoft.CustomerInsights/hubs/roleAssignments/read | Olvassa el a bármely Azure felhasználói Insights Rbac-hozzárendelés |
> | Művelet | Microsoft.CustomerInsights/hubs/roleAssignments/write | Hozzon létre, vagy bármely Azure felhasználói Insights Rbac-hozzárendelés módosítása |
> | Művelet | Microsoft.CustomerInsights/hubs/roles/read | Olvassa el a bármely Azure felhasználói Insights Rbac-szerepkörök |
> | Művelet | Microsoft.CustomerInsights/hubs/salesforcemetadata/action | Létrehozása vagy frissítése bármely Azure felhasználói Insights SalesForce metaadatok |
> | Művelet | Microsoft.CustomerInsights/hubs/salesforcemetadata/read | Minden Azure felhasználói Insights SalesForce metaadatot olvasása |
> | Művelet | Microsoft.CustomerInsights/hubs/segments/delete | Bármely Azure Insights ügyfélszegmensek törlése |
> | Művelet | Microsoft.CustomerInsights/hubs/segments/dynamic/action | Felügyeleti bármely Azure felhasználói Insight dinamikus szegmensek |
> | Művelet | Microsoft.CustomerInsights/hubs/segments/read | Bármely Azure Insights ügyfélszegmensek olvasása |
> | Művelet | Microsoft.CustomerInsights/hubs/segments/static/action | Felügyeleti bármely Azure felhasználói Insight statikus szegmensek |
> | Művelet | Microsoft.CustomerInsights/hubs/segments/write | Bármely Azure felhasználói Insights szegmensek létrehozása és módosítása |
> | Művelet | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/delete | Bármely Azure felhasználói Insights SqlConnectionStrings törlése |
> | Művelet | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/read | Bármely Azure felhasználói Insights SqlConnectionStrings olvasása |
> | Művelet | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/write | Hozzon létre vagy bármely Azure felhasználói Insights SqlConnectionStrings frissítése |
> | Művelet | Microsoft.CustomerInsights/hubs/suggesttypeschema/action | Hozzon létre típus séma felajánlja a mintaadatokat |
> | Művelet | Microsoft.CustomerInsights/hubs/tenantmanagement/read | Bármely Azure felhasználói Insights hub beállításainak kezelése |
> | Művelet | Microsoft.CustomerInsights/hubs/views/delete | Bármely Azure felhasználói Insights alkalmazás nézet törlése |
> | Művelet | Microsoft.CustomerInsights/hubs/views/read | Olvassa el a bármely Azure felhasználói Insights alkalmazás megtekintése |
> | Művelet | Microsoft.CustomerInsights/hubs/views/write | Hozzon létre vagy bármely Azure felhasználói Insights alkalmazás nézet frissítése |
> | Művelet | Microsoft.CustomerInsights/hubs/widgettypes/read | Olvassa el a bármely Azure felhasználói Insights App Widget típusa |
> | Művelet | Microsoft.CustomerInsights/hubs/write | Hozzon létre vagy bármely Azure felhasználói Insights központ frissítése |
> | Művelet | Microsoft.CustomerInsights/operations/read | Olvassa el az Azure felhasználói Insights Api Metadatas |
> | Művelet | Microsoft.CustomerInsights/register/action | Az ügyfél Insights erőforrás-szolgáltató előfizetésének regisztrálása és létrehozását lehetővé tevő ügyfél Insights-erőforrások |
> | Művelet | Microsoft.CustomerInsights/unregister/action | Az ügyfél Insights erőforrás-szolgáltató az előfizetés regisztrációjának törlése |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.Databricks/workspaces/delete | Eltávolítja az munkaterülethez. |
> | Művelet | Microsoft.Databricks/workspaces/read | A munkaterületek listájának beolvasása. |
> | Művelet | Microsoft.Databricks/workspaces/write | Egy munkaterület létrehozása |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.DataCatalog/catalogs/delete | Törli a katalógusban. |
> | Művelet | Microsoft.DataCatalog/catalogs/read | Tulajdonságok beolvasása katalógus vagy a katalógusban az előfizetéshez vagy erőforráscsoporthoz. |
> | Művelet | Microsoft.DataCatalog/catalogs/write | Katalógus létrehozza vagy frissíti, a címkéket és a katalógus tulajdonságait. |
> | Művelet | Microsoft.DataCatalog/checkNameAvailability/action | Bérlő katalógus neve rendelkezésre állását ellenőrzi. |
> | Művelet | Microsoft.DataCatalog/operations/read | Microsoft.DataCatalog erőforrás-szolgáltató elérhető műveleteinek listázása. |
> | Művelet | Microsoft.DataCatalog/register/action | Az előfizetés regisztrálása a Microsoft.DataCatalog erőforrás-szolgáltató. |
> | Művelet | Microsoft.DataCatalog/unregister/action | Előfizetés Microsoft.DataCatalog erőforrás-szolgáltató regisztrációjának törlése. |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.DataFactory/datafactories/activitywindows/read | Olvassa be a tevékenység Windows adat-előállító megadott paraméterekkel. |
> | Művelet | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | Tevékenység Windows beolvasása az adatcsatorna tevékenységhez megadott paraméterekkel. |
> | Művelet | Microsoft.DataFactory/datafactories/datapipelines/activitywindows/read | Tevékenység Windows beolvassa a folyamat a megadott paraméterekkel. |
> | Művelet | Microsoft.DataFactory/datafactories/datapipelines/delete | A feldolgozási sor törlése. |
> | Művelet | Microsoft.DataFactory/datafactories/datapipelines/pause/action | Minden folyamatkezelési felfüggesztése. |
> | Művelet | Microsoft.DataFactory/datafactories/datapipelines/read | Minden folyamatkezelési beolvasása. |
> | Művelet | Microsoft.DataFactory/datafactories/datapipelines/resume/action | A folyamat folytatódik. |
> | Művelet | Microsoft.DataFactory/datafactories/datapipelines/update/action | A folyamat frissíti. |
> | Művelet | Microsoft.DataFactory/datafactories/datapipelines/write | Létrehozza vagy frissíti a folyamat. |
> | Művelet | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | Tevékenység Windows beolvassa az adatkészlet megadott paraméterekkel. |
> | Művelet | Microsoft.DataFactory/datafactories/datasets/delete | A Dataset törli. |
> | Művelet | Microsoft.DataFactory/datafactories/datasets/read | A Dataset beolvasása. |
> | Művelet | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | Beolvassa a szelet-futtassa a megadott kezdési időponttal adott adatkészlet. |
> | Művelet | Microsoft.DataFactory/datafactories/datasets/slices/read | Lekérdezi a Adatszeletek az adott időszakban. |
> | Művelet | Microsoft.DataFactory/datafactories/datasets/slices/write | Az Adatszelet állapotának frissítése. |
> | Művelet | Microsoft.DataFactory/datafactories/datasets/write | Létrehozza vagy frissíti a Dataset. |
> | Művelet | Microsoft.DataFactory/datafactories/delete | A Data Factory törlése. |
> | Művelet | Microsoft.DataFactory/datafactories/gateways/connectioninfo/action | Minden átjáró olvassa be a kapcsolat adatai. |
> | Művelet | Microsoft.DataFactory/datafactories/gateways/delete | Törli az összes átjáró. |
> | Művelet | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | Minden átjáró jeleníti meg a hitelesítési kulcsokat. |
> | Művelet | Microsoft.DataFactory/datafactories/gateways/read | Minden átjáró beolvasása. |
> | Művelet | Microsoft.DataFactory/datafactories/gateways/regenerateauthkey/action | Minden átjáró hitelesítési kulcsainak újragenerálása. |
> | Művelet | Microsoft.DataFactory/datafactories/gateways/write | Létrehozza vagy frissíti az összes átjáró. |
> | Művelet | Microsoft.DataFactory/datafactories/linkedServices/delete | Törli a társított szolgáltatás. |
> | Művelet | Microsoft.DataFactory/datafactories/linkedServices/read | Beolvassa a társított szolgáltatás. |
> | Művelet | Microsoft.DataFactory/datafactories/linkedServices/write | Létrehozza vagy frissíti a társított szolgáltatás. |
> | Művelet | Microsoft.DataFactory/datafactories/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Művelet | Microsoft.DataFactory/datafactories/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Művelet | Microsoft.DataFactory/datafactories/providers/Microsoft.Insights/metricDefinitions/read | A datafactories elérhető metrikai meghatározások beolvasása |
> | Művelet | Microsoft.DataFactory/datafactories/read | Beolvassa az adat-Előállítóban. |
> | Művelet | Microsoft.DataFactory/datafactories/runs/loginfo/read | Beolvassa a naplókat tartalmazó blob tárolóra SAS URI. |
> | Művelet | Microsoft.DataFactory/datafactories/tables/delete | A Dataset törli. |
> | Művelet | Microsoft.DataFactory/datafactories/tables/read | A Dataset beolvasása. |
> | Művelet | Microsoft.DataFactory/datafactories/tables/write | Létrehozza vagy frissíti a Dataset. |
> | Művelet | Microsoft.DataFactory/datafactories/write | Létrehozza vagy frissíti az adat-Előállítóban. |
> | Művelet | Microsoft.DataFactory/factories/cancelpipelinerun/action | Futtassa a futtatási azonosító által a folyamat megszakítása |
> | Művelet | Microsoft.DataFactory/factories/datasets/delete | A Dataset törli. |
> | Művelet | Microsoft.DataFactory/factories/datasets/read | A Dataset beolvasása. |
> | Művelet | Microsoft.DataFactory/factories/datasets/write | Létrehozza vagy frissíti a Dataset. |
> | Művelet | Microsoft.DataFactory/factories/delete | Törli az adat-Előállítóban. |
> | Művelet | Microsoft.DataFactory/factories/integrationruntimes/delete | Törli az összes integrációs futásidejű. |
> | Művelet | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/read | Integrációs futásidejű Kapcsolatinformáció beolvasása. |
> | Művelet | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | Integráció futási állapotának beolvasása. |
> | Művelet | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | Minden integrációs futásidejű jeleníti meg a hitelesítési kulcsokat. |
> | Művelet | Microsoft.DataFactory/factories/integrationruntimes/monitoringdata/read | Minden integrációs futásidejű lekéri a figyelési adatokat. |
> | Művelet | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | A csomópont törlése a megadott integrációs futásidejű. |
> | Művelet | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | A megadott csomópont integrációs futásidejű az IP-címet adja vissza. |
> | Művelet | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | Egy önálló üzemeltetett integrációs futásidejű csomópont frissíti. |
> | Művelet | Microsoft.DataFactory/factories/integrationruntimes/read | Minden integrációs futásidejű beolvasása. |
> | Művelet | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | A megadott integrációs futási időben hitelesítési kulcsainak újragenerálása. |
> | Művelet | Microsoft.DataFactory/factories/integrationruntimes/start/action | Minden integrációs futásidejű kezdődik. |
> | Művelet | Microsoft.DataFactory/factories/integrationruntimes/stop/action | Minden integrációs futásidejű leáll. |
> | Művelet | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | A hitelesítő adatok szinkronizálásának a megadott integrációs futási időben. |
> | Művelet | Microsoft.DataFactory/factories/integrationruntimes/upgrade/action | A megadott integrációs futásidejű frissíti. |
> | Művelet | Microsoft.DataFactory/factories/integrationruntimes/write | Létrehozza vagy frissíti az összes integrációs futásidejű. |
> | Művelet | Microsoft.DataFactory/factories/linkedServices/delete | Törli a társított szolgáltatás. |
> | Művelet | Microsoft.DataFactory/factories/linkedServices/read | Olvasási társított szolgáltatás. |
> | Művelet | Microsoft.DataFactory/factories/linkedServices/write | Létrehozni vagy frissíteni a társított szolgáltatás |
> | Művelet | Microsoft.DataFactory/factories/pipelineruns/activityruns/read | Beolvassa a tevékenység futtatása a megadott feldolgozási folyamat futtatásához azonosítóját. |
> | Művelet | Microsoft.DataFactory/factories/pipelineruns/read | A folyamat fut beolvasása. |
> | Művelet | Microsoft.DataFactory/factories/pipelines/createrun/action | A tölcsér hoz létre a kísérletek. |
> | Művelet | Microsoft.DataFactory/factories/pipelines/delete | Feldolgozási sor törlése. |
> | Művelet | Microsoft.DataFactory/factories/pipelines/read | Olvassa be a folyamatot. |
> | Művelet | Microsoft.DataFactory/factories/pipelines/write | Létrehozni vagy frissíteni a feldolgozási sor |
> | Művelet | Microsoft.DataFactory/factories/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Művelet | Microsoft.DataFactory/factories/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Művelet | Microsoft.DataFactory/factories/providers/Microsoft.Insights/logDefinitions/read | Előállítók naplók beolvasása. |
> | Művelet | Microsoft.DataFactory/factories/providers/Microsoft.Insights/metricDefinitions/read | Az előállítók elérhető metrikai meghatározások beolvasása |
> | Művelet | Microsoft.DataFactory/factories/read | Olvassa be az adat-Előállítóban. |
> | Művelet | Microsoft.DataFactory/factories/triggers/delete | Törli az összes eseményindító. |
> | Művelet | Microsoft.DataFactory/factories/triggers/read | Bármely eseményindító beolvasása. |
> | Művelet | Microsoft.DataFactory/factories/triggers/start/action | Bármely eseményindító kezdődik. |
> | Művelet | Microsoft.DataFactory/factories/triggers/stop/action | Leállítja az összes eseményindító. |
> | Művelet | Microsoft.DataFactory/factories/triggers/triggerruns/read | Az eseményindító fut beolvasása. |
> | Művelet | Microsoft.DataFactory/factories/triggers/write | Létrehozza vagy frissíti az összes eseményindító. |
> | Művelet | Microsoft.DataFactory/factories/write | Hozzon létre vagy adat-előállító frissítése |
> | Művelet | Microsoft.DataFactory/register/action | A Data Factory erőforrás-szolgáltató előfizetésének regisztrálása. |
> | Művelet | Microsoft.DataFactory/unregister/action | A Data Factory erőforrás-szolgáltató az előfizetés regisztrációjának törlése. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | Számítás-házirend törlése. |
> | Művelet | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | A számítási házirenddel kapcsolatos információk. |
> | Művelet | Microsoft.DataLakeAnalytics/accounts/computePolicies/write | Hozzon létre, vagy egy számítási házirend frissítése. |
> | Művelet | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | Megszünteti egy DataLakeStore fiók DataLakeAnalytics-fiókból. |
> | Művelet | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | A csatolt DataLakeStore fiók DataLakeAnalytics fiók adatainak beolvasása. |
> | Művelet | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | Fiók létrehozása vagy frissítése egy csatolt DataLakeStore DataLakeAnalytics fiók. |
> | Művelet | Microsoft.DataLakeAnalytics/accounts/delete | DataLakeAnalytics fiók törlése. |
> | Művelet | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | Tűzfalszabály törlése. |
> | Művelet | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | Egy tűzfalszabály adatainak beolvasása. |
> | Művelet | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | Hozzon létre, vagy egy tűzfalszabály módosítása. |
> | Művelet | Microsoft.DataLakeAnalytics/accounts/operationResults/read | Első DataLakeAnalytics fiók művelet eredménye. |
> | Művelet | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/diagnosticSettings/read | A DataLakeAnalytics fiók diagnosztikai beállításainak beolvasása. |
> | Művelet | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/diagnosticSettings/write | Hozzon létre, vagy frissítse a diagnosztikai beállításokat a DataLakeAnalytics fiók. |
> | Művelet | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/logDefinitions/read | A naplók lekérése az DataLakeAnalytics fiók. |
> | Művelet | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/metricDefinitions/read | Az elérhető mérőszámok lekérni a DataLakeAnalytics fiókhoz. |
> | Művelet | Microsoft.DataLakeAnalytics/accounts/read | Meglévő DataLakeAnalytics fiók adatainak beolvasása. |
> | Művelet | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | A tároló DataLakeAnalytics fiók csatolt tárfiók SAS-tokenje listában. |
> | Művelet | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | A csatolt tárfiók DataLakeAnalytics fiók tárolók beolvasása. |
> | Művelet | Microsoft.DataLakeAnalytics/accounts/storageAccounts/delete | A Storage-fiók egy DataLakeAnalytics fiókból választható. |
> | Művelet | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | A csatolt tárfiók DataLakeAnalytics fiók adatainak beolvasása. |
> | Művelet | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | Hozzon létre, vagy egy kapcsolódó tárfiók DataLakeAnalytics fiók módosítása. |
> | Művelet | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Más felhasználók által benyújtott szakítsa engedélyeket. |
> | Művelet | Microsoft.DataLakeAnalytics/accounts/write | Fiók létrehozása vagy frissítése egy DataLakeAnalytics. |
> | Művelet | Microsoft.DataLakeAnalytics/locations/capability/read | Előfizetés funkció adatainak DataLakeAnalytics használatával kapcsolatban. |
> | Művelet | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | Egy DataLakeAnalytics fióknevet elérhetőségének ellenőrzése. |
> | Művelet | Microsoft.DataLakeAnalytics/locations/operationResults/read | Első DataLakeAnalytics fiók művelet eredménye. |
> | Művelet | Microsoft.DataLakeAnalytics/operations/read | A DataLakeAnalytics elérhető műveleteinek beolvasása. |
> | Művelet | Microsoft.DataLakeAnalytics/register/action | Előfizetés a DataLakeAnalytics regisztrálja. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.DataLakeStore/accounts/delete | DataLakeStore fiók törlése. |
> | Művelet | Microsoft.DataLakeStore/accounts/enableKeyVault/action | Engedélyezze a KeyVault DataLakeStore fiók. |
> | Művelet | Microsoft.DataLakeStore/accounts/firewallRules/delete | Tűzfalszabály törlése. |
> | Művelet | Microsoft.DataLakeStore/accounts/firewallRules/read | Egy tűzfalszabály adatainak beolvasása. |
> | Művelet | Microsoft.DataLakeStore/accounts/firewallRules/write | Hozzon létre, vagy egy tűzfalszabály módosítása. |
> | Művelet | Microsoft.DataLakeStore/accounts/operationResults/read | Első DataLakeStore fiók művelet eredménye. |
> | Művelet | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/diagnosticSettings/read | A DataLakeStore fiók diagnosztikai beállításainak beolvasása. |
> | Művelet | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/diagnosticSettings/write | Hozzon létre, vagy frissítse a diagnosztikai beállításokat a DataLakeStore fiók. |
> | Művelet | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/logDefinitions/read | A naplók lekérése az DataLakeStore fiók. |
> | Művelet | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/metricDefinitions/read | Az elérhető mérőszámok lekérni a DataLakeStore fiókhoz. |
> | Művelet | Microsoft.DataLakeStore/accounts/read | Meglévő DataLakeStore fiók adatainak beolvasása. |
> | Művelet | Microsoft.DataLakeStore/accounts/Superuser/action | Adja meg a Data Lake Store Microsoft.Authorization/roleAssignments/write megadását felügyelő. |
> | Művelet | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | Egy megbízható identitásszolgáltató törlése. |
> | Művelet | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | Egy megbízható identitásszolgáltató adatainak beolvasása. |
> | Művelet | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | Hozzon létre, vagy egy megbízható identitásszolgáltató frissítése. |
> | Művelet | Microsoft.DataLakeStore/accounts/write | Fiók létrehozása vagy frissítése egy DataLakeStore. |
> | Művelet | Microsoft.DataLakeStore/locations/capability/read | Előfizetés funkció adatainak DataLakeStore használatával kapcsolatban. |
> | Művelet | Microsoft.DataLakeStore/locations/checkNameAvailability/action | Egy DataLakeStore fióknevet elérhetőségének ellenőrzése. |
> | Művelet | Microsoft.DataLakeStore/locations/operationResults/read | Első DataLakeStore fiók művelet eredménye. |
> | Művelet | Microsoft.DataLakeStore/operations/read | A DataLakeStore elérhető műveleteinek beolvasása. |
> | Művelet | Microsoft.DataLakeStore/register/action | Előfizetés a DataLakeStore regisztrálja. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.DBforMySQL/locations/performanceTiers/read | A teljesítmény rétegek rendelkezésre álló listát ad vissza. |
> | Művelet | Microsoft.DBforMySQL/performanceTiers/read | A teljesítmény rétegek rendelkezésre álló listát ad vissza. |
> | Művelet | Microsoft.DBforMySQL/servers/delete | Egy meglévő kiszolgáló törlése. |
> | Művelet | Microsoft.DBforMySQL/servers/firewallRules/delete | Meglévő tűzfalszabály törlése. |
> | Művelet | Microsoft.DBforMySQL/servers/firewallRules/read | Tűzfal listájának kiszolgáló szabályok vagy a megadott tűzfalszabály tulajdonságainak beolvasása vissza. |
> | Művelet | Microsoft.DBforMySQL/servers/firewallRules/write | A megadott paraméterek vagy a meglévő szabály módosítása egy olyan tűzfalszabályt hoz létre. |
> | Művelet | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Lekérdezi az erőforráshoz tartozó disagnostic beállításai |
> | Művelet | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Művelet | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Térjen vissza a típusú ítélt adatbázisokhoz |
> | Művelet | Microsoft.DBforMySQL/servers/read | A kiszolgálók vagy lekérdezi a megadott kiszolgálóhoz tartozó tulajdonságok listáját adja vissza. |
> | Művelet | Microsoft.DBforMySQL/servers/recoverableServers/read | A helyreállítható MySQL kiszolgáló adatait adja vissza |
> | Művelet | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | Egy meglévő virtuális hálózat szabály törlése |
> | Művelet | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | Térjen vissza a virtuális hálózat szabályok vagy a megadott virtuális hálózati szabály tulajdonságainak beolvasása. |
> | Művelet | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | A virtuális hálózati szabályt hoz létre a megadott paraméterekkel, vagy a Tulajdonságok vagy a megadott virtuális hálózati szabály címkék frissítése. |
> | Művelet | Microsoft.DBforMySQL/servers/write | Kiszolgáló létrehozása a megadott paraméterekkel, vagy frissítse a Tulajdonságok vagy a címkék a megadott kiszolgáló. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | A teljesítmény rétegek rendelkezésre álló listát ad vissza. |
> | Művelet | Microsoft.DBforPostgreSQL/performanceTiers/read | A teljesítmény rétegek rendelkezésre álló listát ad vissza. |
> | Művelet | Microsoft.DBforPostgreSQL/servers/delete | Egy meglévő kiszolgáló törlése. |
> | Művelet | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | Meglévő tűzfalszabály törlése. |
> | Művelet | Microsoft.DBforPostgreSQL/servers/firewallRules/read | Tűzfal listájának kiszolgáló szabályok vagy a megadott tűzfalszabály tulajdonságainak beolvasása vissza. |
> | Művelet | Microsoft.DBforPostgreSQL/servers/firewallRules/write | A megadott paraméterek vagy a meglévő szabály módosítása egy olyan tűzfalszabályt hoz létre. |
> | Művelet | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Lekérdezi az erőforráshoz tartozó disagnostic beállításai |
> | Művelet | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Művelet | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | Postgres kiszolgálók naplók beolvasása. |
> | Művelet | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Térjen vissza a típusú ítélt adatbázisokhoz |
> | Művelet | Microsoft.DBforPostgreSQL/servers/read | A kiszolgálók vagy lekérdezi a megadott kiszolgálóhoz tartozó tulajdonságok listáját adja vissza. |
> | Művelet | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | A helyreállítható PostgreSQL-kiszolgáló adatait adja vissza |
> | Művelet | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | A megadott kiszolgálón konfigurált kiszolgáló fenyegetés észlelési házirend részleteinek beolvasása |
> | Művelet | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | A kiszolgáló fenyegetés szabályzat egy adott kiszolgáló módosítása |
> | Művelet | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | Egy meglévő virtuális hálózat szabály törlése |
> | Művelet | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | Térjen vissza a virtuális hálózat szabályok vagy a megadott virtuális hálózati szabály tulajdonságainak beolvasása. |
> | Művelet | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | A virtuális hálózati szabályt hoz létre a megadott paraméterekkel, vagy a Tulajdonságok vagy a megadott virtuális hálózati szabály címkék frissítése. |
> | Művelet | Microsoft.DBforPostgreSQL/servers/write | Kiszolgáló létrehozása a megadott paraméterekkel, vagy frissítse a Tulajdonságok vagy a címkék a megadott kiszolgáló. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.Devices/checkNameAvailability/Action | Ellenőrizze a rendelkezésre álló Ha IOT hubbal név |
> | Művelet | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Ellenőrizze a rendelkezésre álló Ha IOT hubbal név |
> | Művelet | Microsoft.Devices/ElasticPools/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Művelet | Microsoft.Devices/ElasticPools/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Művelet | Microsoft.Devices/elasticPools/iotHubTenants/Delete | Az IOT hubbal bérlő erőforrás törlése |
> | Művelet | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Művelet | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Művelet | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | Az EventHub felhasználói csoport törlése |
> | Művelet | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | Az EventHub fogyasztói csoportot beolvasása |
> | Művelet | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | Az EventHub felhasználói csoport létrehozása |
> | Művelet | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | Eszközök exportálása |
> | Művelet | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | Az IOT hubbal bérlői statisztikák erőforrás beolvasása |
> | Művelet | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | Eszközök importálása |
> | Művelet | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | Az IOT hubbal bérlői kulcs beolvasása |
> | Művelet | Microsoft.Devices/elasticPools/iotHubTenants/jobs/Read | Ezzel a feladat a megadott IOT hubbal beküldött adatokat |
> | Művelet | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | Lekérdezi a bérlőkulcsok IOT hubbal |
> | Művelet | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | Lekérdezi az IOT hubbal szolgáltatáshoz elérhető naplófájl-definíciói |
> | Művelet | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | Az IOT hubbal szolgáltatáshoz elérhető metrikai meghatározások beolvasása |
> | Művelet | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | Kvóta metrikák beolvasása |
> | Művelet | Microsoft.Devices/elasticPools/iotHubTenants/Read | Az IOT hubbal bérlő erőforrás beolvasása |
> | Művelet | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | A tesztüzenet szemben az összes meglévő útvonal |
> | Művelet | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | A tesztüzenet szemben a megadott útvonal tesztelése |
> | Művelet | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | Az IOT hubbal az összes útválasztási végpontok állapotának beolvasása |
> | Művelet | Microsoft.Devices/elasticPools/iotHubTenants/Write | Létrehozni vagy frissíteni az IOT hubbal bérlő erőforrás |
> | Művelet | Microsoft.Devices/ElasticPools/metricDefinitions/read | Az IOT hubbal szolgáltatáshoz elérhető metrikai meghatározások beolvasása |
> | Művelet | Microsoft.Devices/iotHubs/certificates/Delete | Tanúsítvány törlése |
> | Művelet | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | Ellenőrző kód készítése |
> | Művelet | Microsoft.Devices/iotHubs/certificates/Read | A tanúsítvány beolvasása |
> | Művelet | Microsoft.Devices/iotHubs/certificates/verify/Action | Ellenőrizze az erőforrás-tanúsítvány |
> | Művelet | Microsoft.Devices/iotHubs/certificates/Write | Létrehozni vagy frissíteni a tanúsítvány |
> | Művelet | Microsoft.Devices/iotHubs/Delete | IOT hubbal erőforrás törlése |
> | Művelet | Microsoft.Devices/IotHubs/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Művelet | Microsoft.Devices/IotHubs/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Művelet | Microsoft.Devices/iotHubs/eventGridFilters/Delete | Az esemény rács szűrő törlése |
> | Művelet | Microsoft.Devices/iotHubs/eventGridFilters/Read | Lekérdezi a esemény rács szűrő |
> | Művelet | Microsoft.Devices/iotHubs/eventGridFilters/Write | Új vagy meglévő esemény rács szűrő frissítése |
> | Művelet | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | Az EventHub felhasználói csoport törlése |
> | Művelet | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | Az EventHub fogyasztói csoportot beolvasása |
> | Művelet | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | Az EventHub felhasználói csoport létrehozása |
> | Művelet | Microsoft.Devices/iotHubs/exportDevices/Action | Eszközök exportálása |
> | Művelet | Microsoft.Devices/iotHubs/importDevices/Action | Eszközök importálása |
> | Művelet | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | A megadott névvel az IOT hubbal kulcs beszerzése |
> | Művelet | Microsoft.Devices/iotHubs/iotHubStats/Read | Megtekintheti a statisztikákat IOT hubbal |
> | Művelet | Microsoft.Devices/iotHubs/jobs/Read | Ezzel a feladat a megadott IOT hubbal beküldött adatokat |
> | Művelet | Microsoft.Devices/iotHubs/listkeys/Action | Minden IOT hubbal kulcs beszerzése |
> | Művelet | Microsoft.Devices/IotHubs/logDefinitions/read | Lekérdezi az IOT hubbal szolgáltatáshoz elérhető naplófájl-definíciói |
> | Művelet | Microsoft.Devices/IotHubs/metricDefinitions/read | Az IOT hubbal szolgáltatáshoz elérhető metrikai meghatározások beolvasása |
> | Művelet | Microsoft.Devices/iotHubs/quotaMetrics/Read | Kvóta metrikák beolvasása |
> | Művelet | Microsoft.Devices/iotHubs/Read | Lekérdezi az IOT hubbal (oka) t |
> | Művelet | Microsoft.Devices/iotHubs/routing/$testall/Action | A tesztüzenet szemben az összes meglévő útvonal |
> | Művelet | Microsoft.Devices/iotHubs/routing/$testnew/Action | A tesztüzenet szemben a megadott útvonal tesztelése |
> | Művelet | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | Az IOT hubbal az összes útválasztási végpontok állapotának beolvasása |
> | Művelet | Microsoft.Devices/iotHubs/skus/Read | Érvényes IOT hubbal termékváltozatok beolvasása |
> | Művelet | Microsoft.Devices/iotHubs/Write | Létrehozni vagy frissíteni az IOT hubbal erőforrás |
> | Művelet | Microsoft.Devices/operations/Read | Minden ResourceProvider műveletek beolvasása |
> | Művelet | Microsoft.Devices/provisioningServices/certificates/Delete | Tanúsítvány törlése |
> | Művelet | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | Ellenőrző kód készítése |
> | Művelet | Microsoft.Devices/provisioningServices/certificates/Read | A tanúsítvány beolvasása |
> | Művelet | Microsoft.Devices/provisioningServices/certificates/verify/Action | Ellenőrizze az erőforrás-tanúsítvány |
> | Művelet | Microsoft.Devices/provisioningServices/certificates/Write | Létrehozni vagy frissíteni a tanúsítvány |
> | Művelet | Microsoft.Devices/provisioningServices/Delete | IotDps erőforrás törlése |
> | Művelet | Microsoft.Devices/provisioningServices/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Művelet | Microsoft.Devices/provisioningServices/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Művelet | Microsoft.Devices/provisioningServices/listkeys/Action | Minden IotDps kulcs beszerzése |
> | Művelet | Microsoft.Devices/provisioningServices/logDefinitions/read | Lekérdezi a létesítési szolgáltatás elérhető naplófájl-definíciói |
> | Művelet | Microsoft.Devices/provisioningServices/metricDefinitions/read | A létesítési szolgáltatás elérhető metrikai meghatározások beolvasása |
> | Művelet | Microsoft.Devices/provisioningServices/ProvisioningServiceKeys/listkeys/Action | Kulcsnév IotDps kulcsok beszerzése |
> | Művelet | Microsoft.Devices/provisioningServices/Read | IotDps erőforráskészlet |
> | Művelet | Microsoft.Devices/provisioningServices/skus/Read | Érvényes IotDps termékváltozatok beolvasása |
> | Művelet | Microsoft.Devices/provisioningServices/Write | IotDps erőforrás létrehozása |
> | Művelet | Microsoft.Devices/register/action | Az IOT hubbal erőforrás-szolgáltató előfizetésének regisztrálása és létrehozását lehetővé tevő erőforrások IOT hubbal |
> | Művelet | Microsoft.Devices/register/action | Az IOT hubbal erőforrás-szolgáltató előfizetésének regisztrálása és létrehozását lehetővé tevő erőforrások IOT hubbal |
> | Művelet | Microsoft.Devices/usages/Read | Ezzel előfizetés használati adatokat a szolgáltató. |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.DevTestLab/labCenters/delete | Labor erőforrások törlése. |
> | Művelet | Microsoft.DevTestLab/labCenters/read | Olvassa el a labor adatközpontokban. |
> | Művelet | Microsoft.DevTestLab/labCenters/write | Hozzáadása vagy módosítása a labor adatközpontokban. |
> | Művelet | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | Olvassa el az azure resource manager-sablonok. |
> | Művelet | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | Állít elő, az adott összetevő egy ARM-sablon, feltölti a szükséges fájlok egy tárfiókot, és érvényesíti a generált összetevő. |
> | Művelet | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | Olvassa el az összetevők. |
> | Művelet | Microsoft.DevTestLab/labs/artifactSources/delete | Összetevő források törlése. |
> | Művelet | Microsoft.DevTestLab/labs/artifactSources/read | Olvassa el a összetevő források. |
> | Művelet | Microsoft.DevTestLab/labs/artifactSources/write | Hozzáadása vagy módosítása összetevő források. |
> | Művelet | Microsoft.DevTestLab/labs/ClaimAnyVm/action | Jogcím egy véletlenszerű claimable virtuális gép a tesztkörnyezetben. |
> | Művelet | Microsoft.DevTestLab/labs/costs/read | Olvassa el a költségeket. |
> | Művelet | Microsoft.DevTestLab/labs/costs/write | Adja hozzá, és módosíthatja az. |
> | Művelet | Microsoft.DevTestLab/labs/CreateEnvironment/action | Virtuális gépek létrehozása egy tesztkörnyezetben. |
> | Művelet | Microsoft.DevTestLab/labs/customImages/delete | Törölje az egyéni lemezképet. |
> | Művelet | Microsoft.DevTestLab/labs/customImages/read | Olvassa el az egyéni lemezképek. |
> | Művelet | Microsoft.DevTestLab/labs/customImages/write | Hozzáadása vagy módosítása az egyéni lemezképek. |
> | Művelet | Microsoft.DevTestLab/labs/delete | Labs törlése. |
> | Művelet | Microsoft.DevTestLab/labs/ExportResourceUsage/action | A tesztlabor-erőforrások használata exportálja a storage-fiók |
> | Művelet | Microsoft.DevTestLab/labs/formulas/delete | Képletek törlése. |
> | Művelet | Microsoft.DevTestLab/labs/formulas/read | Olvassa el a formulákat. |
> | Művelet | Microsoft.DevTestLab/labs/formulas/write | Hozzáadása vagy módosítása formulákat. |
> | Művelet | Microsoft.DevTestLab/labs/galleryImages/read | Olvassa el a gyűjtemény lemezképei. |
> | Művelet | Microsoft.DevTestLab/labs/GenerateUploadUri/action | URI generálása az egyéni lemezképek feltöltése a labor. |
> | Művelet | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | A virtuális gép egy másik, amikor importálja. |
> | Művelet | Microsoft.DevTestLab/labs/ListVhds/action | Listázza az elérhető egyéni lemezkép létrehozásához szükséges lemezképeket. |
> | Művelet | Microsoft.DevTestLab/labs/notificationChannels/delete | Notificationchannels törlése. |
> | Művelet | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | Értesítést küld a megadott csatornán. |
> | Művelet | Microsoft.DevTestLab/labs/notificationChannels/read | Olvassa el a notificationchannels. |
> | Művelet | Microsoft.DevTestLab/labs/notificationChannels/write | Hozzáadása vagy módosítása notificationchannels. |
> | Művelet | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | Labor házirend kiértékelése. |
> | Művelet | Microsoft.DevTestLab/labs/policySets/policies/delete | Törölje a házirendeket. |
> | Művelet | Microsoft.DevTestLab/labs/policySets/policies/read | Olvassa el a házirendeket. |
> | Művelet | Microsoft.DevTestLab/labs/policySets/policies/write | Adja hozzá vagy módosíthat házirendeket. |
> | Művelet | Microsoft.DevTestLab/labs/read | Olvassa el a labs. |
> | Művelet | Microsoft.DevTestLab/labs/schedules/delete | Törölni az ütemezéseket. |
> | Művelet | Microsoft.DevTestLab/labs/schedules/Execute/action | Ütemezés hajtható végre. |
> | Művelet | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | Felsorolja az összes megfelelő ütemezést |
> | Művelet | Microsoft.DevTestLab/labs/schedules/read | Olvassa el az ütemezések. |
> | Művelet | Microsoft.DevTestLab/labs/schedules/write | Hozzáadása vagy módosítása ütemezések. |
> | Művelet | Microsoft.DevTestLab/labs/serviceRunners/delete | Szolgáltatás indák törlése. |
> | Művelet | Microsoft.DevTestLab/labs/serviceRunners/read | Olvasási szolgáltatás indák. |
> | Művelet | Microsoft.DevTestLab/labs/serviceRunners/write | Adja hozzá, vagy módosítsa a szolgáltatás indák. |
> | Művelet | Microsoft.DevTestLab/labs/users/delete | Törölje a felhasználói profilokat. |
> | Művelet | Microsoft.DevTestLab/labs/users/disks/Attach/action | Csatolja, és a címbérlet, a lemez a virtuális gép létrehozása. |
> | Művelet | Microsoft.DevTestLab/labs/users/disks/delete | Törölje a lemezt. |
> | Művelet | Microsoft.DevTestLab/labs/users/disks/Detach/action | Válassza le, és a címbérlet, a lemez a virtuális géphez csatlakoztatott break. |
> | Művelet | Microsoft.DevTestLab/labs/users/disks/read | Lemezek olvasása. |
> | Művelet | Microsoft.DevTestLab/labs/users/disks/write | Hozzáadása vagy módosítása a lemezeket. |
> | Művelet | Microsoft.DevTestLab/labs/users/environments/delete | Törölje a környezetben. |
> | Művelet | Microsoft.DevTestLab/labs/users/environments/read | Olvassa el a környezetben. |
> | Művelet | Microsoft.DevTestLab/labs/users/environments/write | Hozzáadása vagy módosítása a környezetben. |
> | Művelet | Microsoft.DevTestLab/labs/users/read | Olvassa a felhasználói profilokat. |
> | Művelet | Microsoft.DevTestLab/labs/users/secrets/delete | Törölje a titkos kulcsok. |
> | Művelet | Microsoft.DevTestLab/labs/users/secrets/read | Olvassa el a titkos kulcsok. |
> | Művelet | Microsoft.DevTestLab/labs/users/secrets/write | Hozzáadása vagy módosítása a titkos kulcsok. |
> | Művelet | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | Törölje a szolgáltatás hálóhoz. |
> | Művelet | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | Felsorolja az összes megfelelő ütemezést |
> | Művelet | Microsoft.DevTestLab/labs/users/serviceFabrics/read | Olvasási szolgáltatás hálóhoz. |
> | Művelet | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | Törölni az ütemezéseket. |
> | Művelet | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | Ütemezés hajtható végre. |
> | Művelet | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | Olvassa el az ütemezések. |
> | Művelet | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | Hozzáadása vagy módosítása ütemezések. |
> | Művelet | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | Indítsa el a service fabric. |
> | Művelet | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | Állítsa le a service fabric |
> | Művelet | Microsoft.DevTestLab/labs/users/serviceFabrics/write | Adja hozzá, vagy módosítsa a szolgáltatás hálóhoz. |
> | Művelet | Microsoft.DevTestLab/labs/users/write | Hozzáadása vagy módosítása a felhasználói profilok. |
> | Művelet | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | Új vagy meglévő adatlemezt csatolni a virtuális géphez. |
> | Művelet | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | Az összetevők vonatkozik a virtuális gép. |
> | Művelet | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | Saját tulajdonba vétele meglévő virtuális gépből |
> | Művelet | Microsoft.DevTestLab/labs/virtualMachines/delete | Törölje a virtuális gépeket. |
> | Művelet | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | Válassza le a megadott lemezt a virtuális gép. |
> | Művelet | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | Felsorolja az összes megfelelő ütemezést |
> | Művelet | Microsoft.DevTestLab/labs/virtualMachines/read | Olvassa el a virtuális gépek. |
> | Művelet | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | A virtuális gép újbóli üzembe helyezése |
> | Művelet | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | Átméretezni a virtuális gépet. |
> | Művelet | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | A virtuális gép újraindításához. |
> | Művelet | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | Törölni az ütemezéseket. |
> | Művelet | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | Ütemezés hajtható végre. |
> | Művelet | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | Olvassa el az ütemezések. |
> | Művelet | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | Hozzáadása vagy módosítása ütemezések. |
> | Művelet | Microsoft.DevTestLab/labs/virtualMachines/Start/action | Indítsa el a virtuális gépet. |
> | Művelet | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | A virtuális gép leállítása |
> | Művelet | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | Saját kezűleg adatlemezek virtuális gép tulajdonjogának átruházása |
> | Művelet | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | Egy meglévő virtuális gép tulajdonjogának kiadás |
> | Művelet | Microsoft.DevTestLab/labs/virtualMachines/write | Virtuális gépek hozzáadása vagy módosítása. |
> | Művelet | Microsoft.DevTestLab/labs/virtualNetworks/delete | Törölje a virtuális hálózatok. |
> | Művelet | Microsoft.DevTestLab/labs/virtualNetworks/read | Olvassa el a virtuális hálózatok. |
> | Művelet | Microsoft.DevTestLab/labs/virtualNetworks/write | Adja hozzá, vagy módosítsa a virtuális hálózatok. |
> | Művelet | Microsoft.DevTestLab/labs/vmPools/delete | Virtuális gép készleteket. |
> | Művelet | Microsoft.DevTestLab/labs/vmPools/read | Olvassa el a virtuális gép készletek. |
> | Művelet | Microsoft.DevTestLab/labs/vmPools/write | Adja hozzá, vagy módosítsa a virtuális gép készletek. |
> | Művelet | Microsoft.DevTestLab/labs/write | Hozzáadása vagy módosítása labs. |
> | Művelet | Microsoft.DevTestLab/locations/operations/read | Az olvasási műveletek. |
> | Művelet | Microsoft.DevTestLab/register/action | Az előfizetés regisztrálása |
> | Művelet | Microsoft.DevTestLab/schedules/delete | Törölni az ütemezéseket. |
> | Művelet | Microsoft.DevTestLab/schedules/Execute/action | Ütemezés hajtható végre. |
> | Művelet | Microsoft.DevTestLab/schedules/read | Olvassa el az ütemezések. |
> | Művelet | Microsoft.DevTestLab/schedules/Retarget/action | Frissíti a ütemezés célerőforrás azonosítója. |
> | Művelet | Microsoft.DevTestLab/schedules/write | Hozzáadása vagy módosítása ütemezések. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.DocumentDB/databaseAccountNames/read | A név foglaltságának ellenőrzése. |
> | Művelet | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | Módosítsa az erőforráscsoport egy olyan adatbázis-fiók |
> | Művelet | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | A gyűjtemény metrikai meghatározásainak beolvasása. |
> | Művelet | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | A gyűjtemény metrikák beolvasása. |
> | Művelet | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | Olvassa el az adatbázis fiók partíció szintű metrikáit |
> | Művelet | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | Olvassa el az adatbázis fiók partíció szintű metrikák |
> | Művelet | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | Olvassa el az adatbázis fiók partíciók egy gyűjteményen belül |
> | Művelet | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | Olvassa el az adatbázis-fiók partíció szintű módjait |
> | Művelet | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | Beolvassa a gyűjtemény is érvényesek. |
> | Művelet | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | Az adatbázis metrikai meghatározásainak beolvasása |
> | Művelet | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | Az adatbázis metrikák beolvasása. |
> | Művelet | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | Olvassa be az adatbázis is érvényesek. |
> | Művelet | Microsoft.DocumentDB/databaseAccounts/delete | Az adatbázis-fiók törlése. |
> | Művelet | Microsoft.DocumentDB/databaseAccounts/failoverPriorityChange/action | Az adatbázisfiók régiók feladatátvételi prioritás módosítása. Ez használatos kézi feladatátvételi művelet végrehajtásához |
> | Művelet | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | A kapcsolati karakterláncok beolvasása az adatbázisfiók |
> | Művelet | Microsoft.DocumentDB/databaseAccounts/listKeys/action | Egy adatbázis-fiók listázása |
> | Művelet | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | Beolvassa az adatbázist fiók metrikák definíciókat. |
> | Művelet | Microsoft.DocumentDB/databaseAccounts/metrics/read | Az adatbázis-fiók metrikák beolvasása. |
> | Művelet | Microsoft.DocumentDB/databaseAccounts/operationResults/read | Olvassa el az aszinkron művelet állapotát |
> | Művelet | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | Olvassa el a késés metrikák |
> | Művelet | Microsoft.DocumentDB/databaseAccounts/percentile/read | Olvassa el a replikációs késések fordulnak elő a százalékos érték |
> | Művelet | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | Késés olvasni a megadott forrás- és célparaméterei régió |
> | Művelet | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | Késés olvasni a célcsoportok régió |
> | Művelet | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Művelet | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Művelet | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/logDefinitions/read | A rendelkezésre álló napló catageries az adatbázis-fiók beolvasása |
> | Művelet | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/metricDefinitions/read | Az adatbázis fiók elérhető metrikai meghatározások beolvasása |
> | Művelet | Microsoft.DocumentDB/databaseAccounts/read | Az adatbázisfiók beolvasása. |
> | Művelet | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Olvassa be az adatbázis csak olvasható kulcsait. |
> | Művelet | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | Olvassa be az adatbázis csak olvasható kulcsait. |
> | Művelet | Microsoft.DocumentDB/databaseAccounts/regenerateKey/action | Az adatbázisfiók kulcsokkal elforgatása |
> | Művelet | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/metrics/read | A regionális gyűjtemény metrikák beolvasása. |
> | Művelet | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | Olvassa el a regionális adatbázis fiók partíció szintű metrikáit |
> | Művelet | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/metrics/read | Olvassa el a regionális adatbázis fiók partíció szintű metrikák |
> | Művelet | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/read | Olvassa el a regionális adatbázis fiók partíciók egy gyűjteményen belül |
> | Művelet | Microsoft.DocumentDB/databaseAccounts/region/metrics/read | A régió és az adatbázis fiók metrikák beolvasása. |
> | Művelet | Microsoft.DocumentDB/databaseAccounts/usages/read | Beolvassa az adatbázist fiók módjait. |
> | Művelet | Microsoft.DocumentDB/databaseAccounts/write | Egy adatbázis-fiók frissítéséhez. |
> | Művelet | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | Microsoft.DocumentDB értesíti, hogy VirtualNetwork vagy az alhálózat törlése folyamatban van |
> | Művelet | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | Olvassa el a deleteVirtualNetworkOrSubnets állapotának aszinkron művelet |
> | Művelet | Microsoft.DocumentDB/operationResults/read | Olvassa el az aszinkron művelet állapotát |
> | Művelet | Microsoft.DocumentDB/operations/read | A Microsoft documentdb elérhető olvasási műveletek  |
> | Művelet | Microsoft.DocumentDB/register/action |  Az előfizetés a Microsoft a DocumentDB erőforrás-szolgáltató regisztrálása |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.DomainRegistration/checkDomainAvailability/Action | Ellenőrizze, hogy a tartomány megvásárolható |
> | Művelet | Microsoft.DomainRegistration/domains/Delete | Törölje a meglévő tartományban. |
> | Művelet | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Delete | Törölje a tulajdonjog azonosítója |
> | Művelet | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Lista tulajdonjoga azonosítói |
> | Művelet | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Tulajdonosi azonosító lekérése |
> | Művelet | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Write | Létrehozása vagy frissítése azonosítója |
> | Művelet | Microsoft.DomainRegistration/domains/operationresults/Read | A tartomány művelet |
> | Művelet | Microsoft.DomainRegistration/domains/Read | A tartományok listájának lekérdezése |
> | Művelet | Microsoft.DomainRegistration/domains/Read | Get domain |
> | Művelet | Microsoft.DomainRegistration/domains/renew/Action | Újítsa meg egy meglévő tartományhoz. |
> | Művelet | Microsoft.DomainRegistration/domains/Write | Új tartomány hozzáadása vagy egy meglévő frissítése |
> | Művelet | Microsoft.DomainRegistration/generateSsoRequest/Action | Létre a tartomány vezérlőközpont bejelentkezni. |
> | Művelet | Microsoft.DomainRegistration/listDomainRecommendations/Action | A lista tartomány meg ajánlásainkat kulcsszavak beolvasása |
> | Művelet | Microsoft.DomainRegistration/operations/Read | Minden műveleteket az app service tartományregisztrációs felsorolása |
> | Művelet | Microsoft.DomainRegistration/register/action | Az előfizetés a Microsoft Domains erőforrás-szolgáltató regisztrálása |
> | Művelet | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | A szerződés művelet |
> | Művelet | Microsoft.DomainRegistration/topLevelDomains/Read | A toplevel tartományok beolvasása |
> | Művelet | Microsoft.DomainRegistration/topLevelDomains/Read | A toplevel lekérése |
> | Művelet | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | Ellenőrizze a tartomány beszerzési objektum lefolytatása nélkül |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.DynamicsLcs/lcsprojects/clouddeployments/read | Megjeleníti a Microsoft Dynamics AX 2012 R3 értékelési központi telepítések a felhasználóhoz tartozó Microsoft Dynamics életciklus szolgáltatások projektben |
> | Művelet | Microsoft.DynamicsLcs/lcsprojects/clouddeployments/write | A felhasználóhoz tartozó Microsoft Dynamics életciklus szolgáltatások projektben létrehozása a Microsoft Dynamics AX 2012 R3 értékelési telepítést. Központi telepítések is kezelhető az Azure felügyeleti portálon |
> | Művelet | Microsoft.DynamicsLcs/lcsprojects/connectors/read | Olvassa el a Microsoft Dynamics életciklus szolgáltatások projekthez tartozó összekötők |
> | Művelet | Microsoft.DynamicsLcs/lcsprojects/connectors/write | Létrehozása és frissítése a Microsoft Dynamics életciklus szolgáltatások projekthez tartozó összekötők |
> | Művelet | Microsoft.DynamicsLcs/lcsprojects/delete | A felhasználóhoz tartozó Microsoft Dynamics életciklus-Services-projektek törlése |
> | Művelet | Microsoft.DynamicsLcs/lcsprojects/read | A felhasználóhoz tartozó Microsoft Dynamics életciklus szolgáltatások projektek megjelenítéséhez |
> | Művelet | Microsoft.DynamicsLcs/lcsprojects/write | Hozzon létre, és a felhasználóhoz tartozó Microsoft Dynamics életciklus szolgáltatások projektek frissítése. Csak a nevet és leírást tulajdonságait is lehet frissíteni. Az egyes előfizetésekhez és helyekhez társított a projekt létrehozása után nem frissíthető |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.EventGrid/eventSubscriptions/delete | Egy eventSubscription törlése |
> | Művelet | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | Az esemény-előfizetés teljes URL-cím lekérése |
> | Művelet | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read | Az esemény előfizetések diagnosztikai beállításának beolvasása. |
> | Művelet | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti a diagnosztikai esemény-előfizetések beállítása |
> | Művelet | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read | A eventSubscriptions elérhető metrikai meghatározások beolvasása |
> | Művelet | Microsoft.EventGrid/eventSubscriptions/read | Olvassa el a eventSubscription |
> | Művelet | Microsoft.EventGrid/eventSubscriptions/write | Hozható létre vagy frissíthető egy eventSubscription |
> | Művelet | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | Témakörök diagnosztikai beállításának beolvasása. |
> | Művelet | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti a diagnosztikai beállításának kapcsolatos témakörök |
> | Művelet | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | Elérhető metrikai meghatározások beolvasása kapcsolatos témakörök |
> | Művelet | Microsoft.EventGrid/register/action | Regisztrálja a eventSubscription a EventGrid erőforrás-szolgáltató, és lehetővé teszi, hogy a rács esemény előfizetések létrehozása. |
> | Művelet | Microsoft.EventGrid/topics/delete | Egy témakör törlése |
> | Művelet | Microsoft.EventGrid/topics/listKeys/action | A témakör a listában kulcsok |
> | Művelet | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/read | Témakörök diagnosztikai beállításának beolvasása. |
> | Művelet | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti a diagnosztikai beállításának kapcsolatos témakörök |
> | Művelet | Microsoft.EventGrid/topics/providers/Microsoft.Insights/metricDefinitions/read | Elérhető metrikai meghatározások beolvasása kapcsolatos témakörök |
> | Művelet | Microsoft.EventGrid/topics/read | Olvassa el a témakör |
> | Művelet | Microsoft.EventGrid/topics/regenerateKey/action | A témakör kulcs újragenerálása |
> | Művelet | Microsoft.EventGrid/topics/write | Létrehozni vagy frissíteni a témakör |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.EventHub/checkNameAvailability/action | A névtér adott előfizetésben való elérhetőségének ellenőrzése. |
> | Művelet | Microsoft.EventHub/checkNamespaceAvailability/action | A névtér adott előfizetésben való elérhetőségének ellenőrzése. Ez az API elavult. használja helyette a CheckNameAvailabiltiy. |
> | Művelet | Microsoft.EventHub/clusters/providers/Microsoft.Insights/metricDefinitions/read | Fürt metrikáinak erőforrás leírása listájának beolvasása |
> | Művelet | Microsoft.EventHub/clusters/read | A fürterőforrás leírásának beolvasása |
> | Művelet | Microsoft.EventHub/clusters/write | A fürterőforrás leírásának beolvasása |
> | Művelet | Microsoft.EventHub/namespaces/authorizationRules/action | Frissítések Namespace engedélyezési szabályt. Ez az API depricated. A Namespace engedélyezési szabály sorkészletének frissítéséhez használja a PUT hívás... Ez a művelet nem támogatott az API verzió 2017-04-01. |
> | Művelet | Microsoft.EventHub/namespaces/authorizationRules/delete | Namespace engedélyezési szabály törlése. Az alapértelmezett Namespace engedélyezési szabály nem törölhető.  |
> | Művelet | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | A névtérhez tartozó kapcsolati karakterlánc beolvasása |
> | Művelet | Microsoft.EventHub/namespaces/authorizationRules/read | A névtér-engedélyezési szabályok leírásai listájának beolvasása. |
> | Művelet | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása |
> | Művelet | Microsoft.EventHub/namespaces/authorizationRules/write | A Namespace szintű engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és másodlagos kulcsok lehet frissíteni. |
> | Művelet | Microsoft.EventHub/namespaces/Delete | Egy névtérerőforrás törlése |
> | Művelet | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | A vészhelyreállítás elsődleges névterére vonatkozó engedélyezési szabályok kulcsainak beolvasása |
> | Művelet | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | Vészhelyreállítás elsődleges névterére vonatkozó engedélyezési szabályok beolvasása |
> | Művelet | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | A vészhelyreállítás letiltása és a módosítások elsődleges névtérből másodlagos névtérbe történő replikálásának leállítása. |
> | Művelet | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | A névtéralias adott előfizetésben való elérhetőségének ellenőrzése. |
> | Művelet | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | A névtér társított vész-helyreállítási konfiguráció törlése. Ezt a műveletet csak az elsődleges névtér keresztül kell meghívni. |
> | Művelet | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | GEO DR feladatátvételének meghívása és a névtéralias beállítása úgy, hogy a másodlagos névtérre mutasson. |
> | Művelet | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | A névtérhez hozzárendelt vész-helyreállítási konfiguráció beolvasása. |
> | Művelet | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | A névtérhez hozzárendelt vész-helyreállítási konfiguráció létrehozása vagy frissítése. |
> | Művelet | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/action | Az EventHub frissítési művelete. Ez a művelet nem támogatott az API verzió 2017-04-01. Az engedélyezési szabályok. Egy PUT hívás segítségével engedélyezési szabály frissítése. |
> | Művelet | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/delete | A művelet törli az EventHub-engedélyezési szabályok |
> | Művelet | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/listkeys/action | Az EventHub kapcsolati karakterláncot az beszerzése |
> | Művelet | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/read |  Az EventHub-az engedélyezési szabályok listájának lekérdezése |
> | Művelet | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/action | Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása |
> | Művelet | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/write | Az EventHub-engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Az engedélyezési szabályok hozzáférési jogok lehet frissíteni. |
> | Művelet | Microsoft.EventHub/namespaces/eventHubs/consumergroups/Delete | A művelet ConsumerGroup erőforrás törlése |
> | Művelet | Microsoft.EventHub/namespaces/eventHubs/consumergroups/read | Listájának ConsumerGroup erőforrás leírása |
> | Művelet | Microsoft.EventHub/namespaces/eventHubs/consumergroups/write | Hozzon létre vagy frissítés ConsumerGroup tulajdonságai. |
> | Művelet | Microsoft.EventHub/namespaces/eventhubs/Delete | A művelet az EventHub erőforrás törlése |
> | Művelet | Microsoft.EventHub/namespaces/eventhubs/read | Az EventHub erőforrás leírások listáját |
> | Művelet | Microsoft.EventHub/namespaces/eventhubs/write | Hozzon létre vagy frissítés EventHub tulajdonságai. |
> | Művelet | Microsoft.EventHub/namespaces/messagingPlan/read | Lekérdezi az névtér az üzenetküldési megtervezése.<br>Ez az API elavult.<br>A MessagingPlan erőforrás keresztül elérhetővé tett tulajdonságok (szülő) Namespace erőforrás későbbi API-verziók kerülnek...<br>Ez a művelet nem támogatott az API verzió 2017-04-01. |
> | Művelet | Microsoft.EventHub/namespaces/messagingPlan/write | Frissíti az üzenetkezelési tervezése névtér.<br>Ez az API elavult.<br>A MessagingPlan erőforrás keresztül elérhetővé tett tulajdonságok (szülő) Namespace erőforrás későbbi API-verziók kerülnek...<br>Ez a művelet nem támogatott az API verzió 2017-04-01. |
> | Művelet | Microsoft.EventHub/namespaces/operationresults/read | Névtérművelet állapotának beolvasása |
> | Művelet | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Namespace diagnosztikai beállítások erőforrás leírása listájának beolvasása |
> | Művelet | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Namespace diagnosztikai beállítások erőforrás leírása listájának beolvasása |
> | Művelet | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/logDefinitions/read | Listájának Namespace naplók erőforrás leírása |
> | Művelet | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Namespace metrikák erőforrás leírása listájának beolvasása |
> | Művelet | Microsoft.EventHub/namespaces/read | A névtérerőforrások leírásai listájának beolvasása |
> | Művelet | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | ACS-névtér eltávolítása |
> | Művelet | Microsoft.EventHub/namespaces/write | Namespace erőforrás létrehozása és frissítése a tulajdonságait. Címkék és a kapacitás, a Namespace nem frissíthető tulajdonságait. |
> | Művelet | Microsoft.EventHub/operations/read | Műveletek beolvasása |
> | Művelet | Microsoft.EventHub/register/action | Az EventHub erőforrás-szolgáltatóhoz tartozó előfizetés regisztrálása és az EventHub-erőforrások létrehozásának engedélyezése |
> | Művelet | Microsoft.EventHub/sku/read | Termékváltozat erőforrás leírása listájának beolvasása |
> | Művelet | Microsoft.EventHub/sku/regions/read | Listájának SkuRegions erőforrás leírása |
> | Művelet | Microsoft.EventHub/unregister/action | Az EventHub erőforrás-szolgáltató regisztrálása |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.Features/features/read | Beolvassa az előfizetéshez tartozó szolgáltatásokat. |
> | Művelet | Microsoft.Features/operations/read | A műveletek listájának beolvasása. |
> | Művelet | Microsoft.Features/providers/features/read | Beolvassa az előfizetés szolgáltatásait az adott erőforrás-szolgáltatón. |
> | Művelet | Microsoft.Features/providers/features/register/action | Regisztrálja az előfizetéshez tartozó szolgáltatást az adott erőforrás-szolgáltatón. |
> | Művelet | Microsoft.Features/providers/features/unregister/action | Az előfizetéshez tartozó szolgáltatás regisztrációjának törlése az adott erőforrás-szolgáltatón. |
> | Művelet | Microsoft.Features/register/action | Az előfizetésben elérhető funkció regisztrálása. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.HDInsight/clusters/changerdpsetting/action | HDInsight-fürt RDP beállításának módosítása |
> | Művelet | Microsoft.HDInsight/clusters/configurations/action | HDInsight-fürt konfigurációjának frissítése |
> | Művelet | Microsoft.HDInsight/clusters/configurations/read | HDInsight-fürt konfigurációjának beolvasása |
> | Művelet | Microsoft.HDInsight/clusters/delete | A HDInsight-fürt törlése |
> | Művelet | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/read | A HDInsight-fürt erőforrás diagnosztikai beállításának beolvasása. |
> | Művelet | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti a HDInsight-fürt erőforrás diagnosztikai beállításának |
> | Művelet | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/metricDefinitions/read | HDInsight-fürthöz elérhető metrikai meghatározások beolvasása |
> | Művelet | Microsoft.HDInsight/clusters/read | Részletes információkat szolgáltatva HDInsight-fürt |
> | Művelet | Microsoft.HDInsight/clusters/roles/resize/action | A HDInsight-fürt méretezése |
> | Művelet | Microsoft.HDInsight/clusters/write | Létrehozni vagy frissíteni a HDInsight-fürt |
> | Művelet | Microsoft.HDInsight/locations/capabilities/read | Az előfizetési lehetőségek elérése |
> | Művelet | Microsoft.HDInsight/locations/checkNameAvailability/read | Név foglaltságának ellenőrzése |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.ImportExport/jobs/delete | Egy meglévő feladat törlése. |
> | Művelet | Microsoft.ImportExport/jobs/listBitLockerKeys/action | Lekérdezi a megadott feladathoz tartozó BitLocker-kulcsok. |
> | Művelet | Microsoft.ImportExport/jobs/read | Tulajdonságait olvassa be a megadott feladathoz tartozó, illetve a feladatok beolvasása. |
> | Művelet | Microsoft.ImportExport/jobs/write | Létrehoz egy feladatot a megadott paraméterekkel, vagy a Tulajdonságok vagy a megadott feladathoz tartozó címkék frissítése. |
> | Művelet | Microsoft.ImportExport/locations/read | A megadott hely tulajdonságainak beolvasása, vagy a helyek listáját adja vissza. |
> | Művelet | Microsoft.ImportExport/register/action | Az importálási/exportálási erőforrás-szolgáltató előfizetésének regisztrálása, és lehetővé teszi, hogy az importálási/exportálási feladatok létrehozása. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.Insights/ActionGroups/Delete | Műveletcsoport törlése |
> | Művelet | Microsoft.Insights/ActionGroups/Read | Műveletcsoport olvasása |
> | Művelet | Microsoft.Insights/ActionGroups/Write | Műveletcsoport írása |
> | Művelet | Microsoft.Insights/ActivityLogAlerts/Activated/Action | A rendszer elindította a tevékenységnapló-riasztást |
> | Művelet | Microsoft.Insights/ActivityLogAlerts/Delete | Tevékenységnapló-riasztás törlése |
> | Művelet | Microsoft.Insights/ActivityLogAlerts/Read | Tevékenységnapló-riasztás olvasása |
> | Művelet | Microsoft.Insights/ActivityLogAlerts/Write | Tevékenységnapló-riasztás olvasása |
> | Művelet | Microsoft.Insights/AlertRules/Activated/Action | Riasztási szabály aktiválva |
> | Művelet | Microsoft.Insights/AlertRules/Delete | Riasztási szabály konfigurációjának törlése |
> | Művelet | Microsoft.Insights/AlertRules/Incidents/Read | Riasztási szabály incidenskonfigurációjának beolvasása |
> | Művelet | Microsoft.Insights/AlertRules/Read | Riasztási szabály konfigurációjának beolvasása |
> | Művelet | Microsoft.Insights/AlertRules/Resolved/Action | Riasztási szabály feloldva |
> | Művelet | Microsoft.Insights/AlertRules/Throttled/Action | A riiasztási szabály elindítva |
> | Művelet | Microsoft.Insights/AlertRules/Write | Riasztási szabály konfigurációjának írása |
> | Művelet | Microsoft.Insights/AutoscaleSettings/Delete | Automatikus skálázási beállítás konfigurációjának törlése |
> | Művelet | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | A metrikadefiníciók olvasása |
> | Művelet | Microsoft.Insights/AutoscaleSettings/Read | Automatikus skálázási beállítás konfigurációjának beolvasása |
> | Művelet | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | Automatikus vertikális leskálázási művelet |
> | Művelet | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | Automatikus vertikális felskálázási művelet |
> | Művelet | Microsoft.Insights/AutoscaleSettings/Write | Automatikus skálázási beállítás konfigurációjának írása |
> | Művelet | Microsoft.Insights/Components/AnalyticsItems/Delete | Egy Application Insights-elemzési elem törlése |
> | Művelet | Microsoft.Insights/Components/AnalyticsItems/Read | Egy Application Insights-elemzési elem olvasása |
> | Művelet | Microsoft.Insights/Components/AnalyticsItems/Write | Egy Application Insights-elemzési elem írása |
> | Művelet | Microsoft.Insights/Components/AnalyticsTables/Action | Az Application Insights-elemzési táblájának művelete |
> | Művelet | Microsoft.Insights/Components/AnalyticsTables/Delete | Egy Application Insights-elemzési tábla sémájának törlése |
> | Művelet | Microsoft.Insights/Components/AnalyticsTables/Read | Egy Application Insights-elemzési tábla sémájának olvasása |
> | Művelet | Microsoft.Insights/Components/AnalyticsTables/Write | Egy Application Insights-elemzési tábla sémájának írása |
> | Művelet | Microsoft.Insights/Components/Annotations/Delete | Egy Application Insights-jegyzet törlése |
> | Művelet | Microsoft.Insights/Components/Annotations/Read | Egy Application Insights-jegyzet olvasása |
> | Művelet | Microsoft.Insights/Components/Annotations/Write | Egy Application Insights-jegyzet írása |
> | Művelet | Microsoft.Insights/Components/Api/Read | Application Insights-összetevőadatok API-jának olvasása |
> | Művelet | Microsoft.Insights/Components/ApiKeys/Action | Egy Application Insights-beli API-kulcs létrehozása |
> | Művelet | Microsoft.Insights/Components/ApiKeys/Delete | Egy Application Insights-beli API-kulcs törlése |
> | Művelet | Microsoft.Insights/Components/ApiKeys/Read | Egy Application Insights-beli API-kulcs olvasása |
> | Művelet | Microsoft.Insights/Components/BillingPlanForComponent/Read | Egy Application Insights-összetevő számlázási csomagjának olvasása |
> | Művelet | Microsoft.Insights/Components/CurrentBillingFeatures/Read | Application Insights-összetevő aktuális számlázási funkcióinak olvasása |
> | Művelet | Microsoft.Insights/Components/CurrentBillingFeatures/Write | Application Insights-összetevő aktuális számlázási funkcióinak írása |
> | Művelet | Microsoft.Insights/Components/DefaultWorkItemConfig/Read | Az Application Insights egy alapértelmezett ALM-integrációja konfigurációjának olvasása |
> | Művelet | Microsoft.Insights/Components/Delete | Application Insights-összetevő konfigurációjának törlése |
> | Művelet | Microsoft.Insights/Components/ExportConfiguration/Action | Az Application Insights exportálási beállításainak művelete |
> | Művelet | Microsoft.Insights/Components/ExportConfiguration/Delete | Az Application Insights exportálási beállításainak törlése |
> | Művelet | Microsoft.Insights/Components/ExportConfiguration/Read | Az Application Insights exportálási beállításainak olvasása |
> | Művelet | Microsoft.Insights/Components/ExportConfiguration/Write | Az Application Insights exportálási beállításainak írása |
> | Művelet | Microsoft.Insights/Components/ExtendQueries/Read | Application Insights-összetevő bővített lekérdezési eredményeinek olvasása |
> | Művelet | Microsoft.Insights/Components/Favorites/Delete | Egy Application Insights-kedvenc törlése |
> | Művelet | Microsoft.Insights/Components/Favorites/Read | Egy Application Insights-kedvenc olvasása |
> | Művelet | Microsoft.Insights/Components/Favorites/Write | Egy Application Insights-kedvenc írása |
> | Művelet | Microsoft.Insights/Components/FeatureCapabilities/Read | Application Insights-összetevő funkciói lehetőségeinek olvasása |
> | Művelet | Microsoft.Insights/Components/GetAvailableBillingFeatures/Read | Application Insights-összetevő elérhető számlázási funkcióinak olvasása |
> | Művelet | Microsoft.Insights/Components/GetToken/Read | Egy Application Insights-összetevő jogkivonatának olvasása |
> | Művelet | Microsoft.Insights/Components/MetricDefinitions/Read | Application Insights-összetevő metrikadefinícióinak olvasása |
> | Művelet | Microsoft.Insights/Components/Metrics/Read | Application Insights-összetevő metrikáinak olvasása |
> | Művelet | Microsoft.Insights/Components/Move/Action | Application Insights-összetevő áthelyezése másik erőforráscsoportba vagy előfizetésbe |
> | Művelet | Microsoft.Insights/Components/MyAnalyticsItems/Delete | Egy személyes Application Insights-elemzési elem törlése |
> | Művelet | Microsoft.Insights/Components/MyAnalyticsItems/Read | Egy személyes Application Insights-elemzési elem olvasása |
> | Művelet | Microsoft.Insights/Components/MyAnalyticsItems/Write | Egy személyes Application Insights-elemzési elem írása |
> | Művelet | Microsoft.Insights/Components/MyFavorites/Read | Egy személyes Application Insights-kedvenc olvasása |
> | Művelet | Microsoft.Insights/Components/PricingPlans/Read | Egy Application Insights-összetevő tarifacsomagjának olvasása |
> | Művelet | Microsoft.Insights/Components/PricingPlans/Write | Egy Application Insights-összetevő tarifacsomagjának írása |
> | Művelet | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | Az Application Insights Proaktív problémaészlelés konfigurációjának olvasása |
> | Művelet | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | Az Application Insights Proaktív problémaészlelés konfigurációjának írása |
> | Művelet | Microsoft.Insights/Components/providers/Microsoft.Insights/MetricDefinitions/Read | A metrikadefiníciók olvasása |
> | Művelet | Microsoft.Insights/Components/QuotaStatus/Read | Application Insights-összetevő kvótaállapotának olvasása |
> | Művelet | Microsoft.Insights/Components/Read | Application Insights-összetevő konfigurációjának olvasása |
> | Művelet | Microsoft.Insights/Components/SyntheticMonitorLocations/Read | Application Insights-webtesztek helyének olvasása |
> | Művelet | Microsoft.Insights/Components/WorkItemConfigs/Delete | Az Application Insights egy ALM integrációja konfigurációjának törlése |
> | Művelet | Microsoft.Insights/Components/WorkItemConfigs/Read | Az Application Insights egy ALM-integrációja konfigurációjának olvasása |
> | Művelet | Microsoft.Insights/Components/WorkItemConfigs/Write | Az Application Insights egy ALM-integrációja konfigurációjának írása |
> | Művelet | Microsoft.Insights/Components/Write | Application Insights-összetevő konfigurációjának írása |
> | Művelet | Microsoft.Insights/DiagnosticSettings/Delete | Diagnosztikai beállítások konfigurációjának törlése |
> | Művelet | Microsoft.Insights/DiagnosticSettings/Read | Diagnosztikai beállítások konfigurációjának olvasása |
> | Művelet | Microsoft.Insights/DiagnosticSettings/Write | Diagnosztikai beállítások konfigurációjának írása |
> | Művelet | Microsoft.Insights/EventCategories/Read | Eseménykategória olvasása |
> | Művelet | Microsoft.Insights/eventtypes/digestevents/Read | A felügyeleti eseménytípus kivonatának olvasása |
> | Művelet | Microsoft.Insights/eventtypes/values/Read | A felügyeleti eseménytípus értékeinek olvasása |
> | Művelet | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | Bővített diagnosztikai beállítások konfigurációjának törlése |
> | Művelet | Microsoft.Insights/ExtendedDiagnosticSettings/Read | Bővített diagnosztikai beállítások konfigurációjának olvasása |
> | Művelet | Microsoft.Insights/ExtendedDiagnosticSettings/Write | Bővített diagnosztikai beállítások konfigurációjának írása |
> | Művelet | Microsoft.Insights/ListMigrationDate/Action | Előfizetés áttelepítési dátumának visszaolvasása |
> | Művelet | Microsoft.Insights/ListMigrationDate/Read | Előfizetés áttelepítési dátumának visszaolvasása |
> | Művelet | Microsoft.Insights/LogDefinitions/Read | A naplódefiníciók olvasása |
> | Művelet | Microsoft.Insights/LogProfiles/Delete | Naplóprofilok konfigurációjának törlése |
> | Művelet | Microsoft.Insights/LogProfiles/Read | Naplóprofilok olvasása |
> | Művelet | Microsoft.Insights/LogProfiles/Write | Naplóprofil-konfigurációba való írás |
> | Művelet | Microsoft.Insights/MetricAlerts/Delete | Metrikariasztás törlése |
> | Művelet | Microsoft.Insights/MetricAlerts/Read | Metrikariasztás olvasása |
> | Művelet | Microsoft.Insights/MetricAlerts/Status/Read | Metrikaalapú riasztás állapotának olvasása |
> | Művelet | Microsoft.Insights/MetricAlerts/Write | Metrikariasztás írása |
> | Művelet | Microsoft.Insights/MetricDefinitions/Microsoft.Insights/Read | A metrikadefiníciók olvasása |
> | Művelet | Microsoft.Insights/MetricDefinitions/providers/Microsoft.Insights/Read | A metrikadefiníciók olvasása |
> | Művelet | Microsoft.Insights/MetricDefinitions/Read | A metrikadefiníciók olvasása |
> | Művelet | Microsoft.Insights/Metrics/providers/Metrics/Read | Metrikák olvasása |
> | Művelet | Microsoft.Insights/Metrics/Read | Metrikák olvasása |
> | Művelet | Microsoft.Insights/Metrics/Write | Metrikák írása |
> | Művelet | Microsoft.Insights/MigrateToNewpricingModel/Action | Előfizetés áttelepítése új díjszabási modellbe |
> | Művelet | Microsoft.Insights/Operations/Read | Olvasási műveletek |
> | Művelet | Microsoft.Insights/Register/Action | A Microsoft Insights-szolgáltató regisztrálása |
> | Művelet | Microsoft.Insights/RollbackToLegacyPricingModel/Action | Előfizetés visszaállítása a régi díjszabási modellre |
> | Művelet | Microsoft.Insights/ScheduledQueryRules/Delete | Ütemezett lekérdezés szabály törlése |
> | Művelet | Microsoft.Insights/ScheduledQueryRules/Read | Egy ütemezett lekérdezési szabály olvasása |
> | Művelet | Microsoft.Insights/ScheduledQueryRules/Write | Ütemezett lekérdezési szabály írása |
> | Művelet | Microsoft.Insights/Tenants/Register/Action | A Microsoft Insights-szolgáltató inicializálása |
> | Művelet | Microsoft.Insights/Unregister/Action | A Microsoft Insights-szolgáltató regisztrálása |
> | Művelet | Microsoft.Insights/Webtests/Delete | Webteszt-konfiguráció törlése |
> | Művelet | Microsoft.Insights/Webtests/GetToken/Read | Egy webteszt jogkivonatának olvasása |
> | Művelet | Microsoft.Insights/Webtests/MetricDefinitions/Read | Egy webteszt metrikadefinícióinak olvasása |
> | Művelet | Microsoft.Insights/Webtests/Metrics/Read | Egy webteszt metrikáinak olvasása |
> | Művelet | Microsoft.Insights/Webtests/Read | Webteszt-konfiguráció olvasása |
> | Művelet | Microsoft.Insights/Webtests/Write | Webteszt-konfiguráció írása |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.KeyVault/checkNameAvailability/read | Annak ellenőrzése, hogy érvényes-e a kulcstartónév, és nincs-e használatban |
> | Művelet | Microsoft.KeyVault/deletedVaults/read | Helyreállíthatóan törölt kulcstartók tulajdonságainak megtekintése |
> | Művelet | Microsoft.KeyVault/hsmPools/delete | HSM-készlet törlése |
> | Művelet | Microsoft.KeyVault/hsmPools/joinVault/action | Kulcstartó HSM-készlethez való csatlakoztatása |
> | Művelet | Microsoft.KeyVault/hsmPools/read | A HSM-készlet tulajdonságainak megtekintése |
> | Művelet | Microsoft.KeyVault/hsmPools/write | Új HSM-készlet létrehozása vagy egy meglévő HSM-készlet tulajdonságainak frissítése |
> | Művelet | Microsoft.KeyVault/locations/deletedVaults/purge/action | Helyreállíthatóan törölt kulcstartó végleges törlése |
> | Művelet | Microsoft.KeyVault/locations/deletedVaults/read | Helyreállíthatóan törölt kulcstartó tulajdonságainak megtekintése |
> | Művelet | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | A Microsoft.KeyVault értesítése arról, hogy virtuális hálózat vagy alhálózat törlése folyamatban van |
> | Művelet | Microsoft.KeyVault/locations/operationResults/read | Hosszú futtatású művelet eredményének ellenőrzése |
> | Művelet | Microsoft.KeyVault/operations/read | A Microsoft.KeyVault erőforrás-szolgáltató elérhető műveleteinek listázása |
> | Művelet | Microsoft.KeyVault/register/action | Az előfizetés regisztrálása |
> | Művelet | Microsoft.KeyVault/unregister/action | Előfizetés regisztrációjának törlése |
> | Művelet | Microsoft.KeyVault/vaults/accessPolicies/write | Egyesítéssel vagy cserével frissíthet egy meglévő hozzáférési szabályzatot, vagy létrehozhat egy új hozzáférési szabályzatot a tárolóban. |
> | Művelet | Microsoft.KeyVault/vaults/delete | Kulcstároló törlése |
> | Művelet | Microsoft.KeyVault/vaults/deploy/action | A kulcstartóban található titkos kódokhoz való hozzáférés engedélyezése Azure-erőforrások üzembe helyezésekor |
> | Művelet | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/diagnosticSettings/Read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Művelet | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/diagnosticSettings/Write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Művelet | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/logDefinitions/read | Beolvassa a kulcstartóhoz elérhető naplókat |
> | Művelet | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/metricDefinitions/read | Beolvassa a kulcstartóhoz elérhető metrikákat |
> | Művelet | Microsoft.KeyVault/vaults/read | A kulcstartó tulajdonságainak megtekintése |
> | Művelet | Microsoft.KeyVault/vaults/secrets/read | A titkos kód tulajdonságainak megtekintése, kivéve a kód értékét |
> | Művelet | Microsoft.KeyVault/vaults/secrets/write | Új titkos kód létrehozása, vagy meglévő titkos kód értékének módosítása |
> | Művelet | Microsoft.KeyVault/vaults/write | Új kulcstartó létrehozása, vagy meglévő kulcstartó tulajdonságainak módosítása |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.LabServices/labAccounts/CreateLab/action | Labor létrehozása a tesztkörnyezeti fiók. |
> | Művelet | Microsoft.LabServices/labAccounts/delete | Tesztkörnyezet törlését. |
> | Művelet | Microsoft.LabServices/labAccounts/labs/delete | Labs törlése. |
> | Művelet | Microsoft.LabServices/labAccounts/labs/environmentSettings/ClaimAny/action | Jogcím-egy véletlenszerű környezetben egy felhasználó egy környezet beállításaiban |
> | Művelet | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | Törölje a környezeti beállítás. |
> | Művelet | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Claim/action | A környezet jogcímekből, és hozzárendeli a felhasználót |
> | Művelet | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | Törölje a környezetben. |
> | Művelet | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | Olvassa el a környezetben. |
> | Művelet | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | Elindítja a környezet összes erőforráshoz, a környezet elindításával. |
> | Művelet | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | A környezet összes erőforráshoz leállításával leállítja a környezet |
> | Művelet | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/write | Hozzáadása vagy módosítása a környezetben. |
> | Művelet | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | Rendelkezések/deprovisions szükséges erőforrások beállítása környezet aktuális állapotát, a laborkörnyezet/beállítás alapján. |
> | Művelet | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | Olvassa el a környezeti beállítás. |
> | Művelet | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | Elindítja a sablon összes erőforráshoz, a sablon elindításával. |
> | Művelet | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | Elindítja a sablon összes erőforráshoz, a sablon elindításával. |
> | Művelet | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | Hozzáadása vagy módosítása a környezeti beállítás. |
> | Művelet | Microsoft.LabServices/labAccounts/labs/read | Olvassa el a labs. |
> | Művelet | Microsoft.LabServices/labAccounts/labs/Register/action | Regisztrálja a felügyelt laborkörnyezetben. |
> | Művelet | Microsoft.LabServices/labAccounts/labs/users/delete | Felhasználók törlése. |
> | Művelet | Microsoft.LabServices/labAccounts/labs/users/read | Olvassa el a felhasználók. |
> | Művelet | Microsoft.LabServices/labAccounts/labs/users/write | Hozzáadása vagy módosítása a felhasználók. |
> | Művelet | Microsoft.LabServices/labAccounts/labs/write | Hozzáadása vagy módosítása labs. |
> | Művelet | Microsoft.LabServices/labAccounts/read | Olvassa el a labor fiókok. |
> | Művelet | Microsoft.LabServices/labAccounts/sizes/read | Olvassa el a méretét. |
> | Művelet | Microsoft.LabServices/labAccounts/write | Hozzáadása vagy módosítása a labor fiókok. |
> | Művelet | Microsoft.LabServices/locations/operations/read | Az olvasási műveletek. |
> | Művelet | Microsoft.LabServices/register/action | Az előfizetés regisztrálása |
> | Művelet | Microsoft.LabServices/users/GetEnvironment/action | Lekérdezi a virtuális gép részletei |
> | Művelet | Microsoft.LabServices/users/ListEnvironments/action | A felhasználó lista környezetekben |
> | Művelet | Microsoft.LabServices/users/ListLabs/action | A felhasználó labs listája. |
> | Művelet | Microsoft.LabServices/users/Register/action | Egy felügyelt, amikor a felhasználó regisztrálása |
> | Művelet | Microsoft.LabServices/users/StartEnvironment/action | Elindítja a környezet összes erőforráshoz, a környezet elindításával. |
> | Művelet | Microsoft.LabServices/users/StopEnvironment/action | A környezet összes erőforráshoz leállításával leállítja a környezet |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.LocationBasedServices/accounts/delete | (Elavult: használja a /providers/Microsoft.Maps) Egy hely alapú törlése szolgáltatásfiók. |
> | Művelet | Microsoft.LocationBasedServices/accounts/listKeys/action | (Elavult: használja a /providers/Microsoft.Maps) Hely alapú Services-fiók listázása |
> | Művelet | Microsoft.LocationBasedServices/accounts/providers/Microsoft.Insights/diagnosticSettings/read | (Elavult: használja a /providers/Microsoft.Maps) Az erőforrás diagnosztikai beállításának beolvasása. |
> | Művelet | Microsoft.LocationBasedServices/accounts/providers/Microsoft.Insights/diagnosticSettings/write | (Elavult: használja a /providers/Microsoft.Maps) Létrehozza vagy frissíti az erőforrás diagnosztikai beállításának |
> | Művelet | Microsoft.LocationBasedServices/accounts/providers/Microsoft.Insights/metricDefinitions/read | (Elavult: használja a /providers/Microsoft.Maps) A hely alapú szolgáltatások fiókok elérhető metrikai meghatározások beolvasása |
> | Művelet | Microsoft.LocationBasedServices/accounts/read | (Elavult: használja a /providers/Microsoft.Maps) Egy alapú helyét szolgáltatásfiók. |
> | Művelet | Microsoft.LocationBasedServices/accounts/regenerateKey/action | (Elavult: használja a /providers/Microsoft.Maps) Hozzon létre új hely alapú Services-fiók elsődleges vagy másodlagos kulcsot |
> | Művelet | Microsoft.LocationBasedServices/accounts/write | (Elavult: használja a /providers/Microsoft.Maps) Fiók létrehozása vagy frissítése egy hely alapú szolgáltatások. |
> | Művelet | Microsoft.LocationBasedServices/register/action | (Elavult: használja a /providers/Microsoft.Maps) Regisztrálja a szolgáltatót |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.Logic/integrationAccounts/agreements/delete | Egy integrációs fiókban lévő megállapodás törlése. |
> | Művelet | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | Egy integrációs fiókban lévő megállapodástartalom visszahívási URL-címének beolvasása. |
> | Művelet | Microsoft.Logic/integrationAccounts/agreements/read | Egy integrációs fiókban lévő megállapodás beolvasása. |
> | Művelet | Microsoft.Logic/integrationAccounts/agreements/write | A megállapodás létrehozása vagy frissítése egy integrációs fiókban. |
> | Művelet | Microsoft.Logic/integrationAccounts/assemblies/delete | Egy integrációs fiókban lévő szerelvény törlése. |
> | Művelet | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | Egy integrációs fiókban lévő szerelvénytartalom visszahívási URL-címének beolvasása. |
> | Művelet | Microsoft.Logic/integrationAccounts/assemblies/read | Egy integrációs fiókban lévő szerelvény beolvasása. |
> | Művelet | Microsoft.Logic/integrationAccounts/assemblies/write | A szerelvény létrehozása vagy frissítése egy integrációs fiókban. |
> | Művelet | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | Egy integrációs fiókban lévő kötegkonfiguráció törlése. |
> | Művelet | Microsoft.Logic/integrationAccounts/batchConfigurations/read | Egy integrációs fiókban lévő kötegkonfiguráció beolvasása. |
> | Művelet | Microsoft.Logic/integrationAccounts/batchConfigurations/write | A kötegkonfiguráció létrehozása vagy frissítése egy integrációs fiókban. |
> | Művelet | Microsoft.Logic/integrationAccounts/certificates/delete | Egy integrációs fiókban lévő tanúsítvány törlése. |
> | Művelet | Microsoft.Logic/integrationAccounts/certificates/read | Egy integrációs fiókban lévő tanúsítvány beolvasása. |
> | Művelet | Microsoft.Logic/integrationAccounts/certificates/write | A tanúsítvány létrehozása vagy frissítése egy integrációs fiókban. |
> | Művelet | Microsoft.Logic/integrationAccounts/delete | Egy integrációs fiók törlése. |
> | Művelet | Microsoft.Logic/integrationAccounts/join/action | Az integrációs fiók csatlakoztatása. |
> | Művelet | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | Egy integrációs fiók visszahívási URL-címének beolvasása. |
> | Művelet | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | Egy kulcstartóban lévő kulcsok beolvasása. |
> | Művelet | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | Egy integrációs fiókban bekövetkező követési események naplózása. |
> | Művelet | Microsoft.Logic/integrationAccounts/maps/delete | Egy integrációs fiókban lévő leképezés törlése. |
> | Művelet | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | Egy integrációs fiókban lévő hozzárendelési tartalom visszahívási URL-címének beolvasása. |
> | Művelet | Microsoft.Logic/integrationAccounts/maps/read | Egy integrációs fiókban lévő hozzárendelés beolvasása. |
> | Művelet | Microsoft.Logic/integrationAccounts/maps/write | A hozzárendelés létrehozása vagy frissítése egy integrációs fiókban. |
> | Művelet | Microsoft.Logic/integrationAccounts/partners/delete | Egy integrációs fiókban lévő partner törlése. |
> | Művelet | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | Egy integrációs fiókban lévő partnertartalom visszahívási URL-címének beolvasása. |
> | Művelet | Microsoft.Logic/integrationAccounts/partners/read | Egy integrációs fiókban lévő partner beolvasása. |
> | Művelet | Microsoft.Logic/integrationAccounts/partners/write | A partner létrehozása vagy frissítése egy integrációs fiókban. |
> | Művelet | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | Az integrációs fiók naplódefinícióinak olvasása. |
> | Művelet | Microsoft.Logic/integrationAccounts/read | Egy integrációs fiók beolvasása. |
> | Művelet | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | Újra létrehozza a hívóbetű titkos kulcsait. |
> | Művelet | Microsoft.Logic/integrationAccounts/schemas/delete | Egy integrációs fiókban lévő séma törlése. |
> | Művelet | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | Egy integrációs fiókban lévő sématartalom visszahívási URL-címének beolvasása. |
> | Művelet | Microsoft.Logic/integrationAccounts/schemas/read | Egy integrációs fiókban lévő séma beolvasása. |
> | Művelet | Microsoft.Logic/integrationAccounts/schemas/write | A séma létrehozása vagy frissítése egy integrációs fiókban. |
> | Művelet | Microsoft.Logic/integrationAccounts/sessions/delete | Egy integrációs fiókban lévő munkamenet törlése. |
> | Művelet | Microsoft.Logic/integrationAccounts/sessions/read | Egy integrációs fiókban lévő kötegkonfiguráció beolvasása. |
> | Művelet | Microsoft.Logic/integrationAccounts/sessions/write | A munkamenet létrehozása vagy frissítése egy integrációs fiókban. |
> | Művelet | Microsoft.Logic/integrationAccounts/write | Egy integrációs fiók létrehozása vagy frissítése. |
> | Művelet | Microsoft.Logic/locations/workflows/validate/action | Érvényesíti a munkafolyamatot. |
> | Művelet | Microsoft.Logic/operations/read | Egy művelet beolvasása. |
> | Művelet | Microsoft.Logic/register/action | A Microsoft.Logic erőforrás-szolgáltató regisztrálása egy adott előfizetéshez. |
> | Művelet | Microsoft.Logic/workflows/accessKeys/delete | Törli a hívóbetűt. |
> | Művelet | Microsoft.Logic/workflows/accessKeys/list/action | Listázza a hívóbetű titkos kulcsait. |
> | Művelet | Microsoft.Logic/workflows/accessKeys/read | Beolvassa a hívóbetűt. |
> | Művelet | Microsoft.Logic/workflows/accessKeys/regenerate/action | Újra létrehozza a hívóbetű titkos kulcsait. |
> | Művelet | Microsoft.Logic/workflows/accessKeys/write | Létrehozza vagy frissíti a hívóbetűt. |
> | Művelet | Microsoft.Logic/workflows/delete | Törli a munkafolyamatot. |
> | Művelet | Microsoft.Logic/workflows/disable/action | Letiltja a munkafolyamatot. |
> | Művelet | Microsoft.Logic/workflows/enable/action | Engedélyezi a munkafolyamatot. |
> | Művelet | Microsoft.Logic/workflows/listCallbackUrl/action | Egy munkafolyamat visszahívási URL-címének beolvasása. |
> | Művelet | Microsoft.Logic/workflows/listSwagger/action | A munkafolyamat Swagger-definícióinak beolvasása. |
> | Művelet | Microsoft.Logic/workflows/move/action | A munkafolyamatot a meglévő előfizetés-azonosítójáról, erőforráscsoportjából és/vagy nevéről egy másik előfizetés-azonosítóra, erőforráscsoportba és/vagy névre helyezi át. |
> | Művelet | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/read | Beolvassa a munkafolyamat diagnosztikai beállításait. |
> | Művelet | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti a munkafolyamat diagnosztikai beállításait. |
> | Művelet | Microsoft.Logic/workflows/providers/Microsoft.Insights/logDefinitions/read | Beolvassa a munkafolyamat naplómeghatározásait. |
> | Művelet | Microsoft.Logic/workflows/providers/Microsoft.Insights/metricDefinitions/read | Beolvassa a munkafolyamat metrikai meghatározásait. |
> | Művelet | Microsoft.Logic/workflows/read | Beolvassa a munkafolyamatot. |
> | Művelet | Microsoft.Logic/workflows/regenerateAccessKey/action | Újra létrehozza a hívóbetű titkos kulcsait. |
> | Művelet | Microsoft.Logic/workflows/run/action | Elindítja egy munkafolyamat futtatását. |
> | Művelet | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | A munkafolyamat-futtatási műveleti kifejezés követési adatainak beolvasása. |
> | Művelet | Microsoft.Logic/workflows/runs/actions/read | Beolvassa a munkafolyamat-futtatási műveletet. |
> | Művelet | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | Egy munkafolyamat-futtatási művelet ismétlődési kifejezéskövetési adatainak beolvasása. |
> | Művelet | Microsoft.Logic/workflows/runs/actions/repetitions/read | Egy munkafolyamat-futtatási művelet ismétlődésének beolvasása. |
> | Művelet | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | Egy munkafolyamat-futtatási művelet hatóköre ismétlődésének beolvasása. |
> | Művelet | Microsoft.Logic/workflows/runs/cancel/action | Megszakítja egy munkafolyamat futtatását. |
> | Művelet | Microsoft.Logic/workflows/runs/operations/read | Beolvassa a munkafolyamat-futtatás műveleti állapotát. |
> | Művelet | Microsoft.Logic/workflows/runs/read | Beolvassa a munkafolyamat-futtatást. |
> | Művelet | Microsoft.Logic/workflows/suspend/action | Egy munkafolyamat felfüggesztése. |
> | Művelet | Microsoft.Logic/workflows/triggers/histories/read | Beolvassa a triggerelőzményeket. |
> | Művelet | Microsoft.Logic/workflows/triggers/histories/resubmit/action | A munkafolyamat-trigger újraküldése. |
> | Művelet | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | Egy trigger visszahívási URL-címének beolvasása. |
> | Művelet | Microsoft.Logic/workflows/triggers/read | Beolvassa a triggert. |
> | Művelet | Microsoft.Logic/workflows/triggers/reset/action | A trigger alaphelyzetbe állítása. |
> | Művelet | Microsoft.Logic/workflows/triggers/run/action | Végrehajtja a triggert. |
> | Művelet | Microsoft.Logic/workflows/triggers/setState/action | A trigger állapotának beállítása. |
> | Művelet | Microsoft.Logic/workflows/validate/action | Érvényesíti a munkafolyamatot. |
> | Művelet | Microsoft.Logic/workflows/versions/read | Beolvassa a munkafolyamat verzióját. |
> | Művelet | Microsoft.Logic/workflows/versions/triggers/listCallbackUrl/action | Egy trigger visszahívási URL-címének beolvasása. |
> | Művelet | Microsoft.Logic/workflows/write | Létrehozza vagy frissíti a munkafolyamatot. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | Helyezze át a gépi tanulási kötelezettségvállalás terv társítása |
> | Művelet | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | Olvassa el a gépi tanulási kötelezettségvállalás terv társítása |
> | Művelet | Microsoft.MachineLearning/commitmentPlans/delete | Gépi tanulás kötelezettségvállalás terv törlése |
> | Művelet | Microsoft.MachineLearning/commitmentPlans/join/action | Csatlakozás a gépi tanulási kötelezettségvállalás terv |
> | Művelet | Microsoft.MachineLearning/commitmentPlans/read | Olvassa el a gépi tanulási kötelezettségvállalás terv |
> | Művelet | Microsoft.MachineLearning/commitmentPlans/write | Hozzon létre vagy bármely Machine Learning előfizetési csomag frissítése |
> | Művelet | Microsoft.MachineLearning/locations/operationresults/read | A Machine Learning művelet eredményének beolvasása |
> | Művelet | Microsoft.MachineLearning/locations/operationsstatus/read | A Machine Learning futó művelet állapotának beolvasása |
> | Művelet | Microsoft.MachineLearning/operations/read | Gépi tanulási műveletek beolvasása |
> | Művelet | Microsoft.MachineLearning/register/action | A machine learning web service erőforrás-szolgáltató előfizetésének regisztrálása, és lehetővé teszi a webszolgáltatások létrehozását. |
> | Művelet | Microsoft.MachineLearning/skus/read | Gépi tanulási kötelezettségvállalás terv termékváltozatok beolvasása |
> | Művelet | Microsoft.MachineLearning/webServices/action | A támogatott régiók regionális webes szolgáltatás tulajdonságok létrehozása |
> | Művelet | Microsoft.MachineLearning/webServices/delete | A Machine Learning webszolgáltatás törlése |
> | Művelet | Microsoft.MachineLearning/webServices/listkeys/read | A Machine Learning webszolgáltatás-kulcsok beszerzése |
> | Művelet | Microsoft.MachineLearning/webServices/read | Olvassa el a Machine Learning webszolgáltatás |
> | Művelet | Microsoft.MachineLearning/webServices/write | Létrehozni vagy frissíteni a Machine Learning webszolgáltatás |
> | Művelet | Microsoft.MachineLearning/Workspaces/delete | Gépi tanulás munkaterület törlése |
> | Művelet | Microsoft.MachineLearning/Workspaces/listworkspacekeys/action | A Machine Learning-munkaterület listában kulcsok |
> | Művelet | Microsoft.MachineLearning/Workspaces/read | Olvassa el a gépi tanulási munkaterület |
> | Művelet | Microsoft.MachineLearning/Workspaces/resyncstoragekeys/action | A Machine Learning-munkaterület beállítása tárfiók kulcsait újraszinkronizálásra |
> | Művelet | Microsoft.MachineLearning/Workspaces/write | Létrehozni vagy frissíteni a gépi tanulási munkaterület |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.MachineLearningCompute/operationalizationClusters/checkUpdate/action | Ha frissítések érhetők el a operationalization fürt-szolgáltatások ellenőrzése |
> | Művelet | Microsoft.MachineLearningCompute/operationalizationClusters/delete | Bármely üzemeltetési fiók törlése |
> | Művelet | Microsoft.MachineLearningCompute/operationalizationClusters/listKeys/action | Operationalization-fürthöz tartozó kulcsainak listázása |
> | Művelet | Microsoft.MachineLearningCompute/operationalizationClusters/read | Bármely olyan üzemeltetési fiók olvasása |
> | Művelet | Microsoft.MachineLearningCompute/operationalizationClusters/updateSystem/action | A rendszerszolgáltatások operationalization fürt frissítése |
> | Művelet | Microsoft.MachineLearningCompute/operationalizationClusters/write | Bármely üzemeltetési fiók létrehozása vagy frissítése |
> | Művelet | Microsoft.MachineLearningCompute/register/action | Az erőforrás-szolgáltató az előfizetés-azonosító regisztrálja, és lehetővé teszi a machine learning-számítási erőforrásokat létrehozását |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.MachineLearningModelManagement/accounts/delete | Bármely üzemeltetési fiók törlése |
> | Művelet | Microsoft.MachineLearningModelManagement/accounts/read | Bármely olyan üzemeltetési fiók olvasása |
> | Művelet | Microsoft.MachineLearningModelManagement/accounts/write | Bármely üzemeltetési fiók létrehozása vagy frissítése |
> | Művelet | Microsoft.MachineLearningModelManagement/register/action | Az erőforrás-szolgáltató az előfizetés-azonosító regisztrálja, és lehetővé teszi egy üzemeltetési fiók létrehozását |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.MachineLearningServices/register/action | A Machine Learning szolgáltatások erőforrás-szolgáltató előfizetésének regisztrálása |
> | Művelet | Microsoft.MachineLearningServices/workspaces/computes/delete | A Machine Learning szolgáltatás munkaterületek számítási erőforrások törlése |
> | Művelet | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | Titkos kulcsainak listázása a számítási erőforrásokat Machine Learning szolgáltatások munkaterület |
> | Művelet | Microsoft.MachineLearningServices/workspaces/computes/read | A számítási erőforrásokat lekérdezi a Machine Learning szolgáltatás munkaterületek |
> | Művelet | Microsoft.MachineLearningServices/workspaces/computes/write | Létrehozza vagy frissíti a számítási erőforrásokat a Machine Learning szolgáltatás munkaterületek |
> | Művelet | Microsoft.MachineLearningServices/workspaces/delete | Törli a gépi tanulási szolgáltatások munkaterületek |
> | Művelet | Microsoft.MachineLearningServices/workspaces/listKeys/action | A Machine Learning szolgáltatások munkaterület titkos kulcsainak listázása |
> | Művelet | Microsoft.MachineLearningServices/workspaces/read | Lekérdezi a gépi tanulási szolgáltatások munkaterületek |
> | Művelet | Microsoft.MachineLearningServices/workspaces/write | Létrehozza vagy frissíti a Machine Learning szolgáltatás munkaterületek |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | Szerepalapú művelet egy meglévő felhasználó hozzárendelése egy erőforráshoz hozzárendelt identitás |
> | Művelet | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Egy meglévő felhasználó lehet hozzárendelve identitás törlése |
> | Művelet | Microsoft.ManagedIdentity/userAssignedIdentities/read | Lekérdezi egy meglévő felhasználó lehet hozzárendelve identitás |
> | Művelet | Microsoft.ManagedIdentity/userAssignedIdentities/write | Egy új felhasználó lehet hozzárendelve identitás létrehoz vagy frissít egy meglévő felhasználó lehet hozzárendelve identitás társított címkék |

## <a name="microsoftmanagedlab"></a>Microsoft.ManagedLab

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.ManagedLab/labAccounts/CreateLab/action | Labor létrehozása a tesztkörnyezeti fiók. |
> | Művelet | Microsoft.ManagedLab/labAccounts/delete | Tesztkörnyezet törlését. |
> | Művelet | Microsoft.ManagedLab/labAccounts/labs/delete | Labs törlése. |
> | Művelet | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/delete | Törölje a környezeti beállítás. |
> | Művelet | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/delete | Törölje a környezetben. |
> | Művelet | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/read | Olvassa el a környezetben. |
> | Művelet | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/write | Hozzáadása vagy módosítása a környezetben. |
> | Művelet | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/read | Olvassa el a környezeti beállítás. |
> | Művelet | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/write | Hozzáadása vagy módosítása a környezeti beállítás. |
> | Művelet | Microsoft.ManagedLab/labAccounts/labs/labVms/delete | Törölje a labor virtuális gépeket. |
> | Művelet | Microsoft.ManagedLab/labAccounts/labs/labVms/read | Olvassa el a labor virtuális gépeken. |
> | Művelet | Microsoft.ManagedLab/labAccounts/labs/labVms/write | Hozzáadása vagy módosítása a labor virtuális gépeken. |
> | Művelet | Microsoft.ManagedLab/labAccounts/labs/read | Olvassa el a labs. |
> | Művelet | Microsoft.ManagedLab/labAccounts/labs/write | Hozzáadása vagy módosítása labs. |
> | Művelet | Microsoft.ManagedLab/labAccounts/read | Olvassa el a labor fiókok. |
> | Művelet | Microsoft.ManagedLab/labAccounts/write | Hozzáadása vagy módosítása a labor fiókok. |
> | Művelet | Microsoft.ManagedLab/locations/operations/read | Az olvasási műveletek. |
> | Művelet | Microsoft.ManagedLab/register/action | Az előfizetés regisztrálása |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.Management/checkNameAvailability/action | Ellenőrzi, hogy a megadott felügyeleti csoport neve érvényes és egyedi. |
> | Művelet | Microsoft.Management/getEntities/action | Összes entitás (felügyeleti csoportok, előfizetések stb.) a hitelesített felhasználók listázása. |
> | Művelet | Microsoft.Management/managementGroups/delete | Felügyeleti csoport törlése. |
> | Művelet | Microsoft.Management/managementGroups/read | A hitelesített felhasználók listáját a felügyeleti csoportok. |
> | Művelet | Microsoft.Management/managementGroups/subscriptions/delete | Előfizetés a felügyeleti csoportból való társítja. |
> | Művelet | Microsoft.Management/managementGroups/subscriptions/write | Meglévő felügyeleti csoporttal előfizetés társult. |
> | Művelet | Microsoft.Management/managementGroups/write | Hozzon létre, vagy egy felügyeleti csoport frissítése. |
> | Művelet | Microsoft.Management/register/action | A megadott előfizetés regisztrálása Microsoft.Management |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.Maps/accounts/delete | A Maps-fiók törlése. |
> | Művelet | Microsoft.Maps/accounts/listKeys/action | A Maps-fiók listázása |
> | Művelet | Microsoft.Maps/accounts/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Művelet | Microsoft.Maps/accounts/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Művelet | Microsoft.Maps/accounts/providers/Microsoft.Insights/metricDefinitions/read | A Maps fiókok elérhető metrikai meghatározások beolvasása |
> | Művelet | Microsoft.Maps/accounts/read | Hozzon létre egy Maps-fiókot. |
> | Művelet | Microsoft.Maps/accounts/regenerateKey/action | Hozzon létre új Maps fiók elsődleges vagy másodlagos kulcsot |
> | Művelet | Microsoft.Maps/accounts/write | Fiók létrehozása vagy frissítése a Maps. |
> | Művelet | Microsoft.Maps/register/action | Regisztrálja a szolgáltatót |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | Szerződés adja vissza. |
> | Művelet | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | Egy aláírt megállapodás fogad el. |
> | Művelet | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | A végfelhasználó ACR lemezkép importál. |
> | Művelet | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | A config adja vissza. |
> | Művelet | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | Menti a config. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.MarketplaceApps/ClassicDevServices/delete | Klasszikus fejlesztői szolgáltatás erőforrás törlési műveletet végez. |
> | Művelet | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | Lekérdezi a klasszikus fejlesztői erőforrás felügyeleti kulcsai. |
> | Művelet | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | Az egyszeri bejelentkezési URL-cím lekérése egy klasszikus fejlesztői szolgáltatáshoz. |
> | Művelet | Microsoft.MarketplaceApps/ClassicDevServices/read | Klasszikus fejlesztői szolgáltatás a GET műveletet végez. |
> | Művelet | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | A klasszikus fejlesztői erőforrás felügyeleti kulcsai állít elő. |
> | Művelet | Microsoft.MarketplaceApps/Operations/read | Olvassa el a műveleteket, az összes erőforrástípus. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.MarketplaceOrdering/agreements/offers/plans/cancel/action | Szakítsa meg a szerződés egy adott piactér elem |
> | Művelet | Microsoft.MarketplaceOrdering/agreements/offers/plans/read | Térjen vissza az adott piactér elem szerződés |
> | Művelet | Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action | Egy adott piactér elem létrejött |
> | Művelet | Microsoft.MarketplaceOrdering/agreements/read | Térjen vissza a minden szerződés megadott előfizetés |
> | Művelet | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | Egy adott piactér virtuális gép elem szerződés beolvasása |
> | Művelet | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | Bejelentkezés, vagy szakítsa meg a szerződés egy adott piactér virtuális gép elem |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.Media/checknameavailability/action | Ellenőrzi, hogy a Media Services-fiók nevének érhető el |
> | Művelet | Microsoft.Media/locations/checkNameAvailability/action | Ellenőrzi, hogy a Media Services-fiók nevének érhető el |
> | Művelet | Microsoft.Media/mediaservices/assets/delete | Minden eszköz törlése |
> | Művelet | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | Eszköz titkosítási kulcs beszerzése |
> | Művelet | Microsoft.Media/mediaservices/assets/listContainerSas/action | Lista eszköz tároló SAS URL-címek |
> | Művelet | Microsoft.Media/mediaservices/assets/read | Minden eszköz beolvasása |
> | Művelet | Microsoft.Media/mediaservices/assets/write | Minden eszköz létrehozása vagy frissítése |
> | Művelet | Microsoft.Media/mediaservices/contentKeyPolicies/delete | A tartalom-házirend törlése |
> | Művelet | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | A titkos kulcsok házirend tulajdonságainak beolvasása |
> | Művelet | Microsoft.Media/mediaservices/contentKeyPolicies/read | A tartalom-házirend olvasása |
> | Művelet | Microsoft.Media/mediaservices/contentKeyPolicies/write | Hozzon létre vagy bármely tartalom-házirend frissítése |
> | Művelet | Microsoft.Media/mediaservices/delete | A Media Services-fiók törlése |
> | Művelet | Microsoft.Media/mediaservices/eventGridFilters/delete | A rács szűrő törlése |
> | Művelet | Microsoft.Media/mediaservices/eventGridFilters/read | Olvassa el a rács szűrő |
> | Művelet | Microsoft.Media/mediaservices/eventGridFilters/write | Létrehozni vagy frissíteni a rács szűrő |
> | Művelet | Microsoft.Media/mediaservices/liveEventOperations/read | Olvassa el az összes élő esemény műveletet |
> | Művelet | Microsoft.Media/mediaservices/liveEvents/delete | Bármely élő esemény törlése |
> | Művelet | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | Élő kimenetet törlése |
> | Művelet | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | Élő kimenetet olvasása |
> | Művelet | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | Hozzon létre vagy élő kimenetet frissítése |
> | Művelet | Microsoft.Media/mediaservices/liveEvents/read | Olvassa el az összes élő esemény |
> | Művelet | Microsoft.Media/mediaservices/liveEvents/reset/action | Bármely élő esemény művelet alaphelyzetbe állítása |
> | Művelet | Microsoft.Media/mediaservices/liveEvents/start/action | Bármely élő esemény indítása |
> | Művelet | Microsoft.Media/mediaservices/liveEvents/stop/action | Bármely élő esemény művelet leállítása |
> | Művelet | Microsoft.Media/mediaservices/liveEvents/write | Létrehozása vagy frissítése bármely élő esemény |
> | Művelet | Microsoft.Media/mediaservices/liveOutputOperations/read | Olvassa el az összes élő kimeneti műveletet |
> | Művelet | Microsoft.Media/mediaservices/read | Olvassa el a Media Services-fiók |
> | Művelet | Microsoft.Media/mediaservices/streamingEndpointOperations/read | Olvassa el a folyamatos átviteli végpont művelet |
> | Művelet | Microsoft.Media/mediaservices/streamingEndpoints/delete | Bármely adatfolyam-továbbítási végpontjának törlése |
> | Művelet | Microsoft.Media/mediaservices/streamingEndpoints/read | A Streamvégpont olvasása |
> | Művelet | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | Minden olyan adatfolyam-továbbítási végpont művelet méretezése |
> | Művelet | Microsoft.Media/mediaservices/streamingEndpoints/start/action | A folyamatos átviteli végpont indítása |
> | Művelet | Microsoft.Media/mediaservices/streamingEndpoints/stop/action | A folyamatos átviteli végpont művelet leállítása |
> | Művelet | Microsoft.Media/mediaservices/streamingEndpoints/write | Hozzon létre vagy bármely adatfolyam-továbbítási végpontjának frissítése |
> | Művelet | Microsoft.Media/mediaservices/streamingLocators/delete | A Streamelési Locator törlése |
> | Művelet | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | Tartalom listázása |
> | Művelet | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | Útvonalak listája |
> | Művelet | Microsoft.Media/mediaservices/streamingLocators/read | Olvassa el a Streamelési lokátor |
> | Művelet | Microsoft.Media/mediaservices/streamingLocators/write | Létrehozni vagy frissíteni a Streamelési lokátor |
> | Művelet | Microsoft.Media/mediaservices/streamingPolicies/delete | Bármely adatfolyam-házirend törlése |
> | Művelet | Microsoft.Media/mediaservices/streamingPolicies/read | Bármely adatfolyam-házirend olvasása |
> | Művelet | Microsoft.Media/mediaservices/streamingPolicies/write | Létrehozni vagy frissíteni a folyamatos átviteli házirendet |
> | Művelet | Microsoft.Media/mediaservices/syncStorageKeys/action | A tárolási kulcsokat csatolt Azure Storage-fiók szinkronizálása |
> | Művelet | Microsoft.Media/mediaservices/transforms/delete | Bármely átalakító törlése |
> | Művelet | Microsoft.Media/mediaservices/transforms/jobs/cancelJob/action | Megszakítása |
> | Művelet | Microsoft.Media/mediaservices/transforms/jobs/delete | Törli a feladatot |
> | Művelet | Microsoft.Media/mediaservices/transforms/jobs/read | A feladat beolvasása |
> | Művelet | Microsoft.Media/mediaservices/transforms/jobs/write | Létrehozni vagy frissíteni a feladat |
> | Művelet | Microsoft.Media/mediaservices/transforms/read | Bármely átalakító olvasása |
> | Művelet | Microsoft.Media/mediaservices/transforms/write | Létrehozni vagy frissíteni a átalakítás |
> | Művelet | Microsoft.Media/mediaservices/write | Bármely Media Services-fiók létrehozása vagy frissítése |
> | Művelet | Microsoft.Media/operations/read | Rendelkezésre álló műveletek beolvasása |
> | Művelet | Microsoft.Media/register/action | A Media Services erőforrás-szolgáltató előfizetésének regisztrálása, és lehetővé teszi a Media Services-fiókok létrehozását |
> | Művelet | Microsoft.Media/unregister/action | A Media Services erőforrás-szolgáltató az előfizetés regisztrációjának törlése |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.Migrate/Operations/read | Az elérhető műveletek beolvasása |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | Alkalmazás átjáró Ssl előre definiált házirend |
> | Művelet | Microsoft.Network/applicationGatewayAvailableSslOptions/read | Alkalmazás átjáró elérhető Ssl-beállítások |
> | Művelet | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | Rendelkezésre álló Waf beállítására Alkalmazásátjáró beolvasása |
> | Művelet | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Egy alkalmazás Alkalmazásátjáró háttércímkészletének illesztése |
> | Művelet | Microsoft.Network/applicationGateways/backendhealth/action | Az átjáró háttér állapotának beolvasása |
> | Művelet | Microsoft.Network/applicationGateways/delete | Alkalmazásátjáró törlése |
> | Művelet | Microsoft.Network/applicationGateways/effectiveNetworkSecurityGroups/action | Alkalmazásátjáró konfigurált útválasztási táblázatot beolvasása |
> | Művelet | Microsoft.Network/applicationGateways/effectiveRouteTable/action | Alkalmazásátjáró konfigurált útválasztási táblázatot beolvasása |
> | Művelet | Microsoft.Network/applicationGateways/providers/Microsoft.Insights/logDefinitions/read | Az Alkalmazásátjáró eseményeinek beolvasása |
> | Művelet | Microsoft.Network/applicationGateways/providers/Microsoft.Insights/metricDefinitions/read | Az Alkalmazásátjáró elérhető metrikai meghatározások beolvasása |
> | Művelet | Microsoft.Network/applicationGateways/read | Alkalmazásátjáró beolvasása |
> | Művelet | Microsoft.Network/applicationGateways/setSecurityCenterConfiguration/action | Konfigurációs készletek alkalmazás átjáró biztonsági központ |
> | Művelet | Microsoft.Network/applicationGateways/start/action | Alkalmazásátjáró kezdődik |
> | Művelet | Microsoft.Network/applicationGateways/stop/action | Leállítja az Alkalmazásátjáró |
> | Művelet | Microsoft.Network/applicationGateways/write | Alkalmazásátjáró létrehozása vagy meglévő Alkalmazásátjáró frissítése |
> | Művelet | Microsoft.Network/applicationSecurityGroups/delete | Az alkalmazás biztonsági csoport törlése |
> | Művelet | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | Biztonsági csoportok csatlakoztatja IP-konfigurációt. |
> | Művelet | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Csatlakozik a szabály az alkalmazás biztonsági csoportokat. |
> | Művelet | Microsoft.Network/applicationSecurityGroups/read | Lekérdezi egy alkalmazás biztonsági csoport. |
> | Művelet | Microsoft.Network/applicationSecurityGroups/write | Egy alkalmazás biztonsági csoportot hoz létre, vagy frissíti a meglévő alkalmazás biztonsági csoporthoz. |
> | Művelet | Microsoft.Network/bgpServiceCommunities/read | Bgp-szolgáltatás Közösségek beolvasása |
> | Művelet | Microsoft.Network/checkTrafficManagerNameAvailability/action | A Traffic Manager relatív DNS-név elérhetőségének ellenőrzése. |
> | Művelet | Microsoft.Network/connections/delete | Deletes VirtualNetworkGatewayConnection |
> | Művelet | Microsoft.Network/connections/read | Gets VirtualNetworkGatewayConnection |
> | Művelet | Microsoft.Network/connections/sharedkey/action | ConnectionType(hypernet/routebased) SharedKey beolvasása |
> | Művelet | Microsoft.Network/connections/sharedKey/read | Gets VirtualNetworkGatewayConnection SharedKey |
> | Művelet | Microsoft.Network/connections/sharedKey/write | Létrehoz vagy frissít egy meglévő ConnectionType(hypernet/routebased) SharedKey |
> | Művelet | Microsoft.Network/connections/vpndeviceconfigurationscript/action | Lekérdezi a ConnectionType(hypernet/routebased) VPN-eszköz konfigurációja |
> | Művelet | Microsoft.Network/connections/write | Létrehoz vagy frissít egy meglévő ConnectionType(hypernet/routebased) |
> | Művelet | Microsoft.Network/ddosProtectionPlans/ddosProtectionPlanProxies/delete | DDoS védelem terv Proxy törlése |
> | Művelet | Microsoft.Network/ddosProtectionPlans/ddosProtectionPlanProxies/read | DDoS védelem megtervezése Proxy definíciójának beolvasása |
> | Művelet | Microsoft.Network/ddosProtectionPlans/ddosProtectionPlanProxies/write | Létrehoz egy DDoS védelem megtervezése Proxy vagy frissítések- és meglévő DDoS védelem megtervezése |
> | Művelet | Microsoft.Network/ddosProtectionPlans/delete | DDoS védelem terv törlése |
> | Művelet | Microsoft.Network/ddosProtectionPlans/join/action | DDoS védelmi tervet illesztése |
> | Művelet | Microsoft.Network/ddosProtectionPlans/read | Lekérdezi a DDoS védelem terv |
> | Művelet | Microsoft.Network/ddosProtectionPlans/write | DDoS védelem terv létrehozása vagy frissítése. a DDoS védelem megtervezése  |
> | Művelet | Microsoft.Network/dnsoperationresults/read | A DNS-művelet eredményének beolvasása |
> | Művelet | Microsoft.Network/dnsoperationstatuses/read | A DNS-művelet állapotának beolvasása  |
> | Művelet | Microsoft.Network/dnszones/A/delete | Eltávolítható az adott nevű és írja be a "A" a DNS-zónából. |
> | Művelet | Microsoft.Network/dnszones/A/read | A "A" típusú rekordkészlet beolvasása JSON formátumban. A rekordhalmaz rögzíti, valamint a TTL-t, a címkék és a etag listáját tartalmazza. |
> | Művelet | Microsoft.Network/dnszones/A/write | Hozzon létre vagy frissíthető a DNS-zónában "A" típusú. A megadott rekordok a rendszer lecseréli a rekordhalmaz aktuális rekordját. |
> | Művelet | Microsoft.Network/dnszones/AAAA/delete | Eltávolítható az adott nevű és írja be a "AAAA" a DNS-zónából. |
> | Művelet | Microsoft.Network/dnszones/AAAA/read | "AAAA", az a típusú rekordkészlet beolvasása JSON formátumban. A rekordhalmaz rögzíti, valamint a TTL-t, a címkék és a etag listáját tartalmazza. |
> | Művelet | Microsoft.Network/dnszones/AAAA/write | Hozzon létre vagy frissíthető a DNS-zónában "AAAA" típusú. A megadott rekordok a rendszer lecseréli a rekordhalmaz aktuális rekordját. |
> | Művelet | Microsoft.Network/dnszones/all/read | Lekérdezi a DNS-rekordhalmazok típusok között |
> | Művelet | Microsoft.Network/dnszones/CAA/delete | Eltávolítható az adott nevű és írja be a "CAA" a DNS-zónából. |
> | Művelet | Microsoft.Network/dnszones/CAA/read | "CAA", az a típusú rekordkészlet beolvasása JSON formátumban. A rekordkészlet tartalmazza a TTL-t, a címkék és az etag. |
> | Művelet | Microsoft.Network/dnszones/CAA/write | Hozzon létre vagy frissíthető a DNS-zónában "CAA" típusú. A megadott rekordok a rendszer lecseréli a rekordhalmaz aktuális rekordját. |
> | Művelet | Microsoft.Network/dnszones/CNAME/delete | Eltávolítható az adott nevű és írja be a "CNAME" a DNS-zónából. |
> | Művelet | Microsoft.Network/dnszones/CNAME/read | "CNAME", az a típusú rekordkészlet beolvasása JSON formátumban. A rekordkészlet tartalmazza a TTL-t, a címkék és az etag. |
> | Művelet | Microsoft.Network/dnszones/CNAME/write | Hozzon létre vagy frissíthető a DNS-zónában "CNAME" típusú. A megadott rekordok a rendszer lecseréli a rekordhalmaz aktuális rekordját. |
> | Művelet | Microsoft.Network/dnszones/delete | Törli a DNS-zóna JSON formátumban. A zóna tulajdonságai tartalmaznak címkéket, etag, numberOfRecordSets és maxnumberofrecordsets tulajdonságokat. |
> | Művelet | Microsoft.Network/dnszones/MX/delete | Eltávolítható az adott nevű és írja be az "MX" a DNS-zónából. |
> | Művelet | Microsoft.Network/dnszones/MX/read | "MX", az a típusú rekordkészlet beolvasása JSON formátumban. A rekordhalmaz rögzíti, valamint a TTL-t, a címkék és a etag listáját tartalmazza. |
> | Művelet | Microsoft.Network/dnszones/MX/write | Hozzon létre vagy frissíthető a DNS-zónában "MX" típusú. A megadott rekordok a rendszer lecseréli a rekordhalmaz aktuális rekordját. |
> | Művelet | Microsoft.Network/dnszones/NS/delete | Törli a DNS-rekordhalmaz NS típusú |
> | Művelet | Microsoft.Network/dnszones/NS/read | Gets DNS record set of type NS |
> | Művelet | Microsoft.Network/dnszones/NS/write | Létrehozza vagy frissíti a DNS NS típusú rekordhalmaz |
> | Művelet | Microsoft.Network/dnszones/providers/Microsoft.Insights/diagnosticSettings/read | A DNS-zóna diagnosztikai beállításainak beolvasása |
> | Művelet | Microsoft.Network/dnszones/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti a DNS-zóna diagnosztikai beállítások |
> | Művelet | Microsoft.Network/dnszones/providers/Microsoft.Insights/metricDefinitions/read | A DNS-zóna metrikai meghatározásainak beolvasása |
> | Művelet | Microsoft.Network/dnszones/PTR/delete | Eltávolítható az adott nevű és írja be a "PTR" a DNS-zónából. |
> | Művelet | Microsoft.Network/dnszones/PTR/read | "PTR", az a típusú rekordkészlet beolvasása JSON formátumban. A rekordhalmaz rögzíti, valamint a TTL-t, a címkék és a etag listáját tartalmazza. |
> | Művelet | Microsoft.Network/dnszones/PTR/write | Hozzon létre vagy frissíthető a DNS-zónában "PTR" típusú. A megadott rekordok a rendszer lecseréli a rekordhalmaz aktuális rekordját. |
> | Művelet | Microsoft.Network/dnszones/read | A DNS-zóna JSON formátumban beolvasása. A zóna tulajdonságai tartalmaznak címkéket, etag, numberOfRecordSets és maxnumberofrecordsets tulajdonságokat. Vegye figyelembe, hogy ez a parancs nem kéri le a rekordkészleteket, a zóna tartalmaz. |
> | Művelet | Microsoft.Network/dnszones/recordsets/read | Lekérdezi a DNS-rekordhalmazok típusok között |
> | Művelet | Microsoft.Network/dnszones/SOA/read | Gets DNS record set of type SOA |
> | Művelet | Microsoft.Network/dnszones/SOA/write | Létrehozza vagy frissíti a DNS-rekordhalmaz SOA típusú |
> | Művelet | Microsoft.Network/dnszones/SRV/delete | Eltávolítható az adott nevű és írja be a "SRV" a DNS-zónából. |
> | Művelet | Microsoft.Network/dnszones/SRV/read | "SRV", az a típusú rekordkészlet beolvasása JSON formátumban. A rekordhalmaz rögzíti, valamint a TTL-t, a címkék és a etag listáját tartalmazza. |
> | Művelet | Microsoft.Network/dnszones/SRV/write | SRV típusú rekordot csoport létrehozása vagy frissítése |
> | Művelet | Microsoft.Network/dnszones/TXT/delete | Eltávolítható az adott nevű és írja be a "TXT" a DNS-zónából. |
> | Művelet | Microsoft.Network/dnszones/TXT/read | "TXT", az a típusú rekordkészlet beolvasása JSON formátumban. A rekordhalmaz rögzíti, valamint a TTL-t, a címkék és a etag listáját tartalmazza. |
> | Művelet | Microsoft.Network/dnszones/TXT/write | Hozzon létre vagy frissíthető a DNS-zónában "TXT" típusú. A megadott rekordok a rendszer lecseréli a rekordhalmaz aktuális rekordját. |
> | Művelet | Microsoft.Network/dnszones/write | Hozzon létre, vagy frissítse a DNS-zónák erőforráscsoporton belül.  A címke van megadva a DNS-zóna erőforrás frissítésére szolgáló. Vegye figyelembe, hogy ez a parancs nem használható létrehozására vagy frissítésére a zónán belül rekordhalmazok. |
> | Művelet | Microsoft.Network/expressRouteCircuits/authorizations/delete | Törli az Express route-körnek engedélyezési |
> | Művelet | Microsoft.Network/expressRouteCircuits/authorizations/read | Lekérdezi az Express route-körnek engedély |
> | Művelet | Microsoft.Network/expressRouteCircuits/authorizations/write | Létrehozza vagy frissíti a meglévő Express route-körnek engedély |
> | Művelet | Microsoft.Network/expressRouteCircuits/delete | Az Express route-körnek törlése |
> | Művelet | Microsoft.Network/expressRouteCircuits/join/action | Az Express Route-körhöz illesztése |
> | Művelet | Microsoft.Network/expressRouteCircuits/peerings/arpTables/action | Az Express route-körnek társviszony-létesítés ArpTable beolvasása |
> | Művelet | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | Az Express route-körnek kapcsolat törlése |
> | Művelet | Microsoft.Network/expressRouteCircuits/peerings/connections/read | Lekérdezi az Express route-körnek kapcsolat |
> | Művelet | Microsoft.Network/expressRouteCircuits/peerings/connections/write | Létrehozza vagy frissíti az Express route-körnek meglévő kapcsolat erőforrás |
> | Művelet | Microsoft.Network/expressRouteCircuits/peerings/delete | Az Express route-körnek Társviszony törlése |
> | Művelet | Microsoft.Network/expressRouteCircuits/peerings/read | Lekérdezi az Express route-körnek társviszony-létesítés |
> | Művelet | Microsoft.Network/expressRouteCircuits/peerings/routeTables/action | Az Express route-körnek társviszony-létesítés Migrálták beolvasása |
> | Művelet | Microsoft.Network/expressRouteCircuits/peerings/routeTablesSummary/action | Az Express route-körnek társviszony-létesítés Migrálták összegzését beolvasása |
> | Művelet | Microsoft.Network/expressRouteCircuits/peerings/stats/read | Lekérdezi az Express route-körnek társviszony-létesítés statisztika |
> | Művelet | Microsoft.Network/expressRouteCircuits/peerings/write | Létrehoz vagy frissít egy meglévő Express route-körnek Társviszony |
> | Művelet | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/read | Az ExpressRoute-Kapcsolatcsoportok diagnosztikai beállításainak beolvasása |
> | Művelet | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti az ExpressRoute-Kapcsolatcsoportok tartozó diagnosztikai beállítások |
> | Művelet | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/logDefinitions/read | Az események lekérése ExpressRoute-Kapcsolatcsoportok |
> | Művelet | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/metricDefinitions/read | Az ExpressRoute-Kapcsolatcsoportok metrikai meghatározásainak beolvasása |
> | Művelet | Microsoft.Network/expressRouteCircuits/read | Az Express route-körnek beolvasása |
> | Művelet | Microsoft.Network/expressRouteCircuits/stats/read | Az Express route-körnek Stat beolvasása |
> | Művelet | Microsoft.Network/expressRouteCircuits/write | Létrehoz vagy frissít egy meglévő Express route-körnek |
> | Művelet | Microsoft.Network/expressRouteCrossConnections/delete | Törölje az Express Route telephelyek közötti kapcsolat |
> | Művelet | Microsoft.Network/expressRouteCrossConnections/join/action | Az Express Route illesztések telephelyek közötti kapcsolat |
> | Művelet | Microsoft.Network/expressRouteCrossConnections/peerings/arpTables/action | Egy Expressroute közötti kapcsolat társviszony-létesítés Arp-táblázat beolvasása |
> | Művelet | Microsoft.Network/expressRouteCrossConnections/peerings/delete | Egy Expressroute közötti kapcsolat Társviszony törlése |
> | Művelet | Microsoft.Network/expressRouteCrossConnections/peerings/read | Egy Expressroute közötti kapcsolat társviszony-létesítés beolvasása |
> | Művelet | Microsoft.Network/expressRouteCrossConnections/peerings/routeTables/action | Egy Expressroute közötti kapcsolat társviszony-létesítés útvonaltábla beolvasása |
> | Művelet | Microsoft.Network/expressRouteCrossConnections/peerings/routeTableSummary/action | Egy Expressroute közötti kapcsolat társviszony-létesítés útvonal tábla összegzés beolvasása |
> | Művelet | Microsoft.Network/expressRouteCrossConnections/peerings/write | Az Express Route telephelyek közötti kapcsolat társviszony-létesítés létrehozása vagy frissítése. egy meglévő Express Route telephelyek közötti kapcsolat társviszony-létesítés |
> | Művelet | Microsoft.Network/expressRouteCrossConnections/read | Expressroute beolvasása telephelyek közötti kapcsolat |
> | Művelet | Microsoft.Network/expressRouteCrossConnections/write | Hozzon létre vagy frissíthető Expressroute telephelyek közötti kapcsolat |
> | Művelet | Microsoft.Network/expressRouteServiceProviders/read | Lekérdezi az Express Route-szolgáltatók |
> | Művelet | Microsoft.Network/intendedPolicies/delete | Egy adott házirend törlése |
> | Művelet | Microsoft.Network/intendedPolicies/read | Lekérdezi az alkalmazni kívánt házirend leírása |
> | Művelet | Microsoft.Network/intendedPolicies/write | Létrehoz egy olyan házirendet, vagy frissíti a meglévő szánt házirend |
> | Művelet | Microsoft.Network/loadBalancers/backendAddressPools/join/action | A load balancer háttércímkészletének illesztése |
> | Művelet | Microsoft.Network/loadBalancers/backendAddressPools/read | A load balancer háttércímkészlet definíciójának beolvasása |
> | Művelet | Microsoft.Network/loadBalancers/delete | Törli a terheléselosztó |
> | Művelet | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | A terheléselosztó előtérbeli IP konfiguráció definíciójának beolvasása |
> | Művelet | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Művelettel illeszthető egy terheléselosztó bejövő forgalmat kezelő nat-készlete |
> | Művelet | Microsoft.Network/loadBalancers/inboundNatPools/read | Terheléselosztó bejövő forgalmat kezelő nat-készlet definíciójának beolvasása |
> | Művelet | Microsoft.Network/loadBalancers/inboundNatRules/delete | Terheléselosztó bejövő forgalmat kezelő nat-szabályának törlése |
> | Művelet | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Terheléselosztó bejövő forgalmat kezelő nat-szabályának illesztése |
> | Művelet | Microsoft.Network/loadBalancers/inboundNatRules/read | Terheléselosztó bejövő forgalmat kezelő nat-szabályát leíró definíció beolvasása |
> | Művelet | Microsoft.Network/loadBalancers/inboundNatRules/write | Terheléselosztó bejövő forgalmat kezelő nat-szabályának létrehozása vagy frissítése egy meglévő terheléselosztó bejövő nat-szabálya |
> | Művelet | Microsoft.Network/loadBalancers/loadBalancingRules/read | Terheléselosztó terheléselosztási terheléselosztási szabály definíciójának beolvasása |
> | Művelet | Microsoft.Network/loadBalancers/networkInterfaces/read | A terheléselosztó minden hálózati interfészen hivatkozások beolvasása |
> | Művelet | Microsoft.Network/loadBalancers/outboundNatRules/read | Terheléselosztó kimenő forgalmat kezelő nat szabály definíciójának beolvasása |
> | Művelet | Microsoft.Network/loadBalancers/probes/join/action | Lehetővé teszi, hogy a terheléselosztó mintavételt használatával. Például, az engedély healthProbe tulajdonság a Virtuálisgép-méretezési készlet is hivatkozhatnak a mintavételi. |
> | Művelet | Microsoft.Network/loadBalancers/probes/read | Terheléselosztói mintavétel beolvasása |
> | Művelet | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/read | A Load Balancer diagnosztikai beállításainak beolvasása |
> | Művelet | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti a terheléselosztási diagnosztikai beállításokat |
> | Művelet | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/logDefinitions/read | Terheléselosztó eseményeinek beolvasása |
> | Művelet | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/metricDefinitions/read | A terheléselosztó elérhető metrikai meghatározások beolvasása |
> | Művelet | Microsoft.Network/loadBalancers/read | Terheléselosztó definíciójának beolvasása |
> | Művelet | Microsoft.Network/loadBalancers/virtualMachines/read | A terheléselosztó virtuális gépek hivatkozások beolvasása |
> | Művelet | Microsoft.Network/loadBalancers/write | Terheléselosztó létrehozása vagy meglévő terheléselosztó frissítése |
> | Művelet | Microsoft.Network/localnetworkgateways/delete | LocalNetworkGateway törlése |
> | Művelet | Microsoft.Network/localnetworkgateways/read | Lekérdezi a LocalNetworkGateway |
> | Művelet | Microsoft.Network/localnetworkgateways/write | Létrehoz vagy frissít egy meglévő virtuális |
> | Művelet | Microsoft.Network/locations/availableDelegations/read | Lekérdezi a rendelkezésre álló delegálásokat |
> | Művelet | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | Ellenőrzi az elérését gyorsítja fel hálózati támogatás |
> | Művelet | Microsoft.Network/locations/checkDnsNameAvailability/read | Ellenőrzi, hogy DNS-címke érhető el a megadott helyen |
> | Művelet | Microsoft.Network/locations/operationResults/read | Aszinkrón POST vagy DELETE művelet eredményének beolvasása |
> | Művelet | Microsoft.Network/locations/operations/read | Egy aszinkron művelet állapotát jelző művelet erőforrás beolvasása |
> | Művelet | Microsoft.Network/locations/supportedVirtualMachineSizes/read | Lekérdezi a támogatott virtuális gépek méretét |
> | Művelet | Microsoft.Network/locations/usages/read | A szoftverhasználati mérési adatok erőforrások lekérése |
> | Művelet | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | Virtuális hálózati végpont elérhető szolgáltatások listájának lekérése |
> | Művelet | Microsoft.Network/networkInterfaces/delete | Hálózati kapcsolat törlése |
> | Művelet | Microsoft.Network/networkInterfaces/diagnosticIdentity/read | Lekérdezi az erőforrás diagnosztikai identitás |
> | Művelet | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | Hálózati biztonsági csoportok konfigurált a hálózati kapcsolat, a VM-lekérdezés |
> | Művelet | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | A virtuális gép hálózati illesztőjén konfigurált útválasztási táblázatot beolvasása |
> | Művelet | Microsoft.Network/networkInterfaces/ipconfigurations/read | A hálózati illesztő ip-konfiguráció definíciójának beolvasása  |
> | Művelet | Microsoft.Network/networkInterfaces/join/action | A virtuális gép illesztése hálózati illesztőhöz |
> | Művelet | Microsoft.Network/networkInterfaces/joinViaPrivateIp/action | Egy erőforrás csatlakoztatja egy hálózati adapteren keresztül a szolgáltatás társítása |
> | Művelet | Microsoft.Network/networkInterfaces/loadBalancers/read | A hálózati illesztő részét képező terheléselosztók beolvasása |
> | Művelet | Microsoft.Network/networkInterfaces/providers/Microsoft.Insights/metricDefinitions/read | Elérhető lekérdezi a hálózati adapter |
> | Művelet | Microsoft.Network/networkInterfaces/read | Hálózati illesztő definíciójának beolvasása.  |
> | Művelet | Microsoft.Network/networkInterfaces/serviceAssociations/delete | A szolgáltatás-hozzárendelés törlése |
> | Művelet | Microsoft.Network/networkInterfaces/serviceAssociations/read | A szolgáltatás társítás definíciójának beolvasása |
> | Művelet | Microsoft.Network/networkInterfaces/serviceAssociations/validate/action | Ellenőrzi a szolgáltatás társítása |
> | Művelet | Microsoft.Network/networkInterfaces/serviceAssociations/write | Társítást hoz létre új szolgáltatás, vagy módosítja a meglévő Service társítás |
> | Művelet | Microsoft.Network/networkInterfaces/write | Egy adott hálózati csatoló létrehozza vagy frissíti a meglévő hálózati illesztő.  |
> | Művelet | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | Alapértelmezett biztonsági szabály definíciójának beolvasása |
> | Művelet | Microsoft.Network/networkSecurityGroups/delete | A hálózati biztonsági csoport törlése |
> | Művelet | Microsoft.Network/networkSecurityGroups/join/action | Hálózati biztonsági csoport illesztése |
> | Művelet | Microsoft.Network/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | A hálózati biztonsági csoport diagnosztikai beállításainak beolvasása |
> | Művelet | Microsoft.Network/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | A hálózati biztonsági csoportok diagnosztikai beállításainak létrehozása vagy frissítése. Ezt a műveletet az adatelemzési erőforrás-szolgáltató egészíti ki adatokkal. |
> | Művelet | Microsoft.Network/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | A hálózati biztonsági csoport lekérése eseményeinek beolvasása |
> | Művelet | Microsoft.Network/networkSecurityGroups/read | Hálózati biztonsági csoport definíciójának beolvasása |
> | Művelet | Microsoft.Network/networkSecurityGroups/securityRules/delete | A biztonsági szabály törlése |
> | Művelet | Microsoft.Network/networkSecurityGroups/securityRules/read | Biztonsági szabály definíciójának beolvasása |
> | Művelet | Microsoft.Network/networkSecurityGroups/securityRules/write | A szabály létrehozása vagy meglévő biztonsági szabály frissítése |
> | Művelet | Microsoft.Network/networkSecurityGroups/write | Hálózati biztonsági csoport létrehozása vagy meglévő hálózati biztonsági csoport frissítése |
> | Művelet | Microsoft.Network/networkWatchers/availableProvidersList/action | Adja vissza az összes rendelkezésre álló internet szolgáltatók a megadott Azure-régió. |
> | Művelet | Microsoft.Network/networkWatchers/azureReachabilityReport/action | Adja vissza a relatív késés pontszám internetes szolgáltatók megadott helyről az Azure-régiók. |
> | Művelet | Microsoft.Network/networkWatchers/configureFlowLog/action | Konfigurálja a cél erőforráson folyamat naplózását. |
> | Művelet | Microsoft.Network/networkWatchers/connectionMonitors/delete | Figyelő kapcsolat törlése |
> | Művelet | Microsoft.Network/networkWatchers/connectionMonitors/providers/Microsoft.Insights/diagnosticSettings/read | Figyelő kapcsolat diagnosztikai beállításainak beolvasása |
> | Művelet | Microsoft.Network/networkWatchers/connectionMonitors/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti a kapcsolat figyelő diagnosztikai beállításai |
> | Művelet | Microsoft.Network/networkWatchers/connectionMonitors/providers/Microsoft.Insights/metricDefinitions/read | Figyelő kapcsolat elérhető metrikai meghatározások beolvasása |
> | Művelet | Microsoft.Network/networkWatchers/connectionMonitors/query/action | A lekérdezés megadott végpontok közötti kapcsolat ellenőrzése |
> | Művelet | Microsoft.Network/networkWatchers/connectionMonitors/read | Részletek a kapcsolat figyelője |
> | Művelet | Microsoft.Network/networkWatchers/connectionMonitors/start/action | Indítsa el a megadott végpontok közötti kapcsolat ellenőrzése |
> | Művelet | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | Állítsa le vagy felfüggesztése megadott végpontok közötti kapcsolat ellenőrzése |
> | Művelet | Microsoft.Network/networkWatchers/connectionMonitors/write | Egy kapcsolat figyelő létrehozása |
> | Művelet | Microsoft.Network/networkWatchers/connectivityCheck/action | Ellenőrzi a lehetőségét a közvetlen TCP-kapcsolatot a virtuális gép egy adott végpont, többek között a másik virtuális gép vagy egy tetszőleges távoli kiszolgálón. |
> | Művelet | Microsoft.Network/networkWatchers/delete | Törli a hálózati figyelőt |
> | Művelet | Microsoft.Network/networkWatchers/ipFlowVerify/action | Adja vissza, hogy a csomag engedélyezett vagy megtagadott vagy onnan az adott célhelyre. |
> | Művelet | Microsoft.Network/networkWatchers/lenses/delete | A fókuszok téma törlése |
> | Művelet | Microsoft.Network/networkWatchers/lenses/query/action | A megadott végpont a hálózati forgalom figyelése lekérdezés |
> | Művelet | Microsoft.Network/networkWatchers/lenses/read | Részletek a fókuszt |
> | Művelet | Microsoft.Network/networkWatchers/lenses/start/action | Indítsa el a megadott végpont a hálózati forgalom figyelése |
> | Művelet | Microsoft.Network/networkWatchers/lenses/stop/action | A megadott végpont a hálózati forgalom figyelése STOP/felfüggesztése |
> | Művelet | Microsoft.Network/networkWatchers/lenses/write | Létrehoz egy fókuszban |
> | Művelet | Microsoft.Network/networkWatchers/nextHop/action | A megadott cél és a cél IP-cím térjen vissza a következő ugrás típusa, és ezután az IP-cím legyen. |
> | Művelet | Microsoft.Network/networkWatchers/packetCaptures/delete | A csomagrögzítéssel törlése |
> | Művelet | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Lekérdezi a tulajdonságok és a csomag rögzítési erőforrás állapotával kapcsolatos adatokat. |
> | Művelet | Microsoft.Network/networkWatchers/packetCaptures/read | A csomag rögzítési definíciójának beolvasása |
> | Művelet | Microsoft.Network/networkWatchers/packetCaptures/stop/action | A futó csomag-rögzítési munkamenet leállítása. |
> | Művelet | Microsoft.Network/networkWatchers/packetCaptures/write | A csomagrögzítéssel létrehozása |
> | Művelet | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | Erőforrás-naplózás folyamat állapotának beolvasása. |
> | Művelet | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | Lekérdezi a korábban futtatott vagy jelenleg hibaelhárítási eredménye hibaelhárítási művelet futtatása. |
> | Művelet | Microsoft.Network/networkWatchers/read | A hálózati figyelő definíciójának beolvasása |
> | Művelet | Microsoft.Network/networkWatchers/securityGroupView/action | A konfigurált és hatékony hálózati biztonsági csoport szabálya a virtuális gép megtekintése. |
> | Művelet | Microsoft.Network/networkWatchers/topology/action | Lekérdezi a hálózati szintű áttekintés a erőforrásokat, és azok erőforráscsoportban. |
> | Művelet | Microsoft.Network/networkWatchers/troubleshoot/action | Egy hálózati erőforráshoz az Azure-ban végzett hibaelhárítás indítása. |
> | Művelet | Microsoft.Network/networkWatchers/write | A hálózati figyelőt létrehoz vagy frissít egy meglévő hálózati figyelőt |
> | Művelet | Microsoft.Network/operations/read | Rendelkezésre álló műveletek beolvasása |
> | Művelet | Microsoft.Network/publicIPAddresses/delete | Törli a nyilvános IP-cím. |
> | Művelet | Microsoft.Network/publicIPAddresses/join/action | A nyilvános IP-cím illesztése |
> | Művelet | Microsoft.Network/publicIPAddresses/loadBalancerPools/delete | A nyilvános IP-címet a terheléselosztó háttérkészletének törlése |
> | Művelet | Microsoft.Network/publicIPAddresses/loadBalancerPools/join/action | A nyilvános IP-címet a terheléselosztó háttérkészletének illesztése |
> | Művelet | Microsoft.Network/publicIPAddresses/loadBalancerPools/read | Nyilvános IP-cím terheléselosztó háttér készlet definíciójának beolvasása |
> | Művelet | Microsoft.Network/publicIPAddresses/loadBalancerPools/write | A nyilvános IP-címet a terheléselosztó háttérkészletének létrehoz vagy frissít egy meglévő nyilvános IP-címet a terheléselosztó háttérkészletének |
> | Művelet | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/read | A nyilvános IP-cím diagnosztikai beállításainak beolvasása |
> | Művelet | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/write | A nyilvános IP-cím diagnosztikai beállításainak létrehozása vagy frissítése |
> | Művelet | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/logDefinitions/read | A naplófájl-definíciói nyilvános IP-cím beszerzése |
> | Művelet | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/metricDefinitions/read | A metrikák definíciók nyilvános IP-cím beszerzése |
> | Művelet | Microsoft.Network/publicIPAddresses/read | A nyilvános IP-cím definíciójának beolvasása |
> | Művelet | Microsoft.Network/publicIPAddresses/write | A nyilvános IP-cím létrehozza vagy frissíti a meglévő nyilvános IP-címnek.  |
> | Művelet | Microsoft.Network/register/action | Az előfizetés regisztrálása |
> | Művelet | Microsoft.Network/routeFilters/delete | Egy útvonal szűrődefinícióban törlése |
> | Művelet | Microsoft.Network/routeFilters/join/action | Útvonal szűrő illesztése |
> | Művelet | Microsoft.Network/routeFilters/read | Útvonal szűrő definíciójának beolvasása |
> | Művelet | Microsoft.Network/routeFilters/routeFilterRules/delete | Törli a útvonal szűrő szabályát leíró definíció beolvasása |
> | Művelet | Microsoft.Network/routeFilters/routeFilterRules/read | Útvonal szűrési szabály definíciójának beolvasása |
> | Művelet | Microsoft.Network/routeFilters/routeFilterRules/write | Egy útvonal-szűrő szabályt hoz létre, vagy meglévő útvonal szűrési szabály frissítése |
> | Művelet | Microsoft.Network/routeFilters/write | Útvonal szűrő létrehoz vagy frissít egy meglévő rotue szűrő |
> | Művelet | Microsoft.Network/routeTables/delete | Útvonaltábla-definíció törlése |
> | Művelet | Microsoft.Network/routeTables/join/action | Egy útválasztási táblázatot illesztése |
> | Művelet | Microsoft.Network/routeTables/read | Útvonaltábla-definíció beolvasása |
> | Művelet | Microsoft.Network/routeTables/routes/delete | Útvonal-definíció törlése |
> | Művelet | Microsoft.Network/routeTables/routes/read | Útvonal definíciójának beolvasása |
> | Művelet | Microsoft.Network/routeTables/routes/write | Új útvonal létrehozása vagy meglévő útvonal frissítése |
> | Művelet | Microsoft.Network/routeTables/write | Útvonaltábla létrehozása vagy meglévő útvonaltábla frissítése |
> | Művelet | Microsoft.Network/securegateways/applicationRuleCollections/delete | Az alkalmazás-szabálygyűjtemény egy biztonságos átjáró törlése |
> | Művelet | Microsoft.Network/securegateways/applicationRuleCollections/read | Az alkalmazás-szabálygyűjtemény egy adott biztonságos átjáró beolvasása |
> | Művelet | Microsoft.Network/securegateways/applicationRuleCollections/write | Létrehozza vagy frissíti az alkalmazás-szabálygyűjtemény egy biztonságos átjáró |
> | Művelet | Microsoft.Network/securegateways/delete | Biztonságos átjáró törlése |
> | Művelet | Microsoft.Network/securegateways/networkRuleCollections/delete | Egy hálózati szabálygyűjteményhez biztonságos átjáró törlése |
> | Művelet | Microsoft.Network/securegateways/networkRuleCollections/read | Egy adott biztonságos átjáró egy hálózati szabálygyűjteményhez beolvasása |
> | Művelet | Microsoft.Network/securegateways/networkRuleCollections/write | Létrehozza vagy frissíti egy hálózati szabálygyűjteményhez biztonságos átjáró |
> | Művelet | Microsoft.Network/securegateways/read | Biztonságos átjáró beolvasása |
> | Művelet | Microsoft.Network/securegateways/write | Létrehozza vagy frissíti egy biztonságos átjáró |
> | Művelet | Microsoft.Network/serviceEndpointPolicies/delete | A szolgáltatási végpont házirend törlése |
> | Művelet | Microsoft.Network/serviceEndpointPolicies/join/action | A szolgáltatási végpont házirend illesztése |
> | Művelet | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | A szolgáltatási végpont házirendek alhálózat illesztése |
> | Művelet | Microsoft.Network/serviceEndpointPolicies/read | Lekérdezi a szolgáltatási végpont házirend leírása |
> | Művelet | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | A szolgáltatási végpont házirend-definíció törlése |
> | Művelet | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | Lekérdezi a szolgáltatási végpont házirend Definition visszafejtés |
> | Művelet | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | Szolgáltatási végpont házirend-definíció létrehozása vagy egy meglévő szolgáltatási végpont házirend-definíció frissítése |
> | Művelet | Microsoft.Network/serviceEndpointPolicies/write | A szolgáltatási végpont házirend létrehozza vagy frissíti a meglévő szolgáltatási végpont házirend |
> | Művelet | Microsoft.Network/trafficManagerGeographicHierarchies/read | Lekérdezi a régiókban, amelyek együtt a földrajzi forgalom-útválasztási módszert tartalmazó Traffic Manager Geographic hierarchia |
> | Művelet | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | Azure-végpont törlése egy meglévő Traffic Manager-profil. A TRAFFIC Manager útválasztási forgalmat a törölt Azure-beli végpont le fog állni. |
> | Művelet | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | Lekérdezi egy Azure-végpontot, amely a Traffic Manager-profil, beleértve az adott Azure-beli végpont tulajdonságait. |
> | Művelet | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | Új Azure-végpont hozzáadása a meglévő Traffic Manager-profilt, vagy egy meglévő Azure-beli végpont, hogy a Traffic Manager-profil tulajdonságainak frissítéséhez. |
> | Művelet | Microsoft.Network/trafficManagerProfiles/delete | A Traffic Manager-profil törlése. A Traffic Manager-profil társított összes beállítást elvész, és a profil már nem használható a forgalom irányításához. |
> | Művelet | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | Külső végpont törlése egy meglévő Traffic Manager-profil. A TRAFFIC Manager útválasztási forgalmat a törölt külső végpont le fog állni. |
> | Művelet | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | Lekérdezi a külső végpont, amely a Traffic Manager-profil, beleértve a külső végpont tulajdonságait. |
> | Művelet | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | Új külső végpont hozzáadása a meglévő Traffic Manager-profilt, vagy egy meglévő külső végpont, hogy a Traffic Manager-profil tulajdonságainak frissítéséhez. |
> | Művelet | Microsoft.Network/trafficManagerProfiles/heatMaps/read | A Traffic Manager Hőtérkép lekérdezi a megadott Traffic Manager-profil helyét és a forrás IP-címekként számát és a késés adatait tartalmazó. |
> | Művelet | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | A beágyazott végpont törlése egy meglévő Traffic Manager-profil. A TRAFFIC Manager útválasztási forgalmat a törölt beágyazott végpont le fog állni. |
> | Művelet | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | Lekérdezi egy beágyazott végpontot, amely a Traffic Manager-profil, beleértve, hogy a beágyazott végpont tulajdonságait. |
> | Művelet | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | Adja hozzá új beágyazott végpont egy már meglévő Traffic Manager-profilt, vagy egy meglévő beágyazott végpont a Traffic Manager-profil tulajdonságainak frissítéséhez. |
> | Művelet | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/read | A Traffic Manager diagnosztikai beállításainak beolvasása |
> | Művelet | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti a Traffic Manager diagnosztikai beállítások, a művelet nem hozhatók insights erőforrás-szolgáltató. |
> | Művelet | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/logDefinitions/read | A Traffic Manager eseményeinek beolvasása |
> | Művelet | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/metricDefinitions/read | Elérhető metrikai meghatározások beolvasása a Traffic Manager. |
> | Művelet | Microsoft.Network/trafficManagerProfiles/read | A Traffic Manager-profil konfigurációjának beolvasása. Ez magában foglalja a DNS-beállítások, forgalom útválasztásához tartozó beállításokat, végpontmonitoring beállításai, és továbbítja a Traffic Manager-profil végpontok listáját. |
> | Művelet | Microsoft.Network/trafficManagerProfiles/write | Traffic Manager-profil létrehozása, vagy egy meglévő Traffic Manager-profil konfigurációjának módosítása.<br>Ez magában foglalja a engedélyezésére, vagy egy profil letiltása folyamatban van, és módosítja a DNS-beállítások, a forgalom útválasztásához tartozó beállításokat vagy a végpontmonitoring beállításai.<br>A Traffic Manager-profil által kezelendő végpontok hozzáadott, eltávolított, engedélyezhető vagy le van tiltva. |
> | Művelet | Microsoft.Network/trafficManagerUserMetricsKeys/delete | A valós idejű felhasználói metrikák gyűjtemény használt előfizetés szintű kulcs törlése. |
> | Művelet | Microsoft.Network/trafficManagerUserMetricsKeys/read | A valós idejű felhasználói metrikák gyűjtemény használt előfizetés szintű kulcs lekérése. |
> | Művelet | Microsoft.Network/trafficManagerUserMetricsKeys/write | Létrehoz egy új előfizetés-szintű kulcs valós idejű felhasználói metrikák gyűjtemény használható. |
> | Művelet | Microsoft.Network/unregister/action | Az előfizetés regisztrációjának törlése |
> | Művelet | Microsoft.Network/virtualHubs/delete | Egy virtuális központi törlése |
> | Művelet | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | Törli a HubVirtualNetworkConnection |
> | Művelet | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | Egy HubVirtualNetworkConnection beolvasása |
> | Művelet | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | Hozható létre vagy frissíthető egy HubVirtualNetworkConnection |
> | Művelet | Microsoft.Network/virtualHubs/read | Egy virtuális központi beolvasása |
> | Művelet | Microsoft.Network/virtualHubs/write | Létrehozni vagy frissíteni a virtuális Hub |
> | Művelet | Microsoft.Network/virtualnetworkgateways/Connections/Read | Get VirtualNetworkGatewayConnection |
> | Művelet | Microsoft.Network/virtualNetworkGateways/delete | Törli az átjárónak |
> | Művelet | Microsoft.Network/virtualnetworkgateways/generatevpnclientpackage/Action | Az átjárónak VpnClient csomagot |
> | Művelet | Microsoft.Network/virtualnetworkgateways/generatevpnprofile/Action | Pedig VpnProfile csomag létrehozása |
> | Művelet | Microsoft.Network/virtualnetworkgateways/getadvertisedroutes/Action | Lekérdezi pedig hirdetett útvonalakat |
> | Művelet | Microsoft.Network/virtualnetworkgateways/getbgppeerstatus/Action | Lekérdezi a pedig a bgp társ állapota |
> | Művelet | Microsoft.Network/virtualnetworkgateways/getlearnedroutes/Action | Megtudta, pedig útvonalak beolvasása |
> | Művelet | Microsoft.Network/virtualnetworkgateways/getvpnclientipsecparameters/Action | Vpnclient Ipsec paramétereinek lekérése pedig P2S-ügyfél. |
> | Művelet | Microsoft.Network/virtualnetworkgateways/getvpnprofilepackageurl/Action | Lekérdezi egy előre generált VPN-ügyfélcsomag profil URL-címe |
> | Művelet | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/read | A virtuális hálózati átjáró diagnosztikai beállításainak beolvasása |
> | Művelet | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti a virtuális hálózati átjáró diagnosztikai beállítások, a művelet nem hozhatók insights erőforrás-szolgáltató. |
> | Művelet | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/logDefinitions/read | Virtuális hálózati átjáró eseményeinek beolvasása |
> | Művelet | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/metricDefinitions/read | A virtuális hálózati átjáró elérhető metrikai meghatározások beolvasása |
> | Művelet | Microsoft.Network/virtualNetworkGateways/read | Lekérdezi a pedig |
> | Művelet | Microsoft.Network/virtualnetworkgateways/reset/Action | Alaphelyzetbe állítja a pedig |
> | Művelet | Microsoft.Network/virtualnetworkgateways/setvpnclientipsecparameters/Action | Vpnclient Ipsec paramétert meg az átjárónak P2S-ügyfél. |
> | Művelet | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | Listák támogatott VPN-eszközök |
> | Művelet | Microsoft.Network/virtualNetworkGateways/write | Létrehozza vagy frissíti az átjárónak |
> | Művelet | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | Ellenőrizze, hogy a megadott virtuális hálózati érhető el-e IP-cím |
> | Művelet | Microsoft.Network/virtualNetworks/customViews/get/action | A virtuális hálózati egyéni nézet tartalmának lekérdezése |
> | Művelet | Microsoft.Network/virtualNetworks/customViews/read | Virtuális hálózat egyéni nézet definíciójának lekérdezése |
> | Művelet | Microsoft.Network/virtualNetworks/delete | Virtuális hálózat törlése |
> | Művelet | Microsoft.Network/virtualNetworks/peer/action | A virtuális hálózat egy másik virtuális hálózathoz állomásokhoz |
> | Művelet | Microsoft.Network/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/read | A virtuális hálózat diagnosztikai beállításainak beolvasása |
> | Művelet | Microsoft.Network/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/write | A virtuális hálózat diagnosztikai beállításainak létrehozása vagy frissítése |
> | Művelet | Microsoft.Network/virtualNetworks/providers/Microsoft.Insights/logDefinitions/read | A naplófájl-definíciói virtuális hálózat beolvasása |
> | Művelet | Microsoft.Network/virtualNetworks/read | A virtuális hálózat definíciójának beolvasása |
> | Művelet | Microsoft.Network/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | Törli a virtuális hálózati társviszony-létesítési proxy |
> | Művelet | Microsoft.Network/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | A virtuális hálózati társviszony-létesítés proxydefiníció beolvasása |
> | Művelet | Microsoft.Network/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | A virtuális hálózati társviszony-létesítési proxy létrehoz vagy frissít egy meglévő virtuális hálózati társviszony-létesítési proxy |
> | Művelet | Microsoft.Network/virtualNetworks/subnets/delete | Virtuális hálózati alhálózat törlése |
> | Művelet | Microsoft.Network/virtualNetworks/subnets/join/action | Egy virtuális hálózathoz csatlakozik |
> | Művelet | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Alhálózathoz csatlakozik az erőforrás, például a tárfiók, vagy az SQL-adatbázis. |
> | Művelet | Microsoft.Network/virtualNetworks/subnets/read | Virtuális hálózati alhálózat definíciójának beolvasása |
> | Művelet | Microsoft.Network/virtualNetworks/subnets/resourceNavigationLinks/delete | Egy erőforrás-navigációs hivatkozás törlése |
> | Művelet | Microsoft.Network/virtualNetworks/subnets/resourceNavigationLinks/read | Az erőforrás-navigációs hivatkozás definíciójának beolvasása |
> | Művelet | Microsoft.Network/virtualNetworks/subnets/resourceNavigationLinks/write | Egy erőforrás-navigációs hivatkozás létrehoz vagy frissít egy meglévő erőforrás-navigációs hivatkozás |
> | Művelet | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/delete | A szolgáltatás társítás hivatkozás törlése |
> | Művelet | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/details/read | A szolgáltatás társítás hivatkozás részletes definíciójának beolvasása |
> | Művelet | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/read | A szolgáltatás társítás hivatkozás definíciójának beolvasása |
> | Művelet | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/validate/action | Ellenőrzi a szolgáltatás társítás hivatkozás |
> | Művelet | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/write | Létrehoz egy szolgáltatás társítás hivatkozást vagy frissíti a meglévő Service társítás hivatkozás |
> | Művelet | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | Virtuális hálózati alhálózat virtuális gépeire mutató hivatkozások beolvasása |
> | Művelet | Microsoft.Network/virtualNetworks/subnets/write | Virtuális hálózati alhálózat létrehozása vagy egy meglévő virtuális hálózati alhálózat frissítése |
> | Művelet | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/delete | Címkézett forgalom ügyféllel törlése |
> | Művelet | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/read | A forgalom fogyasztói címkézett definíciójának beolvasása |
> | Művelet | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/validate/action | Ellenőrzi az címkézett forgalom ügyféllel |
> | Művelet | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/write | Címkézett forgalom ügyféllel létrehoz vagy frissít egy meglévő címkézett forgalom fogyasztói |
> | Művelet | Microsoft.Network/virtualNetworks/usages/read | Az IP-módjait minden, a virtuális hálózati alhálózat beolvasása |
> | Művelet | Microsoft.Network/virtualNetworks/virtualMachines/read | Virtuális hálózat virtuális gépeire mutató hivatkozások beolvasása |
> | Művelet | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Törli a virtuális hálózati társviszony-létesítés |
> | Művelet | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | Virtuális hálózati társviszony-létesítési definíciójának beolvasása |
> | Művelet | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Virtuális hálózati társviszony-létesítés létrehozása vagy frissítése. egy meglévő virtuális hálózati társviszony |
> | Művelet | Microsoft.Network/virtualNetworks/write | Létrehoz egy virtuális hálózatot, vagy meglévő virtuális hálózat frissítése |
> | Művelet | Microsoft.Network/virtualNetworkTaps/delete | Koppintson a virtuális hálózat törlése |
> | Művelet | Microsoft.Network/virtualNetworkTaps/join/action | A virtuális hálózati tap illesztése |
> | Művelet | Microsoft.Network/virtualNetworkTaps/read | Virtuális hálózati Tap beolvasása |
> | Művelet | Microsoft.Network/virtualNetworkTaps/write | Létrehozni vagy frissíteni a virtuális hálózati Tap |
> | Művelet | Microsoft.Network/virtualWans/delete | Törli a virtuális Wan |
> | Művelet | Microsoft.Network/virtualWans/read | Get virtuális a Wan |
> | Művelet | Microsoft.Network/virtualWans/virtualHubProxies/delete | Egy virtuális központi proxy törlése |
> | Művelet | Microsoft.Network/virtualWans/virtualHubProxies/read | Egy virtuális központi proxy definíciójának beolvasása |
> | Művelet | Microsoft.Network/virtualWans/virtualHubProxies/write | Egy virtuális központi proxy létrehoz vagy frissít egy virtuális központi proxy |
> | Művelet | Microsoft.Network/virtualWans/virtualHubs/read | Minden virtuális hubok hivatkozó virtuális Wan lekérdezi. |
> | Művelet | Microsoft.Network/virtualwans/vpnconfiguration/action | Lekérdezi a VPN-konfiguráció |
> | Művelet | Microsoft.Network/virtualWans/vpnSiteProxies/delete | Törli a Vpn-hely proxy |
> | Művelet | Microsoft.Network/virtualWans/vpnSiteProxies/read | Vpn-hely proxy definíciójának beolvasása |
> | Művelet | Microsoft.Network/virtualWans/vpnSiteProxies/write | A Vpn-hely proxy létrehozása vagy frissítése egy Vpn-hely proxy |
> | Művelet | Microsoft.Network/virtualWans/vpnSites/read | Lekérdezi egy virtuális Wan hivatkozó összes VPN-hely. |
> | Művelet | Microsoft.Network/virtualWans/write | Létrehozni vagy frissíteni a virtuális Wan |
> | Művelet | Microsoft.Network/vpnGateways/read | Lekérdezi a A(z). |
> | Művelet | microsoft.network/vpnGateways/vpnConnections/read | Lekérdezi a VPN-kapcsolatot. |
> | Művelet | microsoft.network/vpnGateways/vpnConnections/write | A VPN-kapcsolat helyezi. |
> | Művelet | Microsoft.Network/vpnGateways/write | A A(z) helyezi. |
> | Művelet | Microsoft.Network/vpnsites/delete | Törli a Vpn-hely erőforrást. |
> | Művelet | Microsoft.Network/vpnsites/read | A Vpn-hely erőforrás lekérése. |
> | Művelet | Microsoft.Network/vpnsites/write | Létrehozza vagy frissíti a Vpn-hely erőforrás. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | Adott névtér-erőforrásnév elérhetőségének ellenőrzése a Notification Hub szolgáltatásban. |
> | Művelet | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | A névtér-engedélyezési szabályok leírásai listájának beolvasása. |
> | Művelet | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | Namespace engedélyezési szabály törlése. Az alapértelmezett Namespace engedélyezési szabály nem törölhető.  |
> | Művelet | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | A névtérhez tartozó kapcsolati karakterlánc beolvasása |
> | Művelet | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | A névtér-engedélyezési szabályok leírásai listájának beolvasása. |
> | Művelet | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | Egy névtér-engedélyezési szabály elsődleges vagy másodlagos kulcsának újragenerálása; meg kell adni az újragenerálni kívánt kulcsot |
> | Művelet | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | A Namespace szintű engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és másodlagos kulcsok lehet frissíteni. |
> | Művelet | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | Adott értesítésiközpont-név elérhetőségének ellenőrzése egy névtérben. |
> | Művelet | Microsoft.NotificationHubs/Namespaces/Delete | Egy névtérerőforrás törlése |
> | Művelet | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | Az értesítésiközpont-engedélyezési szabályok listájának beolvasása |
> | Művelet | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Értesítésiközpont-engedélyezési szabályok törlése |
> | Művelet | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | Az értesítési központhoz tartozó kapcsolati karakterlánc beolvasása |
> | Művelet | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | Az értesítésiközpont-engedélyezési szabályok listájának beolvasása |
> | Művelet | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Egy értesítésiközpont-engedélyezési szabály elsődleges vagy másodlagos kulcsának újragenerálása; meg kell adni az újragenerálni kívánt kulcsot |
> | Művelet | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | Értesítési központ engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és másodlagos kulcsok lehet frissíteni. |
> | Művelet | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | Leküldéses tesztértesítés küldése. |
> | Művelet | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | Értesítésiközpont-erőforrás törlése |
> | Művelet | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | Namespace metrikák erőforrás leírása listájának beolvasása |
> | Művelet | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Minden értesítési központ PNS hitelesítő adatainak lekérése. Ez magában foglalja a wns-ből, a MPNS, a APNS, a GCM és a Baidu hitelesítő adatokat |
> | Művelet | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | Az értesítésiközpont-erőforrások leírásai listájának beolvasása |
> | Művelet | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Hozzon létre egy értesítési központot, és a tulajdonságok frissítése. A Tulajdonságok főként PNS hitelesítő adatok közé tartozik. Az engedélyezési szabályok és a TTL-t |
> | Művelet | Microsoft.NotificationHubs/Namespaces/read | A névtérerőforrások leírásai listájának beolvasása |
> | Művelet | Microsoft.NotificationHubs/Namespaces/write | Namespace erőforrás létrehozása és frissítése a tulajdonságait. Címkék és a kapacitás, a Namespace nem frissíthető tulajdonságait. |
> | Művelet | Microsoft.NotificationHubs/register/action | A Notification Hubs-erőforrásszolgáltatóra való előfizetés regisztrálása, valamint a névterek és a Notification Hubs-példányok létrehozásának lehetővé tétele |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.OperationalInsights/linkTargets/read | Felsorolja a meglévő fiókokat, amelyek nem kapcsolódnak az Azure-előfizetéssel. Az Azure-előfizetés összekapcsolása egy munkaterület, egy felhasználói azonosítót, ez a művelet az ügyfél-azonosító tulajdonsággal, a munkaterület létrehozása művelet által visszaadott használja. |
> | Művelet | Microsoft.OperationalInsights/register/action | Egy erőforrás-szolgáltató előfizetés regisztrálásához. |
> | Művelet | Microsoft.OperationalInsights/workspaces/analytics/query/action | A keresés új motor használatával. |
> | Művelet | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | Keresési séma V2 beolvasása. |
> | Művelet | Microsoft.OperationalInsights/workspaces/api/query/action | A keresés új motor használatával. |
> | Művelet | Microsoft.OperationalInsights/workspaces/api/query/schema/read | Keresési séma V2 beolvasása. |
> | Művelet | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | A konfigurációs hatókörben törlése |
> | Művelet | Microsoft.OperationalInsights/workspaces/configurationScopes/read | A konfigurációs hatókörben beolvasása |
> | Művelet | Microsoft.OperationalInsights/workspaces/configurationScopes/write | Konfigurációs hatókör megadása |
> | Művelet | Microsoft.OperationalInsights/workspaces/datasources/delete | Törölje az adott munkaterületen adatforrásokhoz. |
> | Művelet | Microsoft.OperationalInsights/workspaces/datasources/read | Adatforrások beolvasása az adott munkaterületen. |
> | Művelet | Microsoft.OperationalInsights/workspaces/datasources/write | Az adott munkaterületen Adatforrás létrehozása/frissítése. |
> | Művelet | Microsoft.OperationalInsights/workspaces/delete | A munkaterület törlése. Ha a munkaterületet lett csatolva egy meglévő munkaterület a létrehozás időpontjában volt csatolva a munkaterület nem törlődik. |
> | Művelet | Microsoft.OperationalInsights/workspaces/generateregistrationcertificate/action | A munkaterület regisztrációs tanúsítványt hoz létre. Ezzel a tanúsítvánnyal a munkaterületen a Microsoft System Center Operation Manager csatlakozni. |
> | Művelet | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | Egy intelligence Pack csomagot egy adott munkaterület letiltja. |
> | Művelet | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | Lehetővé teszi, hogy egy adott munkaterület egy intelligence Pack csomagot. |
> | Művelet | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | Megjeleníti egy adott worksapce esetében az eszközintelligencia-csomagokat, és is megjeleníti, hogy a csomag engedélyezett vagy letiltott adott munkaterület. |
> | Művelet | Microsoft.OperationalInsights/workspaces/linkedServices/delete | A szolgáltatás kapcsolódó törlése megadott munkaterületen. |
> | Művelet | Microsoft.OperationalInsights/workspaces/linkedServices/read | A társított szolgáltatások munkaterület megadott. |
> | Művelet | Microsoft.OperationalInsights/workspaces/linkedServices/write | Létrehozása/frissítése kapcsolódó szolgáltatások az adott munkaterületen. |
> | Művelet | Microsoft.OperationalInsights/workspaces/listKeys/action | Lekéri a listában kulcsok a munkaterületen. Ezek a kulcsok segítségével a Microsoft az Operational Insights-ügynököket a munkaterülethez csatlakoztatandó. |
> | Művelet | Microsoft.OperationalInsights/workspaces/listKeys/read | Lekéri a listában kulcsok a munkaterületen. Ezek a kulcsok segítségével a Microsoft az Operational Insights-ügynököket a munkaterülethez csatlakoztatandó. |
> | Művelet | Microsoft.OperationalInsights/workspaces/managementGroups/read | System Center Operations Manager felügyeleti csoport csatlakoztatva a munkaterület neve és metaadatok beolvasása. |
> | Művelet | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | A metrika definíciók lekérése a munkaterületen |
> | Művelet | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | A felhasználói értesítés beállításait a munkaterület törlése. |
> | Művelet | Microsoft.OperationalInsights/workspaces/notificationSettings/read | A felhasználói értesítés beállításait a munkaterület beolvasása. |
> | Művelet | Microsoft.OperationalInsights/workspaces/notificationSettings/write | Állítsa be a felhasználói értesítés beállításait a munkaterületen. |
> | Művelet | Microsoft.OperationalInsights/workspaces/purge/action | Megadott adatok törléséhez a munkaterület |
> | Művelet | Microsoft.OperationalInsights/workspaces/read | Lekérdezi a meglévő-munkaterülettel |
> | Művelet | Microsoft.OperationalInsights/workspaces/savedSearches/delete | Törli a mentett keresési lekérdezés |
> | Művelet | Microsoft.OperationalInsights/workspaces/savedSearches/read | Lekérdezi a mentett keresési lekérdezés |
> | Művelet | Microsoft.OperationalInsights/workspaces/savedSearches/write | A mentett keresési lekérdezést hoz létre |
> | Művelet | Microsoft.OperationalInsights/workspaces/schema/read | A keresési séma lekérése a munkaterületen.  Keresési séma kitett mezőket és a típusukat tartalmazza. |
> | Művelet | Microsoft.OperationalInsights/workspaces/search/action | Keresési lekérdezés végrehajtása |
> | Művelet | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Lekéri a megosztott kulcsok a munkaterületen. Ezek a kulcsok segítségével a Microsoft az Operational Insights-ügynököket a munkaterülethez csatlakoztatandó. |
> | Művelet | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Lekéri a megosztott kulcsok a munkaterületen. Ezek a kulcsok segítségével a Microsoft az Operational Insights-ügynököket a munkaterülethez csatlakoztatandó. |
> | Művelet | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | A tárolási konfiguráció törlése. Ez a jövőben nem Microsoft Operational Insights adatok beolvasása a tárfiókból. |
> | Művelet | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/read | Lekérdezi a tárolási konfiguráció. |
> | Művelet | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/write | Létrehoz egy új tárolási konfigurációt. Ezek a beállítások segítségével olvasnak be adatokat egy hely a meglévő tárfiókot. |
> | Művelet | Microsoft.OperationalInsights/workspaces/usages/read | Lekérdezi a használati adatok egy munkaterület, beleértve a munkaterület által beolvasott adatok mennyiségét. |
> | Művelet | Microsoft.OperationalInsights/workspaces/write | Új munkaterület vagy egy meglévő munkaterület mutató hivatkozások azáltal, hogy az ügyfél-azonosítója a meglévő munkaterületről hozható létre. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.OperationsManagement/managementAssociations/delete | Törölje a meglévő felügyeleti társítása |
> | Művelet | Microsoft.OperationsManagement/managementAssociations/read | Meglévő felügyeleti társítása beolvasása |
> | Művelet | Microsoft.OperationsManagement/managementAssociations/write | Hozzon létre egy új felügyeleti társítása |
> | Művelet | Microsoft.OperationsManagement/managementConfigurations/delete | Törölje a meglévő felügyeleti Configuratin |
> | Művelet | Microsoft.OperationsManagement/managementConfigurations/read | Meglévő felügyeleti konfiguráció beolvasása |
> | Művelet | Microsoft.OperationsManagement/managementConfigurations/write | Hozzon létre egy új felügyeleti konfiguráció |
> | Művelet | Microsoft.OperationsManagement/register/action | Egy erőforrás-szolgáltató előfizetés regisztrálásához. |
> | Művelet | Microsoft.OperationsManagement/solutions/delete | Törölje a meglévő OMS-megoldás |
> | Művelet | Microsoft.OperationsManagement/solutions/read | Kilépés az OMS-megoldás beszerzése |
> | Művelet | Microsoft.OperationsManagement/solutions/write | Új OMS-megoldás létrehozása |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.PolicyInsights/policyEvents/queryResults/action | Házirend-eseményekkel kapcsolatos információk lekérdezése. |
> | Művelet | Microsoft.PolicyInsights/policyStates/queryResults/action | Házirend állapotok kapcsolatos információk lekérdezése. |
> | Művelet | Microsoft.PolicyInsights/policyStates/summarize/action | Lekérdezés házirend legújabb állapotok kapcsolatos összegző információkat. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.Portal/dashboards/delete | Az irányítópult eltávolítása az előfizetésből. |
> | Művelet | Microsoft.Portal/dashboards/read | Az előfizetéshez tartozó irányítópultok beolvasása. |
> | Művelet | Microsoft.Portal/dashboards/write | Irányítópult felvétele vagy átalakítása előfizetéssé. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.PowerBIDedicated/capacities/checkNameAvailability/action | Ellenőrzi, hogy a Power BI dedikált kapacitás a megadott név érvényes, és nincs használatban. |
> | Művelet | Microsoft.PowerBIDedicated/capacities/delete | Törli a Power BI dedikált kapacitás. |
> | Művelet | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Művelet | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Művelet | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/logDefinitions/read | A Power BI dedikált kapacitás naplók beolvasása. |
> | Művelet | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/metricDefinitions/read | A Power BI dedikált kapacitás elérhető metrikai meghatározások beolvasása. |
> | Művelet | Microsoft.PowerBIDedicated/capacities/read | A megadott Power BI dedikált kapacitás adatainak beolvasása. |
> | Művelet | Microsoft.PowerBIDedicated/capacities/resume/action | Folytatja a kapacitást. |
> | Művelet | Microsoft.PowerBIDedicated/capacities/skus/read | A kapacitás elérhető SKU információinak lekérése |
> | Művelet | Microsoft.PowerBIDedicated/capacities/suspend/action | Felfüggeszti a kapacitást. |
> | Művelet | Microsoft.PowerBIDedicated/capacities/write | Létrehozza vagy frissíti a megadott Power BI dedikált kapacitásának kihasználásához. |
> | Művelet | Microsoft.PowerBIDedicated/locations/operationresults/read | A megadott művelet eredményének adatainak beolvasása. |
> | Művelet | Microsoft.PowerBIDedicated/locations/operationstatuses/read | A megadott műveleti állapotának adatainak beolvasása. |
> | Művelet | Microsoft.PowerBIDedicated/operations/read | A műveletek adatainak beolvasása |
> | Művelet | Microsoft.PowerBIDedicated/register/action | A Power BI dedikált erőforrás-szolgáltató regisztrálása. |
> | Művelet | Microsoft.PowerBIDedicated/skus/read | Az SKU adatainak beolvasása |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.RecoveryServices/locations/allocatedStamp/read | A Lefoglalt bélyegző beolvasása egy belső művelet, melyet a szolgáltatás használ |
> | Művelet | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp egy szolgáltatás által használt belső művelet |
> | Művelet | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Művelet | Microsoft.RecoveryServices/locations/backupStatus/action | Helyreállítási szolgáltatások tárolók a biztonsági mentés állapotának ellenőrzése |
> | Művelet | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Szolgáltatások ellenőrzése |
> | Művelet | Microsoft.RecoveryServices/operations/read | Műveletet az erőforrás-szolgáltató műveleteinek listáját adja vissza. |
> | Művelet | Microsoft.RecoveryServices/register/action | Regiszterekben előfizetést az adott erőforrás-szolgáltató |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupconfig/read | A helyreállításhoz adja vissza konfigurációs szolgáltatások tárolóban. |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupconfig/write | Frissítések konfigurációja helyreállítási szolgáltatások tárolóban. |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupEngines/read | A tárolóban regisztrált biztonsági másolatokat kezelő kiszolgálókat adja vissza. |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | A biztonságimásolat-készítő védelmi leképezés létrehozásához |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | A művelet állapotának beolvasása |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Az összes védhető tároló beolvasása |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/delete | A regisztrált tárolójának törlése |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Hajtsa végre a lekérdezést a tárolóban futó alkalmazások és szolgáltatások |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | A tárolóban lévő összes beolvasásának |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Beolvassa a védelmi tárolón végrehajtott művelet eredményét. |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Biztonsági másolat készítése egy védett elemről. |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | Törli a védett elem |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Beolvassa a védett elemeken végrehajtott művelet eredményét. |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | A védett elemeken végrehajtott művelet állapotát adja vissza. |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | A védett elem kapcsolatobjektum-adatait adja vissza |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Védett elem rendelkezés azonnali elem helyreállítása |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Védett elemek helyreállítási pontjainak beolvasása. |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Védett elemek helyreállítási pontjainak visszaállítása. |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Védett elem azonnali elem helyreállítása visszavonása |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Biztonsági mentési védett típusú elem létrehozása |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Minden regisztrált tárolókat ad vissza |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Tárolót hoz létre regisztrált |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Frissíti a tároló listáját |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | A feladat megszakítása |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | A feladatművelet eredményét adja vissza. |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupJobs/read | Minden feladat objektumot ad vissza |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportálási feladat |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Az exportálási feladat művelet eredményét adja vissza. |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | A Recovery Services-tároló biztonságimásolat-kezelési metaadatait adja vissza. |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | A Recovery Services-tárolóval kapcsolatos biztonsági mentés eredményét adja vissza. |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupOperations/read | Visszaadja a biztonsági mentési művelet állapotának helyreállítási szolgáltatások tárolóban. |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupPolicies/delete | Védelmi házirend törlése |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | A szabályzatművelet eredményeinek beolvasása. |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Házirend művelet állapotának beolvasása. |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Visszaadja az összes védelmi házirend |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Védelmi házirend létrehozása |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | A védhető elemek listáját adja vissza. |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | A védett elemek listájának beolvasása. |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Az előfizetéshez tartozó összes tárolókat ad vissza |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/action | Visszaadja a PIN-kód biztonsági tároló szolgáltatásról a helyreállításhoz. |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Helyreállítási beolvasása-tárolási konfigurációját szolgáltatások tárolóban. |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupstorageconfig/write | Frissítések Tárkonfigurációt helyreállítási szolgáltatások tárolóban. |
> | Művelet | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Recovery Services beolvasása védett elemek és a védett kiszolgálók összesítések. |
> | Művelet | Microsoft.RecoveryServices/Vaults/certificates/write | A frissítés erőforrás tanúsítvány művelet frissíti az erőforrás/tárolói hitelesítő adatainak tanúsítványa. |
> | Művelet | Microsoft.RecoveryServices/Vaults/delete | A tároló törlése a művelet törli a megadott Azure-erőforrás "tárolóban" típusú |
> | Művelet | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Művelet | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Művelet | Microsoft.RecoveryServices/Vaults/extendedInformation/write | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Művelet | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | A riasztások lekéri a Recovery services-tároló. |
> | Művelet | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Feloldja a riasztást. |
> | Művelet | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | A helyreállítási szolgáltatás tároló értesítési beállításainak lekérése. |
> | Művelet | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | Konfigurálja az e-mail értesítések küldéséhez Recovery services-tároló. |
> | Művelet | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/diagnosticSettings/read | Az Azure biztonsági mentési diagnosztika |
> | Művelet | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/diagnosticSettings/write | Az Azure biztonsági mentési diagnosztika |
> | Művelet | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/logDefinitions/read | Az Azure biztonsági mentési naplók |
> | Művelet | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/metricDefinitions/read | Az Azure biztonsági mentési metrikák |
> | Művelet | Microsoft.RecoveryServices/Vaults/read | A tároló Get művelet lekérdezi az Azure-erőforrás "tárolóban" típusú képviselő objektum |
> | Művelet | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | A tároló regisztrációját művelet használható a tároló regisztrációját. |
> | Művelet | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Művelet alkalmazható Get művelet eredményeit a műveleti állapotának és az aszinkron módon elküldött művelet eredménye beolvasása |
> | Művelet | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Az beszerzése tárolókat művelet alkalmazható a tárolók regisztrált erőforrás lekérése. |
> | Művelet | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | A szolgáltatás tárolójának regisztrálása műveletet szolgáltatásban való regisztráláshoz tárolója helyreállítási használható. |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Olvassa el a riasztások beállításai |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | Minden riasztás beállításainak létrehozása vagy frissítése |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationEvents/read | Bármely események olvasása |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Konzisztencia-ellenőrzést futtat a hálón |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | A hálók törlése |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | Lemezkép központi telepítése |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/read | A hálók olvasása |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Átjáró újbóli társítása |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | Távolítsa el a háló |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Háló tanúsítványának megújítása |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Olvassa el a hálózatok |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | Hálózati leképezések törlése |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Olvassa el a hálózatok leképezését |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | Hozzon létre vagy hálózati leképezések frissítése |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | Védhető objektum felderítése |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Olvassa el a védelmi tárolókkal |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | Távolítsa el a védelmi tároló |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Bármely védhető elemek olvasása |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Helyreállítási pont alkalmazása |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | Minden védett elemek törlése |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Feladatátvétel véglegesítésének |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Tervezett feladatátvétel |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Minden védett elemek olvasása |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Olvassa el a replikációs helyreállítási pontot |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | Távolítsa el a védett elem |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Javítás replikáció |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Állítsa a védett elem |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Feladatátvételi teszt |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Teszt feladatátadás kitakarítását |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Feladatátvétel |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Frissítse a mobilitási szolgáltatás |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | Minden védett cikkek létrehozása vagy frissítése |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | A védelmi tároló leképezéseket törlése |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Olvassa el a védelmi tároló leképezések |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | Védelmitároló-leképezés eltávolítása |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | Létrehozni vagy frissíteni a védelmi tároló leképezések |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Kapcsoló védelmi tároló |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | Létrehozni vagy frissíteni a védelmi tárolókkal |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | Bármilyen helyreállítási szolgáltatók törlése |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Olvassa el az összes helyreállítási szolgáltatók |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Frissítse a szolgáltatót |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | Recovery Services-szolgáltató eltávolítása |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | Létrehozása vagy frissítése a helyreállítási szolgáltatók |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | A Tárhelybesorolások olvasása |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | A tárolási besorolás leképezéseket törlése |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Olvassa el a tárolási besorolás leképezések |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | Létrehozni vagy frissíteni a tárolási besorolás leképezések |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | Feladatok törlése |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Olvassa el a megfelelő feladat |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | A feladatok létrehozása vagy módosítása |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationFabrics/write | Létrehozni vagy frissíteni a háló |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | Megszakítása |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationJobs/read | Olvassa el a megfelelő feladat |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | Indítsa újra a feladatot |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | Feladat folytatása |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | A szabályzatoknak törlése |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Olvassa el az összes házirend |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationPolicies/write | Bármely irányelveinek létrehozása vagy frissítése |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | A helyreállítási terv törlése |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Feladatátvétel véglegesítésének helyreállítási terv |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Tervezett feladatátvétel helyreállítási terv |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | A helyreállítási tervek olvasása |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Állítsa a helyreállítási terv |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Teszt feladatátvételi helyreállítási terv |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Teszt feladatátadás kitakarítását helyreállítási terv |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Feladatátvételi helyreállítási terv |
> | Művelet | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | A helyreállítási tervek létrehozása vagy módosítása |
> | Művelet | Microsoft.RecoveryServices/Vaults/tokenInfo/read | Recovery Services-tároló információi token értéket ad vissza. |
> | Művelet | Microsoft.RecoveryServices/vaults/usages/read | Olvassa el a tároló módjait |
> | Művelet | Microsoft.RecoveryServices/Vaults/usages/read | Egy adott Recovery Services-tároló használati adatait adja vissza. |
> | Művelet | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A tároló Token művelet alkalmazható tároló szintű háttérbeli műveletek tároló jogkivonatának beszerzéséhez. |
> | Művelet | Microsoft.RecoveryServices/Vaults/write | A tárlétrehozási művelettel vault típusú Azure-erőforrás hozható létre |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.Relay/checkNameAvailability/action | A névtér adott előfizetésben való elérhetőségének ellenőrzése. |
> | Művelet | Microsoft.Relay/checkNamespaceAvailability/action | A névtér adott előfizetésben való elérhetőségének ellenőrzése. Ez az API elavult. használja helyette a CheckNameAvailabiltiy. |
> | Művelet | Microsoft.Relay/namespaces/authorizationRules/action | Frissítések Namespace engedélyezési szabályt. Ez az API depricated. A Namespace engedélyezési szabály sorkészletének frissítéséhez használja a PUT hívás... Ez a művelet nem támogatott az API verzió 2017-04-01. |
> | Művelet | Microsoft.Relay/namespaces/authorizationRules/delete | Namespace engedélyezési szabály törlése. Az alapértelmezett Namespace engedélyezési szabály nem törölhető.  |
> | Művelet | Microsoft.Relay/namespaces/authorizationRules/listkeys/action | A névtérhez tartozó kapcsolati karakterlánc beolvasása |
> | Művelet | Microsoft.Relay/namespaces/authorizationRules/read | A névtér-engedélyezési szabályok leírásai listájának beolvasása. |
> | Művelet | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása |
> | Művelet | Microsoft.Relay/namespaces/authorizationRules/write | A Namespace szintű engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és másodlagos kulcsok lehet frissíteni. |
> | Művelet | Microsoft.Relay/namespaces/Delete | Egy névtérerőforrás törlése |
> | Művelet | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | A vészhelyreállítás elsődleges névterére vonatkozó engedélyezési szabályok kulcsainak beolvasása |
> | Művelet | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | Vészhelyreállítás elsődleges névterére vonatkozó engedélyezési szabályok beolvasása |
> | Művelet | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | A vészhelyreállítás letiltása és a módosítások elsődleges névtérből másodlagos névtérbe történő replikálásának leállítása. |
> | Művelet | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | A névtéralias adott előfizetésben való elérhetőségének ellenőrzése. |
> | Művelet | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | A névtér társított vész-helyreállítási konfiguráció törlése. Ezt a műveletet csak az elsődleges névtér keresztül kell meghívni. |
> | Művelet | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | GEO DR feladatátvételének meghívása és a névtéralias beállítása úgy, hogy a másodlagos névtérre mutasson. |
> | Művelet | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | A névtérhez hozzárendelt vész-helyreállítási konfiguráció beolvasása. |
> | Művelet | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | A névtérhez hozzárendelt vész-helyreállítási konfiguráció létrehozása vagy frissítése. |
> | Művelet | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/action | A művelet HybridConnection frissítéséhez. Ez a művelet nem támogatott az API verzió 2017-04-01. Az engedélyezési szabályok. Egy PUT hívás segítségével engedélyezési szabály frissítése. |
> | Művelet | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/delete | A művelet törli HybridConnection engedélyezési szabályokat |
> | Művelet | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/listkeys/action | A kapcsolati karakterláncot HybridConnection beolvasása |
> | Művelet | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/read |  A HybridConnection engedélyezési szabályok listájának lekérdezése |
> | Művelet | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása |
> | Művelet | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/write | HybridConnection engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Az engedélyezési szabályok hozzáférési jogok lehet frissíteni. |
> | Művelet | Microsoft.Relay/namespaces/HybridConnections/Delete | A művelet HybridConnection erőforrás törlése |
> | Művelet | Microsoft.Relay/namespaces/HybridConnections/read | Listájának HybridConnection erőforrás leírása |
> | Művelet | Microsoft.Relay/namespaces/HybridConnections/write | Hozzon létre vagy frissítés HybridConnection tulajdonságai. |
> | Művelet | Microsoft.Relay/namespaces/messagingPlan/read | Lekérdezi az névtér az üzenetküldési megtervezése.<br>Ez az API elavult.<br>A MessagingPlan erőforrás keresztül elérhetővé tett tulajdonságok (szülő) Namespace erőforrás későbbi API-verziók kerülnek...<br>Ez a művelet nem támogatott az API verzió 2017-04-01. |
> | Művelet | Microsoft.Relay/namespaces/messagingPlan/write | Frissíti az üzenetkezelési tervezése névtér.<br>Ez az API elavult.<br>A MessagingPlan erőforrás keresztül elérhetővé tett tulajdonságok (szülő) Namespace erőforrás későbbi API-verziók kerülnek...<br>Ez a művelet nem támogatott az API verzió 2017-04-01. |
> | Művelet | Microsoft.Relay/namespaces/operationresults/read | Névtérművelet állapotának beolvasása |
> | Művelet | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Namespace metrikák erőforrás leírása listájának beolvasása |
> | Művelet | Microsoft.Relay/namespaces/read | A névtérerőforrások leírásai listájának beolvasása |
> | Művelet | Microsoft.Relay/namespaces/removeAcsNamepsace/action | ACS-névtér eltávolítása |
> | Művelet | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/action | A művelet WcfRelay frissítéséhez. Ez a művelet nem támogatott az API verzió 2017-04-01. Az engedélyezési szabályok. Egy PUT hívás segítségével engedélyezési szabály frissítése. |
> | Művelet | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/delete | A művelet törli WcfRelay engedélyezési szabályokat |
> | Művelet | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/listkeys/action | A kapcsolati karakterláncot WcfRelay beolvasása |
> | Művelet | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/read |  A WcfRelay engedélyezési szabályok listájának lekérdezése |
> | Művelet | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/action | Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása |
> | Művelet | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/write | WcfRelay engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Az engedélyezési szabályok hozzáférési jogok lehet frissíteni. |
> | Művelet | Microsoft.Relay/namespaces/WcfRelays/Delete | A művelet WcfRelay erőforrás törlése |
> | Művelet | Microsoft.Relay/namespaces/WcfRelays/read | Listájának WcfRelay erőforrás leírása |
> | Művelet | Microsoft.Relay/namespaces/WcfRelays/write | Hozzon létre vagy frissítés WcfRelay tulajdonságai. |
> | Művelet | Microsoft.Relay/namespaces/write | Namespace erőforrás létrehozása és frissítése a tulajdonságait. Címkék és a kapacitás, a Namespace nem frissíthető tulajdonságait. |
> | Művelet | Microsoft.Relay/operations/read | Műveletek beolvasása |
> | Művelet | Microsoft.Relay/register/action | A Relay erőforrás-szolgáltató előfizetésének regisztrálása és a Relay-erőforrások létrehozásának engedélyezése |
> | Művelet | Microsoft.Relay/unregister/action | A Relay erőforrás-szolgáltató előfizetésének regisztrálása és a Relay-erőforrások létrehozásának engedélyezése |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | Beolvassa a megadott erőforrás rendelkezésre állási állapotát |
> | Művelet | Microsoft.ResourceHealth/AvailabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Művelet | Microsoft.ResourceHealth/events/read | Az adott előfizetés Service Health-eseményeinek beolvasása |
> | Művelet | Microsoft.Resourcehealth/healthevent/action | A megadott erőforrás állapotának változását jelöli |
> | Művelet | Microsoft.Resourcehealth/healthevent/Activated/action | A megadott erőforrás állapotának változását jelöli |
> | Művelet | Microsoft.Resourcehealth/healthevent/InProgress/action | A megadott erőforrás állapotának változását jelöli |
> | Művelet | Microsoft.Resourcehealth/healthevent/Pending/action | A megadott erőforrás állapotának változását jelöli |
> | Művelet | Microsoft.Resourcehealth/healthevent/Resolved/action | A megadott erőforrás állapotának változását jelöli |
> | Művelet | Microsoft.Resourcehealth/healthevent/Updated/action | A megadott erőforrás állapotának változását jelöli |
> | Művelet | Microsoft.ResourceHealth/impactedResources/read | Az adott előfizetés érintett erőforrásainak beolvasása |
> | Művelet | Microsoft.ResourceHealth/register/action | A Microsoft Resource Health-előfizetés regisztrálása |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.Resources/checkPolicyCompliance/action | Annak ellenőrzése, hogy egy adott erőforrás megfelelőségi állapota megfelel-e az erőforrás-szabályzatoknak. |
> | Művelet | Microsoft.Resources/checkResourceName/action | Erőforrásnév érvényességének ellenőrzése. |
> | Művelet | Microsoft.Resources/deployments/cancel/action | Megszakítja az üzembe helyezést. |
> | Művelet | Microsoft.Resources/deployments/delete | Törli az üzemelő példányt. |
> | Művelet | Microsoft.Resources/deployments/operations/read | Beolvassa vagy listázza az üzembe helyezési műveleteket. |
> | Művelet | Microsoft.Resources/deployments/read | Beolvassa vagy listázza az üzemelő példányokat. |
> | Művelet | Microsoft.Resources/deployments/validate/action | Érvényesíti az üzemelő példányt. |
> | Művelet | Microsoft.Resources/deployments/write | Létrehozza vagy frissíti az üzemelő példányt. |
> | Művelet | Microsoft.Resources/links/delete | Törli az erőforrás-hivatkozást. |
> | Művelet | Microsoft.Resources/links/read | Beolvassa vagy listázza az erőforrás-hivatkozásokat. |
> | Művelet | Microsoft.Resources/links/write | Létrehozza vagy frissíti az erőforrás-hivatkozást. |
> | Művelet | Microsoft.Resources/marketplace/purchase/action | Erőforrás vásárlása a Piactéren. |
> | Művelet | Microsoft.Resources/providers/read | A szolgáltatók listájának lekérése. |
> | Művelet | Microsoft.Resources/resources/read | Az erőforrások listájának beolvasása szűrők alapján. |
> | Művelet | Microsoft.Resources/subscriptions/locations/read | Beolvassa a támogatott helyek listáját. |
> | Művelet | Microsoft.Resources/subscriptions/operationresults/read | Az előfizetési művelet eredményeinek lekérése. |
> | Művelet | Microsoft.Resources/subscriptions/providers/read | Beolvassa vagy listázza az erőforrás-szolgáltatókat. |
> | Művelet | Microsoft.Resources/subscriptions/read | Beolvassa az előfizetések listáját. |
> | Művelet | Microsoft.Resources/subscriptions/resourceGroups/delete | Törli az erőforráscsoportot és az ahhoz tartozó összes erőforrást. |
> | Művelet | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | Beolvassa vagy listázza az üzembe helyezési műveleteket. |
> | Művelet | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | Az üzembe helyezési műveletek állapotának beolvasása vagy listázása. |
> | Művelet | Microsoft.Resources/subscriptions/resourcegroups/deployments/read | Beolvassa vagy listázza az üzemelő példányokat. |
> | Művelet | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | Létrehozza vagy frissíti az üzemelő példányt. |
> | Művelet | Microsoft.Resources/subscriptions/resourceGroups/moveResources/action | Erőforrásokat helyez át az egyik erőforráscsoportból egy másikba. |
> | Művelet | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Művelet | Microsoft.Resources/subscriptions/resourcegroups/resources/read | Az erőforráscsoporthoz tartozó erőforrások lekérése. |
> | Művelet | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | Erőforrások erőforráscsoportok közötti áthelyezésének ellenőrzése. |
> | Művelet | Microsoft.Resources/subscriptions/resourceGroups/write | Létrehozza vagy frissíti az erőforráscsoportot. |
> | Művelet | Microsoft.Resources/subscriptions/resources/read | Beolvassa az előfizetéshez tartozó erőforrásokat. |
> | Művelet | Microsoft.Resources/subscriptions/tagNames/delete | Törli az előfizetéscímkét. |
> | Művelet | Microsoft.Resources/subscriptions/tagNames/read | Beolvassa vagy listázza az előfizetéscímkéket. |
> | Művelet | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | Törli az előfizetéscímke értékét. |
> | Művelet | Microsoft.Resources/subscriptions/tagNames/tagValues/read | Beolvassa vagy listázza az előfizetéscímkék értékeit. |
> | Művelet | Microsoft.Resources/subscriptions/tagNames/tagValues/write | Hozzáadja az előfizetéscímke értékét. |
> | Művelet | Microsoft.Resources/subscriptions/tagNames/write | Előfizetéscímke hozzáadása. |
> | Művelet | Microsoft.Resources/tenants/read | Beolvassa a bérlők listáját. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.Scheduler/jobcollections/delete | A feladatgyűjtemény törlése. |
> | Művelet | Microsoft.Scheduler/jobcollections/disable/action | A feladatgyűjtemény letiltása. |
> | Művelet | Microsoft.Scheduler/jobcollections/enable/action | A feladatgyűjtemény engedélyezése. |
> | Művelet | Microsoft.Scheduler/jobcollections/jobs/delete | A feladat törlése. |
> | Művelet | Microsoft.Scheduler/jobcollections/jobs/generateLogicAppDefinition/action | Logic App-definíció előállítása egy Scheduler-feladat alapján. |
> | Művelet | Microsoft.Scheduler/jobcollections/jobs/jobhistories/read | A feladatelőzmények beolvasása. |
> | Művelet | Microsoft.Scheduler/jobcollections/jobs/read | A feladat beolvasása. |
> | Művelet | Microsoft.Scheduler/jobcollections/jobs/run/action | A feladat futtatása. |
> | Művelet | Microsoft.Scheduler/jobcollections/jobs/write | Feladatok létrehozása és frissítése |
> | Művelet | Microsoft.Scheduler/jobcollections/read | Feladatgyűjtemény beolvasása |
> | Művelet | Microsoft.Scheduler/jobcollections/write | Feladatgyűjtemények létrehozása és frissítése |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.Search/checkNameAvailability/action | A szolgáltatásnév rendelkezésre állását ellenőrzi. |
> | Művelet | Microsoft.Search/operations/read | A Microsoft.Search szolgáltató elérhető műveleteinek listája. |
> | Művelet | Microsoft.Search/register/action | A keresési erőforrás-szolgáltató előfizetésének regisztrálása, és lehetővé teszi a keresési szolgáltatások létrehozását. |
> | Művelet | Microsoft.Search/searchServices/createQueryKey/action | A lekérdezési kulcsot hoz létre. |
> | Művelet | Microsoft.Search/searchServices/delete | Törli a keresési szolgáltatást. |
> | Művelet | Microsoft.Search/searchServices/deleteQueryKey/delete | Törli a lekérdezési kulcsot. |
> | Művelet | Microsoft.Search/searchServices/diagnosticSettings/read | Lekérdezi a diganostic beállítást olvassa el az erőforrás |
> | Művelet | Microsoft.Search/searchServices/diagnosticSettings/write | Létrehozza vagy frissíti az erőforrást a diganostic beállítása |
> | Művelet | Microsoft.Search/searchServices/listAdminKeys/action | Az adminisztrációs kulcsok beolvasása. |
> | Művelet | Microsoft.Search/searchServices/listQueryKeys/read | Az adott Azure Search szolgáltatás API lekérdezési kulcsok listáját adja vissza. |
> | Művelet | Microsoft.Search/searchServices/logDefinitions/read | A keresési szolgáltatás naplók beolvasása. |
> | Művelet | Microsoft.Search/searchServices/metricDefinitions/read | A keresési szolgáltatáshoz elérhető metrikai meghatározások beolvasása |
> | Művelet | Microsoft.Search/searchServices/read | Olvassa be a keresési szolgáltatást. |
> | Művelet | Microsoft.Search/searchServices/regenerateAdminKey/action | Az adminisztrátori kulcs újragenerálása. |
> | Művelet | Microsoft.Search/searchServices/start/action | Elindítja a keresési szolgáltatást. |
> | Művelet | Microsoft.Search/searchServices/stop/action | A keresési szolgáltatás leállítása. |
> | Művelet | Microsoft.Search/searchServices/write | Létrehozza vagy frissíti a keresési szolgáltatást. |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.Security/alerts/read | Lekérdezi az összes rendelkezésre álló biztonsági riasztások |
> | Művelet | Microsoft.Security/applicationWhitelistings/read | Az alkalmazás whitelistings beolvasása |
> | Művelet | Microsoft.Security/applicationWhitelistings/write | Létrehoz egy új alkalmazások engedélyezése vagy frissít egy meglévő |
> | Művelet | Microsoft.Security/complianceResults/read | A megfelelőségi eredményeket az erőforrás beolvasása |
> | Művelet | Microsoft.Security/locations/alerts/activate/action | Aktiválja a biztonsági riasztások |
> | Művelet | Microsoft.Security/locations/alerts/dismiss/action | A biztonsági riasztások elvetése |
> | Művelet | Microsoft.Security/locations/alerts/read | Lekérdezi az összes rendelkezésre álló biztonsági riasztások |
> | Művelet | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | Kezdeményezi a közvetlenül az időponthoz kötött hálózati hozzáférési házirend |
> | Művelet | Microsoft.Security/locations/jitNetworkAccessPolicies/read | Lekérdezi a közvetlenül az időponthoz kötött hálózati hozzáférési házirendek |
> | Művelet | Microsoft.Security/locations/jitNetworkAccessPolicies/write | Létrehoz egy új just-in-time hálózati hozzáférési házirendet, vagy frissít egy meglévő |
> | Művelet | Microsoft.Security/locations/read | Lekérdezi a biztonsági adatok helye |
> | Művelet | Microsoft.Security/locations/tasks/activate/action | Biztonsági ajánlás olyan környezetekben aktiválása |
> | Művelet | Microsoft.Security/locations/tasks/dismiss/action | Biztonsági ajánlás olyan környezetekben elvetése |
> | Művelet | Microsoft.Security/locations/tasks/read | Lekérdezi az összes rendelkezésre álló biztonsági javaslatok |
> | Művelet | Microsoft.Security/locations/tasks/resolve/action | Biztonsági ajánlás olyan környezetekben feloldása |
> | Művelet | Microsoft.Security/locations/tasks/start/action | Indítsa el a biztonsági ajánlás olyan környezetekben |
> | Művelet | Microsoft.Security/policies/read | Lekérdezi a biztonsági házirend |
> | Művelet | Microsoft.Security/policies/write | A biztonsági házirend frissítése |
> | Művelet | Microsoft.Security/pricings/delete | Törli a hatókör árképzési beállításokat |
> | Művelet | Microsoft.Security/pricings/read | A hatókör árképzési beállításainak beolvasása |
> | Művelet | Microsoft.Security/pricings/write | Frissíti a hatókör árképzési beállításait |
> | Művelet | Microsoft.Security/register/action | Az Azure Security Center előfizetésének regisztrálása |
> | Művelet | Microsoft.Security/securityContacts/delete | A biztonsági ügyfél törlése |
> | Művelet | Microsoft.Security/securityContacts/read | A biztonsági ügyfél beolvasása |
> | Művelet | Microsoft.Security/securityContacts/write | A biztonsági ügyfél frissítése |
> | Művelet | Microsoft.Security/securitySolutions/delete | Olyan biztonsági megoldás törlése |
> | Művelet | Microsoft.Security/securitySolutions/read | Lekérdezi a biztonsági megoldások |
> | Művelet | Microsoft.Security/securitySolutions/write | Létrehoz egy új biztonsági megoldást, vagy frissít egy meglévő |
> | Művelet | Microsoft.Security/securitySolutionsReferenceData/read | Lekérdezi a biztonsági megoldások referenciaadatok |
> | Művelet | Microsoft.Security/securityStatuses/read | Az Azure-erőforrások állapotát állapotok a biztonsági beolvasása |
> | Művelet | Microsoft.Security/securityStatusesSummaries/read | A biztonsági állapot-összefoglalók lekérdezi a hatókör |
> | Művelet | Microsoft.Security/tasks/read | Lekérdezi az összes rendelkezésre álló biztonsági javaslatok |
> | Művelet | Microsoft.Security/webApplicationFirewalls/delete | Webalkalmazási tűzfal törlése |
> | Művelet | Microsoft.Security/webApplicationFirewalls/read | Lekérdezi a webes alkalmazás tűzfalak |
> | Művelet | Microsoft.Security/webApplicationFirewalls/write | Létrehoz egy új webalkalmazási tűzfal vagy frissít egy meglévő |
> | Művelet | Microsoft.Security/workspaceSettings/connect/action | Munkaterület beállítások újracsatlakozás beállításainak módosítása |
> | Művelet | Microsoft.Security/workspaceSettings/delete | A munkaterület beállításainak törlése |
> | Művelet | Microsoft.Security/workspaceSettings/read | A munkaterület beállításainak beolvasása |
> | Művelet | Microsoft.Security/workspaceSettings/write | A munkaterület beállításainak frissítése |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.SecurityGraph/diagnosticsettings/delete | A diagnosztikai beállítás törlése |
> | Művelet | Microsoft.SecurityGraph/diagnosticsettings/read | A diagnosztikai beállítás beolvasásakor. |
> | Művelet | Microsoft.SecurityGraph/diagnosticsettings/write | A diagnosztikai beállításának írása |
> | Művelet | Microsoft.SecurityGraph/diagnosticsettingscategories/read | A diagnosztikai beállításának kategóriák olvasása |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.ServiceBus/checkNameAvailability/action | A névtér adott előfizetésben való elérhetőségének ellenőrzése. |
> | Művelet | Microsoft.ServiceBus/checkNamespaceAvailability/action | A névtér adott előfizetésben való elérhetőségének ellenőrzése. Ez az API elavult. használja helyette a CheckNameAvailabiltiy. |
> | Művelet | Microsoft.ServiceBus/namespaces/authorizationRules/action | Frissítések Namespace engedélyezési szabályt. Ez az API depricated. A Namespace engedélyezési szabály sorkészletének frissítéséhez használja a PUT hívás... Ez a művelet nem támogatott az API verzió 2017-04-01. |
> | Művelet | Microsoft.ServiceBus/namespaces/authorizationRules/delete | Namespace engedélyezési szabály törlése. Az alapértelmezett Namespace engedélyezési szabály nem törölhető.  |
> | Művelet | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | A névtérhez tartozó kapcsolati karakterlánc beolvasása |
> | Művelet | Microsoft.ServiceBus/namespaces/authorizationRules/read | A névtér-engedélyezési szabályok leírásai listájának beolvasása. |
> | Művelet | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása |
> | Művelet | Microsoft.ServiceBus/namespaces/authorizationRules/write | A Namespace szintű engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és másodlagos kulcsok lehet frissíteni. |
> | Művelet | Microsoft.ServiceBus/namespaces/Delete | Egy névtérerőforrás törlése |
> | Művelet | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | A vészhelyreállítás elsődleges névterére vonatkozó engedélyezési szabályok kulcsainak beolvasása |
> | Művelet | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | Vészhelyreállítás elsődleges névterére vonatkozó engedélyezési szabályok beolvasása |
> | Művelet | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | A vészhelyreállítás letiltása és a módosítások elsődleges névtérből másodlagos névtérbe történő replikálásának leállítása. |
> | Művelet | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | A névtéralias adott előfizetésben való elérhetőségének ellenőrzése. |
> | Művelet | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | A névtér társított vész-helyreállítási konfiguráció törlése. Ezt a műveletet csak az elsődleges névtér keresztül kell meghívni. |
> | Művelet | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | GEO DR feladatátvételének meghívása és a névtéralias beállítása úgy, hogy a másodlagos névtérre mutasson. |
> | Művelet | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | A névtérhez hozzárendelt vész-helyreállítási konfiguráció beolvasása. |
> | Művelet | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | A névtérhez hozzárendelt vész-helyreállítási konfiguráció létrehozása vagy frissítése. |
> | Művelet | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | A névtérhez társított Event Grid-szűrő törlése. |
> | Művelet | Microsoft.ServiceBus/namespaces/eventGridFilters/read | A névtérhez társított Event Grid-szűrő beolvasása. |
> | Művelet | Microsoft.ServiceBus/namespaces/eventGridFilters/write | A névtérhez társított Event Grid-szűrő létrehozása vagy frissítése. |
> | Művelet | Microsoft.ServiceBus/namespaces/eventhubs/read | Az EventHub erőforrás leírások listáját |
> | Művelet | Microsoft.ServiceBus/namespaces/messagingPlan/read | Lekérdezi az névtér az üzenetküldési megtervezése.<br>Ez az API elavult.<br>A MessagingPlan erőforrás keresztül elérhetővé tett tulajdonságok (szülő) Namespace erőforrás későbbi API-verziók kerülnek...<br>Ez a művelet nem támogatott az API verzió 2017-04-01. |
> | Művelet | Microsoft.ServiceBus/namespaces/messagingPlan/write | Frissíti az üzenetkezelési tervezése névtér.<br>Ez az API elavult.<br>A MessagingPlan erőforrás keresztül elérhetővé tett tulajdonságok (szülő) Namespace erőforrás későbbi API-verziók kerülnek...<br>Ez a művelet nem támogatott az API verzió 2017-04-01. |
> | Művelet | Microsoft.ServiceBus/namespaces/migrate/action | Névtérmigrálási művelet |
> | Művelet | Microsoft.ServiceBus/namespaces/operationresults/read | Névtérművelet állapotának beolvasása |
> | Művelet | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Namespace diagnosztikai beállítások erőforrás leírása listájának beolvasása |
> | Művelet | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Namespace diagnosztikai beállítások erőforrás leírása listájának beolvasása |
> | Művelet | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | Listájának Namespace naplók erőforrás leírása |
> | Művelet | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Namespace metrikák erőforrás leírása listájának beolvasása |
> | Művelet | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | Várólista frissítési művelete. Ez a művelet nem támogatott az API verzió 2017-04-01. Az engedélyezési szabályok. Egy PUT hívás segítségével engedélyezési szabály frissítése. |
> | Művelet | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | Művelet az engedélyezési szabályok várólista törlése |
> | Művelet | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | A kapcsolati karakterlánc várólistára beolvasása |
> | Művelet | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  A várólista-engedélyezési szabályok listájának lekérdezése |
> | Művelet | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása |
> | Művelet | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | Várólista engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Az engedélyezési szabályok hozzáférési jogok lehet frissíteni. |
> | Művelet | Microsoft.ServiceBus/namespaces/queues/Delete | A művelet várólista erőforrás törlése |
> | Művelet | Microsoft.ServiceBus/namespaces/queues/read | Várólista erőforrás leírása listájának beolvasása |
> | Művelet | Microsoft.ServiceBus/namespaces/queues/write | Hozzon létre vagy frissítés várólista-tulajdonságok. |
> | Művelet | Microsoft.ServiceBus/namespaces/read | A névtérerőforrások leírásai listájának beolvasása |
> | Művelet | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | ACS-névtér eltávolítása |
> | Művelet | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | A témakör frissítése műveletet. Ez a művelet nem támogatott az API verzió 2017-04-01. Az engedélyezési szabályok. Egy PUT hívás segítségével engedélyezési szabály frissítése. |
> | Művelet | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | A művelet törli a témakör az engedélyezési szabályok |
> | Művelet | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | A témakör a kapcsolati karakterlánc beolvasása |
> | Művelet | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  A témakör az engedélyezési szabályok listájának lekérdezése |
> | Művelet | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása |
> | Művelet | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | A témakör az engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Az engedélyezési szabályok hozzáférési jogok lehet frissíteni. |
> | Művelet | Microsoft.ServiceBus/namespaces/topics/Delete | A művelet témakör erőforrás törlése |
> | Művelet | Microsoft.ServiceBus/namespaces/topics/read | A témakör erőforrás leírások listáját |
> | Művelet | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | A művelet TopicSubscription erőforrás törlése |
> | Művelet | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | Listájának TopicSubscription erőforrás leírása |
> | Művelet | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | Műveletet, szabály erőforrás törlése |
> | Művelet | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | Szabály erőforrás leírása listájának beolvasása |
> | Művelet | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/write | Hozzon létre vagy frissítés szabály tulajdonságait. |
> | Művelet | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | Hozzon létre vagy frissítés TopicSubscription tulajdonságai. |
> | Művelet | Microsoft.ServiceBus/namespaces/topics/write | Hozzon létre vagy frissítés témakör tulajdonságai. |
> | Művelet | Microsoft.ServiceBus/namespaces/write | Namespace erőforrás létrehozása és frissítése a tulajdonságait. Címkék és a kapacitás, a Namespace nem frissíthető tulajdonságait. |
> | Művelet | Microsoft.ServiceBus/operations/read | Műveletek beolvasása |
> | Művelet | Microsoft.ServiceBus/register/action | Regisztrálja az előfizetést a ServiceBus erőforrás-szolgáltatóra, és lehetővé teszi a ServiceBus-erőforrások létrehozását |
> | Művelet | Microsoft.ServiceBus/sku/read | Termékváltozat erőforrás leírása listájának beolvasása |
> | Művelet | Microsoft.ServiceBus/sku/regions/read | Listájának SkuRegions erőforrás leírása |
> | Művelet | Microsoft.ServiceBus/unregister/action | Regisztrálja az előfizetést a ServiceBus erőforrás-szolgáltatóra, és lehetővé teszi a ServiceBus-erőforrások létrehozását |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.ServiceFabric/clusters/applications/delete | Tetszőleges alkalmazás törlése |
> | Művelet | Microsoft.ServiceFabric/clusters/applications/read | Tetszőleges alkalmazás beolvasása |
> | Művelet | Microsoft.ServiceFabric/clusters/applications/services/delete | Tetszőleges szolgáltatás törlése |
> | Művelet | Microsoft.ServiceFabric/clusters/applications/services/partitions/read | Tetszőleges partíció beolvasása |
> | Művelet | Microsoft.ServiceFabric/clusters/applications/services/partitions/replicas/read | Tetszőleges replika beolvasása |
> | Művelet | Microsoft.ServiceFabric/clusters/applications/services/read | Tetszőleges szolgáltatás beolvasása |
> | Művelet | Microsoft.ServiceFabric/clusters/applications/services/statuses/read | Bármely szolgáltatásállapot beolvasása |
> | Művelet | Microsoft.ServiceFabric/clusters/applications/services/write | Tetszőleges szolgáltatás létrehozása vagy frissítése |
> | Művelet | Microsoft.ServiceFabric/clusters/applications/write | Tetszőleges alkalmazás létrehozása vagy frissítése |
> | Művelet | Microsoft.ServiceFabric/clusters/applicationTypes/delete | Tetszőleges alkalmazástípus törlése |
> | Művelet | Microsoft.ServiceFabric/clusters/applicationTypes/read | Tetszőleges alkalmazástípus beolvasása |
> | Művelet | Microsoft.ServiceFabric/clusters/applicationTypes/versions/delete | Tetszőleges alkalmazástípus-verzió törlése |
> | Művelet | Microsoft.ServiceFabric/clusters/applicationTypes/versions/read | Tetszőleges alkalmazástípus-verzió beolvasása |
> | Művelet | Microsoft.ServiceFabric/clusters/applicationTypes/versions/write | Tetszőleges alkalmazástípus-verzió létrehozása vagy frissítése |
> | Művelet | Microsoft.ServiceFabric/clusters/applicationTypes/write | Tetszőleges alkalmazástípus létrehozása vagy frissítése |
> | Művelet | Microsoft.ServiceFabric/clusters/delete | Tetszőleges fürt törlése |
> | Művelet | Microsoft.ServiceFabric/clusters/nodes/read | Tetszőleges csomópont beolvasása |
> | Művelet | Microsoft.ServiceFabric/clusters/read | Tetszőleges fürt beolvasása |
> | Művelet | Microsoft.ServiceFabric/clusters/statuses/read | Bármely fürtállapot beolvasása |
> | Művelet | Microsoft.ServiceFabric/clusters/write | Tetszőleges fürt létrehozása vagy frissítése |
> | Művelet | Microsoft.ServiceFabric/locations/clusterVersions/read | Minden fürtverzió beolvasása |
> | Művelet | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | Tetszőleges fürtverzió beolvasása egy adott környezetben |
> | Művelet | Microsoft.ServiceFabric/locations/operationresults/read | Tetszőleges műveleti eredmények beolvasása |
> | Művelet | Microsoft.ServiceFabric/locations/operations/read | Tetszőleges műveletek beolvasása hely szerint |
> | Művelet | Microsoft.ServiceFabric/operations/read | Tetszőleges használható műveletek beolvasása |
> | Művelet | Microsoft.ServiceFabric/register/action | Tetszőleges művelet regisztrálása |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.SignalRService/checknameavailability/action | Ellenőrzi, hogy a név egy új szolgáltatással SignalR használható |
> | Művelet | Microsoft.SignalRService/register/action | Előfizetés az "Microsoft.SignalRService" erőforrás-szolgáltató regisztrálása |
> | Művelet | Microsoft.SignalRService/SignalR/delete | A teljes SignalR törlése |
> | Művelet | Microsoft.SignalRService/SignalR/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Művelet | Microsoft.SignalRService/SignalR/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Művelet | Microsoft.SignalRService/SignalR/providers/Microsoft.Insights/metricDefinitions/read | A SignalR elérhető metrikai meghatározások beolvasása |
> | Művelet | Microsoft.SignalRService/SignalR/read | A SignalR beállítás és konfiguráció megtekintése, a kezelési portál vagy API-n keresztül |
> | Művelet | Microsoft.SignalRService/SignalR/write | Módosítsa a SignalR beállítás és konfiguráció a kezelési portál vagy API-n keresztül |
> | Művelet | Microsoft.SignalRService/unregister/action | Előfizetés az "Microsoft.SignalRService" erőforrás-szolgáltató regisztrációjának törlése |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.Solutions/applicationDefinitions/delete | Alkalmazásdefiníció eltávolítása. |
> | Művelet | Microsoft.Solutions/applicationDefinitions/read | Az alkalmazásdefiníciók listájának beolvasása. |
> | Művelet | Microsoft.Solutions/applicationDefinitions/write | Alkalmazásdefiníció hozzáadása vagy módosítása. |
> | Művelet | Microsoft.Solutions/applications/delete | Alkalmazás eltávolítása. |
> | Művelet | Microsoft.Solutions/applications/read | Az alkalmazások listájának beolvasása. |
> | Művelet | Microsoft.Solutions/applications/write | Alkalmazás létrehozása. |
> | Művelet | Microsoft.Solutions/locations/operationStatuses/read | Az erőforrás műveleti állapotának beolvasása. |
> | Művelet | Microsoft.Solutions/register/action | Regisztrálás megoldásokra. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.Sql/checkNameAvailability/action | Ellenőrizze, hogy a megadott kiszolgáló neve rendelkezésre álló világszerte az adott előfizetéshez. |
> | Művelet | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | A kiterjesztett kiszolgáló blob naplózási házirend-beállítási művelet eredményének beolvasása |
> | Művelet | Microsoft.Sql/locations/auditingSettingsOperationResults/read | A kiszolgáló blob naplózási házirend-beállítási művelet eredményének beolvasása |
> | Művelet | Microsoft.Sql/locations/capabilities/read | A képességek lekérdezi az előfizetés egy adott helyen |
> | Művelet | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | Egy adatbázis-művelet állapotát olvassa be. |
> | Művelet | Microsoft.Sql/locations/databaseOperationResults/read | Egy adatbázis-művelet állapotát olvassa be. |
> | Művelet | Microsoft.Sql/locations/deletedServerAsyncOperation/read | Lekérdezi a folyamatban lévő műveletek törölt kiszolgálón |
> | Művelet | Microsoft.Sql/locations/deletedServerOperationResults/read | Lekérdezi a folyamatban lévő műveletek törölt kiszolgálón |
> | Művelet | Microsoft.Sql/locations/deletedServers/read | A törölt kiszolgálók vagy lekérdezi a megadott törölt kiszolgáló tulajdonságok listáját adja vissza. |
> | Művelet | Microsoft.Sql/locations/deletedServers/recover/action | Törölt kiszolgáló helyreállítása |
> | Művelet | Microsoft.Sql/locations/deleteVirtualNetworkOrSubnets/action | Egy virtuális hálózathoz vagy alhálózathoz társított virtuális hálózati szabály törlése |
> | Művelet | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | Az azure aszinkron művelet, a rugalmas készlet aszinkron művelet lekérdezi |
> | Művelet | Microsoft.Sql/locations/elasticPoolOperationResults/read | Egy rugalmas készlet művelet eredményének beolvasása. |
> | Művelet | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | A kiterjesztett kiszolgáló blob naplózási házirend-beállítási művelet eredményének beolvasása |
> | Művelet | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | A kiterjesztett kiszolgáló blob naplózási házirend-beállítási művelet eredményének beolvasása |
> | Művelet | Microsoft.Sql/locations/instanceFailoverGroups/delete | Egy meglévő példányát feladatátvételi csoport törlése. |
> | Művelet | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | Tervezett feladatátvétel végrehajtása a meglévő példány feladatátvételi csoport. |
> | Művelet | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | Egy meglévő feladatátvevő példánycsoport kényszerített feladatátvételi hajtja végre. |
> | Művelet | Microsoft.Sql/locations/instanceFailoverGroups/read | Az a példány feladatátvételi csoportokat vagy a megadott példány feladatátvételi csoport tulajdonságainak beolvasása adja vissza. |
> | Művelet | Microsoft.Sql/locations/instanceFailoverGroups/write | Példány feladatátvevő csoportot hoz létre a megadott paraméterekkel, vagy frissíti a Tulajdonságok vagy a megadott példány feladatátvételi csoport címkék. |
> | Művelet | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | Felügyelt adatbázis-visszaállítási művelet befejezéséről. |
> | Művelet | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | Lekérdezi a folyamatban lévő műveletek felügyelt adatbázis átlátható adattitkosítás |
> | Művelet | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | Lekérdezi a folyamatban lévő műveletek felügyelt adatbázis átlátható adattitkosítás |
> | Művelet | Microsoft.Sql/locations/networkInterfaceAzureAsyncOperation/read | Egy adott hálózati adapter Azure aszinkron művelet adatait adja vissza. |
> | Művelet | Microsoft.Sql/locations/networkInterfaceOperationResults/read | A megadott hálózati illesztő működési adatait adja vissza. |
> | Művelet | Microsoft.Sql/locations/read | Lekérdezi a megadott előfizetéshez tartozó helyeket |
> | Művelet | Microsoft.Sql/locations/syncAgentOperationResults/read | A szinkronizálási ügynök erőforrás művelet eredményének beolvasása |
> | Művelet | Microsoft.Sql/locations/syncDatabaseIds/read | A szinkronizálási adatbázis-azonosító egy adott régióban, és az előfizetés beolvasása |
> | Művelet | Microsoft.Sql/locations/syncGroupOperationResults/read | A szinkronizálás erőforrás-művelet eredményének beolvasása |
> | Művelet | Microsoft.Sql/locations/syncMemberOperationResults/read | A szinkronizálás tag erőforrás művelet eredményének beolvasása |
> | Művelet | Microsoft.Sql/locations/usages/read | Ehhez az előfizetéshez, egy helyen lekérdezi a szoftverhasználati mérési adatok gyűjteménye |
> | Művelet | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | Adatait adja vissza. a megadott virtuális hálózati szabályok azure aszinkron művelet  |
> | Művelet | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | A megadott virtuális hálózati szabályok művelet adatait adja vissza.  |
> | Művelet | Microsoft.Sql/managedInstances/administrators/delete | Törli a meglévő rendszergazda felügyelt példány. |
> | Művelet | Microsoft.Sql/managedInstances/administrators/read | Felügyelt példány rendszergazdák listájának lekérése. |
> | Művelet | Microsoft.Sql/managedInstances/administrators/write | Létrehozza vagy frissíti a felügyelt példány rendszergazda a megadott paraméterekkel. |
> | Művelet | Microsoft.Sql/managedInstances/databases/delete | Törli a meglévő felügyelt adatbázis |
> | Művelet | Microsoft.Sql/managedInstances/databases/read | Lekérdezi a meglévő felügyelt adatbázisba |
> | Művelet | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | Az adatbázis fenyegetés szabályzat egy adott felügyelt adatbázisnak konfigurált részleteinek beolvasása |
> | Művelet | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | Egy adott felügyelt adatbázisnak adatbázis threat detection szabályzatának módosítása |
> | Művelet | Microsoft.Sql/managedInstances/databases/securityEvents/read | Beolvassa a felügyelt adatbázis biztonsági események |
> | Művelet | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | Egy adott felügyelt adatbázison átlátható adattitkosítási adatbázis adatait beolvasása |
> | Művelet | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | Az adatbázis az átlátható adattitkosítási egy adott felügyelt adatbázis |
> | Művelet | Microsoft.Sql/managedInstances/databases/write | Létrehoz egy új adatbázist, vagy egy meglévő adatbázist frissíti. |
> | Művelet | Microsoft.Sql/managedInstances/delete | Törli a meglévő felügyelt példány. |
> | Művelet | Microsoft.Sql/managedInstances/metricDefinitions/read | Felügyelt példány metrikai meghatározásainak beolvasása |
> | Művelet | Microsoft.Sql/managedInstances/metrics/read | Felügyelt példány metrikák beolvasása |
> | Művelet | Microsoft.Sql/managedInstances/read | A figyelt példányokat vagy lekérdezi a megadott felügyelt példány tulajdonságainak listáját adja vissza. |
> | Művelet | Microsoft.Sql/managedInstances/securityAlertPolicies/read | A felügyelt kiszolgáló fenyegetés szabályzat egy adott felügyelt kiszolgálón konfigurált részleteinek beolvasása |
> | Művelet | Microsoft.Sql/managedInstances/securityAlertPolicies/write | A felügyelt kiszolgáló fenyegetés szabályzat egy adott felügyelt kiszolgáló módosítása |
> | Művelet | Microsoft.Sql/managedInstances/write | Létrehoz egy felügyelt példányt a megadott paraméterekkel, vagy tulajdonságainak vagy a megadott felügyelt példány címkék frissítése. |
> | Művelet | Microsoft.Sql/operations/read | Lekérdezi a használható további műveletek |
> | Művelet | Microsoft.Sql/register/action | A Microsoft SQL-adatbázis erőforrás-szolgáltató előfizetésének regisztrálása, és lehetővé teszi a Microsoft SQL-adatbázisok létrehozását. |
> | Művelet | Microsoft.Sql/servers/administratorOperationResults/read | Lekérdezi a folyamatban lévő műveleteket a kiszolgáló-rendszergazdák |
> | Művelet | Microsoft.Sql/servers/administrators/delete | Törölje a kiszolgáló rendszergazdája |
> | Művelet | Microsoft.Sql/servers/administrators/read | Rendszergazda kiszolgálóadatok beolvasása |
> | Művelet | Microsoft.Sql/servers/administrators/write | Létrehozni vagy frissíteni a kiszolgáló rendszergazdája |
> | Művelet | Microsoft.Sql/servers/advisors/read | A kiszolgáló elérhető tanácsadók listáját adja vissza |
> | Művelet | Microsoft.Sql/servers/advisors/recommendedActions/read | Az a kiszolgáló megadott advisor ajánlott műveletek listáját adja vissza |
> | Művelet | Microsoft.Sql/servers/advisors/recommendedActions/write | A javasolt műveletet alkalmazza a kiszolgálón |
> | Művelet | Microsoft.Sql/servers/advisors/write | Frissítések automatikus-hajtható végre az advisor a kiszolgáló szintjén állapotát. |
> | Művelet | Microsoft.Sql/servers/auditingPolicies/read | Az alapértelmezett kiszolgáló táblázat naplózás a megadott kiszolgálón konfigurált házirend részleteinek beolvasása |
> | Művelet | Microsoft.Sql/servers/auditingPolicies/write | Az alapértelmezett kiszolgáló tábla naplózását egy adott kiszolgáló módosítása |
> | Művelet | Microsoft.Sql/servers/auditingSettings/operationResults/read | A kiszolgáló blob naplózási házirend-beállítási művelet eredményének beolvasása |
> | Művelet | Microsoft.Sql/servers/auditingSettings/read | A kiszolgáló blob naplózási házirend adatait a megadott kiszolgálón konfigurált beolvasása |
> | Művelet | Microsoft.Sql/servers/auditingSettings/write | A kiszolgáló blob naplózását egy adott kiszolgáló módosítása |
> | Művelet | Microsoft.Sql/servers/automaticTuning/read | A kiszolgáló automatikus hangolási beállításainak adja vissza |
> | Művelet | Microsoft.Sql/servers/automaticTuning/write | A kiszolgáló automatikus hangolási beállításainak frissíti, és adja vissza a frissített beállításokkal |
> | Művelet | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | Egy meglévő biztonsági mentési, archiválási tárolóban törli. |
> | Művelet | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | Ez a művelet egy biztonsági mentési hosszú távú megőrzési tárolóban eléréséhez használatos. A tárolóban regisztrált erre a kiszolgálóra vonatkozó információkat ad vissza |
> | Művelet | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | Ez a művelet egy biztonsági mentési hosszú távú megőrzési regisztrálásához használt kiszolgálóra tároló |
> | Művelet | Microsoft.Sql/servers/communicationLinks/delete | Törli a meglévő kiszolgálói kommunikáció hivatkozás. |
> | Művelet | Microsoft.Sql/servers/communicationLinks/read | A megadott kiszolgáló a kommunikációs kapcsolatok listáját adja vissza. |
> | Művelet | Microsoft.Sql/servers/communicationLinks/write | Létrehozása vagy frissítése server kommunikációs kapcsolatot. |
> | Művelet | Microsoft.Sql/servers/connectionPolicies/read | Térjen vissza a megadott kiszolgáló kiszolgálói kapcsolat házirendek listájában. |
> | Művelet | Microsoft.Sql/servers/connectionPolicies/write | Létrehozása vagy módosítása egy kiszolgáló kapcsolatkezelési házirendet. |
> | Művelet | Microsoft.Sql/servers/databases/advisors/read | Az adatbázis elérhető tanácsadók listáját adja vissza |
> | Művelet | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | A megadott advisort az adatbázishoz ajánlott műveletek listáját adja vissza |
> | Művelet | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | A javasolt műveletet alkalmazza az adatbázishoz |
> | Művelet | Microsoft.Sql/servers/databases/advisors/write | Frissítés Automatikus végrehajtás az advisor adatbázis szinten állapotának. |
> | Művelet | Microsoft.Sql/servers/databases/auditingPolicies/read | A naplózási házirend tábla, egy adott adatbázisnak konfigurált adatait beolvasása |
> | Művelet | Microsoft.Sql/servers/databases/auditingPolicies/write | A tábla egy adott adatbázisnak a naplózási házirend módosítása |
> | Művelet | Microsoft.Sql/servers/databases/auditingSettings/read | Egy adott adatbázisnak konfigurált blob naplózási házirend részleteinek beolvasása |
> | Művelet | Microsoft.Sql/servers/databases/auditingSettings/write | A blob egy adott adatbázisnak a naplózási házirend módosítása |
> | Művelet | Microsoft.Sql/servers/databases/auditRecords/read | A blob naplózási rekordok beolvasása |
> | Művelet | Microsoft.Sql/servers/databases/automaticTuning/read | Automatikus hangolása egy adatbázis-beállításainak beolvasása |
> | Művelet | Microsoft.Sql/servers/databases/automaticTuning/write | Egy adatbázis automatikus hangolási beállításainak frissíti, és adja vissza a frissített beállításokkal |
> | Művelet | Microsoft.Sql/servers/databases/azureAsyncOperation/read | Egy adatbázis-művelet állapotát olvassa be. |
> | Művelet | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | Térjen vissza a megadott adatbázis biztonsági mentési archiválási házirendek listájában. |
> | Művelet | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | Hozzon létre, vagy frissítse az adatbázis biztonsági mentési, archiválási házirend. |
> | Művelet | Microsoft.Sql/servers/databases/connectionPolicies/read | A kapcsolat házirend egy adott adatbázisnak konfigurált részleteinek beolvasása |
> | Művelet | Microsoft.Sql/servers/databases/connectionPolicies/write | Módosítsa a kapcsolatkezelési házirendet az egy adott adatbázisnak |
> | Művelet | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | Az adatbázis adatmaszkolási házirendek listáját adja vissza. |
> | Művelet | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | Egy adott adatbázis házirendszabály adatmaszkolási törlése |
> | Művelet | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | A adatmaszkolási egy adott adatbázisnak konfigurált házirendszabály részleteit beolvasása |
> | Művelet | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | Adatok házirend szabály egy adott adatbázisnak a maszkolás módosítása |
> | Művelet | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | Adatmaszkolási egy adott adatbázis-házirend módosítása |
> | Művelet | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | Információkat ad vissza az elosztott lekérdezés lépés adatraktár lekérdezés kijelölt lépés azonosítója |
> | Művelet | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | Az adatraktár terjesztési lekérdezés információi a kijelölt lekérdezés Azonosítóját adja vissza |
> | Művelet | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | Lekéri a felhasználói tevékenység fut, és a felfüggesztett lekérdezések köztük az SQL Data Warehouse-példány |
> | Művelet | Microsoft.Sql/servers/databases/delete | Törli a meglévő adatbázist. |
> | Művelet | Microsoft.Sql/servers/databases/export/action | Az Azure SQL adatbázis exportálása |
> | Művelet | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Egy adott adatbázisnak konfigurált bővített blob naplózási házirend részleteinek beolvasása |
> | Művelet | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | A bővített blob egy adott adatbázisnak a naplózási házirend módosítása |
> | Művelet | Microsoft.Sql/servers/databases/extensions/read | Az adatbázis lekérdezi a bővítmények gyűjteménye. |
> | Művelet | Microsoft.Sql/servers/databases/extensions/write | A megadott adatbázis bővítmény módosítása |
> | Művelet | Microsoft.Sql/servers/databases/geoBackupPolicies/read | Georedundáns biztonsági mentési házirendek egy adott adatbázis beolvasása |
> | Művelet | Microsoft.Sql/servers/databases/geoBackupPolicies/write | Hozzon létre vagy egy adatbázis geobackup házirend frissítése |
> | Művelet | Microsoft.Sql/servers/databases/importExportOperationResults/read | Lekérdezi a folyamatban lévő importálási/exportálási műveleteket |
> | Művelet | Microsoft.Sql/servers/databases/metricDefinitions/read | Térjen vissza a típusú ítélt adatbázisokhoz |
> | Művelet | Microsoft.Sql/servers/databases/metrics/read | Térjen vissza az adatbázisok metrikák |
> | Művelet | Microsoft.Sql/servers/databases/move/action | Az Azure SQL-adatbázis átnevezése |
> | Művelet | Microsoft.Sql/servers/databases/operationResults/read | Egy adatbázis-művelet állapotát olvassa be. |
> | Művelet | Microsoft.Sql/servers/databases/operations/cancel/action | Az Azure SQL Database megszakítja a függőben lévő aszinkron művelet, amely még nem fejeződött be. |
> | Művelet | Microsoft.Sql/servers/databases/operations/read | Térjen vissza az adatbázison végrehajtott műveletek listája |
> | Művelet | Microsoft.Sql/servers/databases/pause/action | Az Azure SQL-adatraktár adatbázisa felfüggesztése |
> | Művelet | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Művelet | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Művelet | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | Az adatbázisok naplók beolvasása. |
> | Művelet | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/metricDefinitions/read | Térjen vissza a típusú ítélt adatbázisokhoz |
> | Művelet | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | A gyűjtemény lekérdezési szövegek, amelyek megfelelnek a megadott paraméterek adja vissza. |
> | Művelet | Microsoft.Sql/servers/databases/queryStore/read | A Lekérdezéstár beállítása az adatbázis aktuális értékek beolvasása. |
> | Művelet | Microsoft.Sql/servers/databases/queryStore/write | A Lekérdezéstár-beállítás az adatbázis frissítése |
> | Művelet | Microsoft.Sql/servers/databases/read | Adatbázisok vagy lekérdezi a megadott adatbázis tulajdonságainak megadása listáját adja vissza. |
> | Művelet | Microsoft.Sql/servers/databases/replicationLinks/delete | Állítsa le a replikációs kapcsolat kényszerített módon, és az esetleges adatvesztés |
> | Művelet | Microsoft.Sql/servers/databases/replicationLinks/failover/action | Feladatátvételi minden szinkronizálás után az elsődleges megváltozik, ezt az adatbázist és a replikációs relationship\u0027s az elsődleges és a távoli elsődleges és másodlagos be |
> | Művelet | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | Feladatátvétel azonnal esetleges adatvesztés, ezt az adatbázist és a replikációs relationship\u0027s az elsődleges és a távoli elsődleges és másodlagos be |
> | Művelet | Microsoft.Sql/servers/databases/replicationLinks/read | Egy adott adatbázist létrehozni a replikációs hivatkozások visszatérési részletei |
> | Művelet | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | Állítsa le a replikációs kapcsolat kényszerített módon vagy a partnerrel való szinkronizálás után |
> | Művelet | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | A frissítési replikációs mód csatolás szinkron vagy aszinkron módban |
> | Művelet | Microsoft.Sql/servers/databases/restorePoints/action | Új visszaállítási pont létrehozása |
> | Művelet | Microsoft.Sql/servers/databases/restorePoints/delete | Törli az adatbázis visszaállítási pontot. |
> | Művelet | Microsoft.Sql/servers/databases/restorePoints/read | Vissza visszaállítási pontok az adatbázishoz. |
> | Művelet | Microsoft.Sql/servers/databases/resume/action | Az Azure SQL-adatraktár adatbázisa folytatása |
> | Művelet | Microsoft.Sql/servers/databases/schemas/read | Adatbázis sémák listájának beolvasása |
> | Művelet | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Egy tábla oszlopainak listájának beolvasása |
> | Művelet | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | A megadott oszlop tartalomcímke törlése |
> | Művelet | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | A megadott oszlop tartalomcímke beolvasása |
> | Művelet | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | Létrehozni vagy frissíteni az adott oszlop tartalomcímke |
> | Művelet | Microsoft.Sql/servers/databases/schemas/tables/read | Egy adatbázis táblák listáját beolvasása |
> | Művelet | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | Egy adatbázishoz ajánlott index listájának beolvasása |
> | Művelet | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | Alkalmazza az ajánlott index |
> | Művelet | Microsoft.Sql/servers/databases/securityAlertPolicies/read | A fenyegetés szabályzat egy adott adatbázisnak konfigurált részleteinek beolvasása |
> | Művelet | Microsoft.Sql/servers/databases/securityAlertPolicies/write | Egy adott adatbázisnak threat detection szabályzatának módosítása |
> | Művelet | Microsoft.Sql/servers/databases/securityMetrics/read | Lekérdezi az adatbázis biztonsági metrikák gyűjteménye |
> | Művelet | Microsoft.Sql/servers/databases/sensitivityLabels/read | Egy adott adatbázisnak érzékenységi címkéket listája |
> | Művelet | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | Térjen javaslat adatbázis méretezésével felfelé vagy lefelé kapcsolatos teljesítményének javítása és a költségek csökkentése a lekérdezés végrehajtási statisztika alapján |
> | Művelet | Microsoft.Sql/servers/databases/skus/read | Az adatbázis elérhető termékváltozatok gyűjteménye beolvasása |
> | Művelet | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | Szinkronizálási csoport szinkronizálás megszakítása |
> | Művelet | Microsoft.Sql/servers/databases/syncGroups/delete | Törli a meglévő szinkronizálási csoport. |
> | Művelet | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | Térjen vissza a szinkronizálási listájának hub adatbázis sémák |
> | Művelet | Microsoft.Sql/servers/databases/syncGroups/logs/read | Térjen vissza a szinkronizálási csoport naplók listája |
> | Művelet | Microsoft.Sql/servers/databases/syncGroups/read | Térjen vissza a szinkronizálási csoportok, vagy a megadott szinkronizálási csoport tulajdonságainak beolvasása. |
> | Művelet | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | Szinkronizáló központ adatbázis séma frissítése |
> | Művelet | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | A Szinkronizáló központ séma frissítési művelet eredményének beolvasása |
> | Művelet | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | Törli a meglévő szinkronizálási tagjára. |
> | Művelet | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | A megadott szinkronizálási tag térjen vissza a szinkronizálási tagok vagy lekérdezi a tulajdonságok listáját. |
> | Művelet | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | Szinkronizálási tag séma frissítése |
> | Művelet | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | A szinkronizálás tag séma frissítési művelet eredményének beolvasása |
> | Művelet | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | Térjen vissza a szinkronizálási listájának tag adatbázis sémák |
> | Művelet | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | Hoz létre a szinkronizálási tagja a megadott paramétereket, vagy a megadott szinkronizálási tag tulajdonságainak frissítéséhez. |
> | Művelet | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | Eseményindító szinkronizálási csoport szinkronizálási |
> | Művelet | Microsoft.Sql/servers/databases/syncGroups/write | Szinkronizálási csoportot hoz létre a megadott paraméterekkel, vagy a megadott szinkronizálási csoport tulajdonságainak frissítéséhez. |
> | Művelet | Microsoft.Sql/servers/databases/topQueries/queryText/action | A kijelölt lekérdezés Azonosítóját a Transact-SQL szöveget adja vissza |
> | Művelet | Microsoft.Sql/servers/databases/topQueries/read | Értéket ad vissza kijelölt időszakra összesített értéket a kijelölt lekérdezésre vonatkozó futásidejű statisztikák |
> | Művelet | Microsoft.Sql/servers/databases/topQueries/statistics/read | Értéket ad vissza kijelölt időszakra összesített értéket a kijelölt lekérdezésre vonatkozó futásidejű statisztikák |
> | Művelet | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | Lekérdezi a folyamatban lévő műveletek átlátható adattitkosítás |
> | Művelet | Microsoft.Sql/servers/databases/transparentDataEncryption/read | Állapot és átlátható titkosítási biztonsági szolgáltatást egy adott adatbázisnak részleteinek beolvasása |
> | Művelet | Microsoft.Sql/servers/databases/transparentDataEncryption/write | Átlátható titkosítási állapot módosítása |
> | Művelet | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | Frissítés Azure SQL-adatraktár adatbázisa |
> | Művelet | Microsoft.Sql/servers/databases/usages/read | Az Azure SQL Database módjait információ lekérése |
> | Művelet | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | Távolítsa el a megadott adatbázis biztonsági réseinek értékelése |
> | Művelet | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | A biztonsági réseinek értékelése, egy adott adatbázisnak konfigurált részleteinek beolvasása |
> | Művelet | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | Távolítsa el a biztonsági rés értékelése szabály alaptervet egy adott adatbázisnak |
> | Művelet | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | Egy adott adatbázisnak a biztonsági rés értékelése szabály alapterv lekérése |
> | Művelet | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | Egy adott adatbázisnak a biztonsági rés értékelése szabály alaptervet módosítása |
> | Művelet | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | Egy meglévő vizsgálati eredmény átalakítása emberi olvasható formátumba. Ha már létezik semmi nem történik, |
> | Művelet | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | A biztonsági rés adatbázis vizsgálat hajtható végre. |
> | Művelet | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | Az adatbázis biztonsági rés listája assessment vizsgálat rekordot ad vissza, vagy a vizsgálat rekord lekérése a megadott vizsgálat azonosítóját. |
> | Művelet | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | A biztonsági réseinek értékelése egy adott adatbázis módosítása |
> | Művelet | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | A biztonsági rés adatbázis vizsgálat hajtható végre. |
> | Művelet | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | Az adatbázis biztonsági rés vizsgálatának végrehajtásához hajtsa végre a művelet eredményének beolvasása |
> | Művelet | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | A biztonsági réseinek értékelése, egy adott adatbázisnak konfigurált részleteinek beolvasása |
> | Művelet | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | A biztonsági réseinek értékelése egy adott adatbázis módosítása |
> | Művelet | Microsoft.Sql/servers/databases/write | Létrehoz egy adatbázist a megadott paraméterekkel, vagy a Tulajdonságok vagy a megadott adatbázis címkék frissítése. |
> | Művelet | Microsoft.Sql/servers/delete | Egy meglévő kiszolgáló törlése. |
> | Művelet | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | Egy meglévő vész-helyreállítási beállítások egy adott kiszolgáló törlése |
> | Művelet | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | Feladatátvétel egy DisasterRecoveryConfiguration |
> | Művelet | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | Feladatátvétel egy DisasterRecoveryConfiguration kényszerítése |
> | Művelet | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | Lekérdezi a katasztrófa gyűjteménye, amely tartalmazza az ehhez a kiszolgálóhoz helyreállítási konfigurációk |
> | Művelet | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | Kiszolgáló vész helyreállítási konfigurációjának módosítása |
> | Művelet | Microsoft.Sql/servers/elasticPoolEstimates/read | Ehhez a kiszolgálóhoz már létrehozott rugalmas készlet becslések listáját adja vissza |
> | Művelet | Microsoft.Sql/servers/elasticPoolEstimates/write | Hoz létre új rugalmas készletbecslés megadott adatbázisok listája |
> | Művelet | Microsoft.Sql/servers/elasticPools/advisors/read | A rugalmas készlet elérhető tanácsadók listáját adja vissza |
> | Művelet | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | Javasolt művelet a megadott advisor a rugalmas készlet listáját adja vissza |
> | Művelet | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | A rugalmas készlet alkalmazni a javasolt művelet |
> | Művelet | Microsoft.Sql/servers/elasticPools/advisors/write | Frissítés Automatikus végrehajtás az advisor szinten a rugalmas készlet állapotát. |
> | Művelet | Microsoft.Sql/servers/elasticPools/databases/read | Rugalmas készletek adatbázisok listájának lekérése |
> | Művelet | Microsoft.Sql/servers/elasticPools/delete | A meglévő rugalmas készlet törlése |
> | Művelet | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | Tevékenységek és egy adott rugalmas adatbáziskészlet részleteinek beolvasása |
> | Művelet | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | Tevékenységek és egy adott adatbázisnak, amely része a rugalmas adatbáziskészlet részleteinek beolvasása |
> | Művelet | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | Térjen vissza a rugalmas adatbáziskészletek elérhető metrikák típusú |
> | Művelet | Microsoft.Sql/servers/elasticPools/metrics/read | Térjen vissza a rugalmas adatbáziskészletek metrikák |
> | Művelet | Microsoft.Sql/servers/elasticPools/operations/cancel/action | Visszavonja az Azure SQL rugalmas készlet függőben lévő aszinkron művelet, amely még nem fejeződött be. |
> | Művelet | Microsoft.Sql/servers/elasticPools/operations/read | A művelet végrehajtását, a rugalmas készlet listáját adja vissza |
> | Művelet | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Művelet | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Művelet | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | Térjen vissza a rugalmas adatbáziskészletek elérhető metrikák típusú |
> | Művelet | Microsoft.Sql/servers/elasticPools/read | A rugalmas készlet a megadott kiszolgálón részleteinek beolvasása |
> | Művelet | Microsoft.Sql/servers/elasticPools/skus/read | Elérhető termékváltozatok gyűjteménye lekérdezi a rugalmas készletek |
> | Művelet | Microsoft.Sql/servers/elasticPools/write | Hozzon létre egy új vagy meglévő rugalmas készlet tulajdonságainak módosítása |
> | Művelet | Microsoft.Sql/servers/encryptionProtector/read | Kiszolgáló titkosítási protectors listáját adja vissza, vagy tulajdonságait olvassa be a megadott kiszolgálóhoz tartozó titkosítási védőt. |
> | Művelet | Microsoft.Sql/servers/encryptionProtector/write | Az a megadott kiszolgáló titkosítási védő tulajdonságainak frissítéséhez. |
> | Művelet | Microsoft.Sql/servers/extendedAuditingSettings/read | A kiterjesztett kiszolgáló blob a megadott kiszolgálón konfigurált naplórendet részleteinek beolvasása |
> | Művelet | Microsoft.Sql/servers/extendedAuditingSettings/write | A kiterjesztett kiszolgáló blob naplózását egy adott kiszolgáló módosítása |
> | Művelet | Microsoft.Sql/servers/failoverGroups/delete | Egy meglévő feladatátvevő csoport törlése. |
> | Művelet | Microsoft.Sql/servers/failoverGroups/failover/action | Tervezett feladatátvétel végrehajtása egy meglévő feladatátvevő csoportban. |
> | Művelet | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | Végrehajtja a kényszerített feladatátvételi egy meglévő feladatátvevő csoportban. |
> | Művelet | Microsoft.Sql/servers/failoverGroups/read | A lista a feladatátvétel csoportokat vagy a megadott feladatátvételi csoport tulajdonságainak beolvasása adja vissza. |
> | Művelet | Microsoft.Sql/servers/failoverGroups/write | Feladatátvételi csoportot hoz létre a megadott paraméterekkel, vagy frissíti a Tulajdonságok vagy a megadott feladatátvételi csoport címkék. |
> | Művelet | Microsoft.Sql/servers/firewallRules/delete | Meglévő kiszolgáló tűzfalszabály törlése. |
> | Művelet | Microsoft.Sql/servers/firewallRules/read | A kiszolgáló tűzfal listáját szabályok vagy tulajdonságait olvassa be a megadott kiszolgálóhoz tartozó tűzfalszabály vissza. |
> | Művelet | Microsoft.Sql/servers/firewallRules/write | Létrehoz egy tűzfalszabály létrehozása a megadott paraméterekkel, a megadott szabály tulajdonságainak frissítéséhez, vagy minden meglévő szabályok felülírása az új kiszolgáló tűzfal-szabályok. |
> | Művelet | Microsoft.Sql/servers/import/action | Hozzon létre egy új adatbázist a kiszolgálón, és a séma és adatainak áttelepítését egy DacPac csomag telepítése |
> | Művelet | Microsoft.Sql/servers/importExportOperationResults/read | Lekérdezi a folyamatban lévő importálási/exportálási műveleteket |
> | Művelet | Microsoft.Sql/servers/keys/delete | Törli a meglévő kiszolgáló-kulcsot. |
> | Művelet | Microsoft.Sql/servers/keys/read | Térjen vissza a kiszolgáló a kulcsok vagy a megadott kiszolgáló kulcs tulajdonságainak beolvasása. |
> | Művelet | Microsoft.Sql/servers/keys/write | Létrehoz egy kulcsot a megadott paraméterekkel, vagy a Tulajdonságok vagy a megadott kiszolgálókulcs címkék frissítése. |
> | Művelet | Microsoft.Sql/servers/networkInterfaces/read | A megadott hálózati adapter tulajdonságainak beolvasása |
> | Művelet | Microsoft.Sql/servers/networkInterfaces/write | A megadott paraméterekkel a hálózati illesztő létrehozása vagy frissítése. a Tulajdonságok vagy a címkék a megadott hálózati adapter |
> | Művelet | Microsoft.Sql/servers/operationResults/read | Lekérdezi a folyamatban lévő kiszolgáló műveletei |
> | Művelet | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | Térjen vissza a kiszolgálók esetén elérhetők metrikák típusai |
> | Művelet | Microsoft.Sql/servers/read | A kiszolgálók vagy lekérdezi a megadott kiszolgálóhoz tartozó tulajdonságok listáját adja vissza. |
> | Művelet | Microsoft.Sql/servers/recommendedElasticPools/databases/read | Egy adott kiszolgálóhoz ajánlott rugalmas adatbáziskészletek metrikák beolvasása |
> | Művelet | Microsoft.Sql/servers/recommendedElasticPools/read | Ajánlott rugalmas adatbáziskészlet költségeinek csökkentése vagy historica erőforrás-használat alapján a teljesítmény javítása beolvasása |
> | Művelet | Microsoft.Sql/servers/recoverableDatabases/read | Ez a művelet segítségével élő adatbázis vész-helyreállítási utolsó ismert helyes biztonsági mentési pontok-adatbázis helyreállításához. Olyan információkat ad vissza a legutóbbi helyes biztonsági másolatot, de az kapcsolatos doesn\u0027t ténylegesen állítsa vissza az adatbázist. |
> | Művelet | Microsoft.Sql/servers/restorableDroppedDatabases/read | Az adatbázisok listája, amelyek el lettek dobva a megadott kiszolgálón még mindig belüli adatmegőrzési beolvasása. |
> | Művelet | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | A kiszolgáló fenyegetések észlelése házirend írási művelet eredményének beolvasása |
> | Művelet | Microsoft.Sql/servers/securityAlertPolicies/read | A megadott kiszolgálón konfigurált kiszolgáló fenyegetés észlelési házirend részleteinek beolvasása |
> | Művelet | Microsoft.Sql/servers/securityAlertPolicies/write | A kiszolgáló fenyegetés szabályzat egy adott kiszolgáló módosítása |
> | Művelet | Microsoft.Sql/servers/serviceObjectives/read | A szolgáltatási szint célkitűzései (más néven teljesítmény rétegek) a megadott kiszolgálón elérhető listájának beolvasása |
> | Művelet | Microsoft.Sql/servers/syncAgents/delete | Törli a meglévő szinkronizálási ügynök. |
> | Művelet | Microsoft.Sql/servers/syncAgents/generateKey/action | Szinkronizálási ügynök regisztrálás kulcs létrehozása |
> | Művelet | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | A listájának kapcsolódó ügynök adatbázisok szinkronizálása |
> | Művelet | Microsoft.Sql/servers/syncAgents/read | A megadott szinkronizálási ügynök térjen vissza a szinkronizálási ügynökök vagy lekérdezi a tulajdonságok listáját. |
> | Művelet | Microsoft.Sql/servers/syncAgents/write | A megadott paraméterek hoz létre a sync-ügynök vagy a megadott szinkronizálási ügynök tulajdonságainak frissítéséhez. |
> | Művelet | Microsoft.Sql/servers/usages/read | Térjen vissza a kiszolgáló DTU-kvótáról és aktuális DTU consuption minden adatbázis-kiszolgálón belül |
> | Művelet | Microsoft.Sql/servers/virtualNetworkRules/delete | Egy meglévő virtuális hálózat szabály törlése |
> | Művelet | Microsoft.Sql/servers/virtualNetworkRules/read | Térjen vissza a virtuális hálózat szabályok vagy a megadott virtuális hálózati szabály tulajdonságainak beolvasása. |
> | Művelet | Microsoft.Sql/servers/virtualNetworkRules/write | A virtuális hálózati szabályt hoz létre a megadott paraméterekkel, vagy a Tulajdonságok vagy a megadott virtuális hálózati szabály címkék frissítése. |
> | Művelet | Microsoft.Sql/servers/write | Kiszolgáló létrehozása a megadott paraméterekkel, vagy frissítse a Tulajdonságok vagy a címkék a megadott kiszolgáló. |
> | Művelet | Microsoft.Sql/unregister/action | Az előfizetés a Microsoft SQL-adatbázis erőforrás-szolgáltató regisztrációjának törlése, és lehetővé teszi a Microsoft SQL-adatbázisok létrehozását. |
> | Művelet | Microsoft.Sql/virtualClusters/read | A virtuális fürtök vagy lekérdezi a megadott virtuális fürt tulajdonságok listáját adja vissza. |
> | Művelet | Microsoft.Sql/virtualClusters/write | Frissíti a virtuális fürt címkék. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.Storage/checknameavailability/read | Ellenőrzi, hogy érvényes-e a fióknév, és nincs-e használatban. |
> | Művelet | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | A Microsoft.Storage értesítése arról, hogy virtuális hálózat vagy alhálózat törlése van folyamatban |
> | Művelet | Microsoft.Storage/operations/read | Az aszinkrón műveletek állapotának lekérdezése. |
> | Művelet | Microsoft.Storage/register/action | A tárolásierőforrás-szolgáltató előfizetésének regisztrálása és a tárfiókkészítés engedélyezése. |
> | Művelet | Microsoft.Storage/skus/read | A Microsoft.Storage által támogatott termékváltozatok listázása. |
> | Művelet | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | Blobtároló visszatartásának törlése |
> | Művelet | Microsoft.Storage/storageAccounts/blobServices/containers/delete | A tároló törlésének eredményét adja vissza |
> | Művelet | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | Blobtároló módosíthatatlansági szabályzatának törlése |
> | Művelet | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | Blobtároló bővítésére vonatkozó módosíthatatlansági szabályzat |
> | Művelet | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | Blobtároló zárolására vonatkozó módosíthatatlansági szabályzat |
> | Művelet | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | Blobtároló beolvasására vonatkozó módosíthatatlansági szabályzat |
> | Művelet | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | Blobtárolón végrehajtott put műveletre vonatkozó módosíthatatlansági szabályzat |
> | Művelet | Microsoft.Storage/storageAccounts/blobServices/containers/read | Egy tárolót vagy tárolók listáját adja vissza |
> | Művelet | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | Blobtároló visszatartásának beállítása |
> | Művelet | Microsoft.Storage/storageAccounts/blobServices/containers/write | A put vagy lease blobtároló-művelet eredményének visszaadása |
> | Művelet | Microsoft.Storage/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításának beolvasása. |
> | Művelet | Microsoft.Storage/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése. |
> | Művelet | Microsoft.Storage/storageAccounts/blobServices/providers/Microsoft.Insights/metricDefinitions/read | A Microsoft Storage metrikadefinícióit tartalmazó lista beolvasása. |
> | Művelet | Microsoft.Storage/storageAccounts/blobServices/read | A Blob szolgáltatás tulajdonságait vagy a statisztikát adja vissza |
> | Művelet | Microsoft.Storage/storageAccounts/blobServices/write | A blobszolgáltatás tulajdonságain végrehajtott Put művelet eredményének visszaadása |
> | Művelet | Microsoft.Storage/storageAccounts/delete | Meglévő tárfiók törlése. |
> | Művelet | Microsoft.Storage/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításának beolvasása. |
> | Művelet | Microsoft.Storage/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése. |
> | Művelet | Microsoft.Storage/storageAccounts/fileServices/providers/Microsoft.Insights/metricDefinitions/read | A Microsoft Storage metrikadefinícióit tartalmazó lista beolvasása. |
> | Művelet | Microsoft.Storage/storageAccounts/listAccountSas/action | A fiók SAS-jogkivonatát adja vissza a megadott tárfiókhoz. |
> | Művelet | Microsoft.Storage/storageAccounts/listkeys/action | A megadott tárfiók hozzáférési kulcsainak lekérése. |
> | Művelet | Microsoft.Storage/storageAccounts/listServiceSas/action | A szolgáltatás SAS-jogkivonatát adja vissza a meghatározott tárfiókhoz. |
> | Művelet | Microsoft.Storage/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításának beolvasása. |
> | Művelet | Microsoft.Storage/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése. |
> | Művelet | Microsoft.Storage/storageAccounts/providers/Microsoft.Insights/metricDefinitions/read | A Microsoft Storage metrikadefinícióit tartalmazó lista beolvasása. |
> | Művelet | Microsoft.Storage/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése. |
> | Művelet | Microsoft.Storage/storageAccounts/queueServices/providers/Microsoft.Insights/metricDefinitions/read | A Microsoft Storage metrikadefinícióit tartalmazó lista beolvasása. |
> | Művelet | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Sor törlésének eredményét adja vissza |
> | Művelet | Microsoft.Storage/storageAccounts/queueServices/queues/read | A sort vagy a sorok listáját adja vissza. |
> | Művelet | Microsoft.Storage/storageAccounts/queueServices/queues/write | A sor írásának eredményét adja vissza |
> | Művelet | Microsoft.Storage/storageAccounts/queueServices/read | A Queue szolgáltatás tulajdonságait vagy a statisztikát adja vissza. |
> | Művelet | Microsoft.Storage/storageAccounts/queueServices/write | A Queue szolgáltatás tulajdonságai beállításának eredményét adja vissza |
> | Művelet | Microsoft.Storage/storageAccounts/read | A tárfiókok listájának vagy a megadott tárfiók tulajdonságainak lekérése. |
> | Művelet | Microsoft.Storage/storageAccounts/regeneratekey/action | A megadott tárfiók hozzáférési kulcsainak újragenerálása. |
> | Művelet | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | Tárfiók diagnosztikai beállításainak létrehozása vagy frissítése. |
> | Művelet | Microsoft.Storage/storageAccounts/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításának beolvasása. |
> | Művelet | Microsoft.Storage/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításának beolvasása. |
> | Művelet | Microsoft.Storage/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése. |
> | Művelet | Microsoft.Storage/storageAccounts/tableServices/providers/Microsoft.Insights/metricDefinitions/read | A Microsoft Storage metrikadefinícióit tartalmazó lista beolvasása. |
> | Művelet | Microsoft.Storage/storageAccounts/write | Tárfiók létrehozása a megadott paraméterekkel, a tulajdonságok vagy címkék frissítése, vagy egyéni tartomány felvétele a megadott tárfiókhoz. |
> | Művelet | Microsoft.Storage/usages/read | A megadott előfizetésben szereplő erőforrások korlátját és jelenlegi kihasználtsági értéket adja vissza |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | microsoft.storagesync/storageSyncServices/delete | A tárolási szinkronizálási szolgáltatások törlése |
> | Művelet | microsoft.storagesync/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read | A tárolási szinkronizálási szolgáltatások elérhető metrikai meghatározások beolvasása |
> | Művelet | microsoft.storagesync/storageSyncServices/read | Olvassa el a tárolási szinkronizálási szolgáltatások |
> | Művelet | microsoft.storagesync/storageSyncServices/registeredServers/delete | Bármely regisztrált kiszolgáló törlése |
> | Művelet | microsoft.storagesync/storageSyncServices/registeredServers/read | Bármely regisztrált kiszolgáló olvasása |
> | Művelet | microsoft.storagesync/storageSyncServices/registeredServers/write | Létrehozni vagy frissíteni a regisztrált kiszolgáló |
> | Művelet | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | Felhő végpontok törlése |
> | Művelet | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | Hely api aszinkron biztonsági mentési hívások |
> | Művelet | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | Biztonsági mentés után a művelet hívása |
> | Művelet | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | Ez a művelet visszaállítás után hívható |
> | Művelet | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | Biztonsági mentés előtt. Ez a művelet hívása |
> | Művelet | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | Ez a művelet a visszaállítási előtt hívható |
> | Művelet | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | Olvassa el a felhő végpontok |
> | Művelet | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | Szívverés visszaállítása |
> | Művelet | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | Létrehozni vagy frissíteni a felhő végpontok |
> | Művelet | microsoft.storagesync/storageSyncServices/syncGroups/delete | A szinkronizálási csoportok törlése |
> | Művelet | microsoft.storagesync/storageSyncServices/syncGroups/read | Olvassa el a szinkronizálási csoportok |
> | Művelet | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | Kiszolgáló végpontok törlése |
> | Művelet | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | Olvassa el a kiszolgáló végpontok |
> | Művelet | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | Ez a művelet egy visszahívás hívása |
> | Művelet | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | Létrehozni vagy frissíteni Server végpontok |
> | Művelet | microsoft.storagesync/storageSyncServices/syncGroups/write | A szinkronizálási csoportok létrehozása vagy frissítése |
> | Művelet | microsoft.storagesync/storageSyncServices/write | A tárolási szinkronizálási szolgáltatások létrehozása vagy módosítása |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.StorSimple/managers/accessControlRecords/delete | A hozzáférés-vezérlési rekordokat törlése |
> | Művelet | Microsoft.StorSimple/managers/accessControlRecords/read | Sorolja fel, vagy a hozzáférés-vezérlési rekordokat beolvasása |
> | Művelet | Microsoft.StorSimple/managers/accessControlRecords/write | A hozzáférés-vezérlő rekordok létrehozása vagy frissítése |
> | Művelet | Microsoft.StorSimple/managers/alerts/read | Lekérdezi a riasztásokat vagy listája |
> | Művelet | Microsoft.StorSimple/managers/bandwidthSettings/delete | Törli a meglévő sávszélesség-beállítások (8000 sorozat csak) |
> | Művelet | Microsoft.StorSimple/managers/bandwidthSettings/read | A sávszélesség-beállítások (csak 8000 sorozat) |
> | Művelet | Microsoft.StorSimple/managers/bandwidthSettings/write | Létrehoz egy új, vagy a sávszélesség-beállítások frissítése (8000 sorozat csak) |
> | Művelet | Microsoft.StorSimple/Managers/certificates/write | A frissítés erőforrás tanúsítvány művelet frissíti az erőforrás/tárolói hitelesítő adatainak tanúsítványa. |
> | Művelet | Microsoft.StorSimple/managers/clearAlerts/action | Törölje a jelet az Eszközkezelőben társított összes riasztást. |
> | Művelet | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | A felhő készülék lista támogatott konfigurációk |
> | Művelet | Microsoft.StorSimple/managers/configureDevice/action | Eszközök konfigurálása |
> | Művelet | Microsoft.StorSimple/managers/delete | Törli az eszköz-kezelők |
> | Művelet | Microsoft.StorSimple/Managers/delete | A tároló törlése a művelet törli a megadott Azure-erőforrás "tárolóban" típusú |
> | Művelet | Microsoft.StorSimple/managers/devices/alertSettings/read | Sorolja fel, vagy a riasztási beállításainak beolvasása |
> | Művelet | Microsoft.StorSimple/managers/devices/alertSettings/write | A riasztási beállításainak létrehozása vagy frissítése |
> | Művelet | Microsoft.StorSimple/managers/devices/backupPolicies/backup/action | Igény szerinti létrehozásához manuális biztonsági mentés készítése a házirend által védett összes kötet biztonsági mentését. |
> | Művelet | Microsoft.StorSimple/managers/devices/backupPolicies/delete | Törli a meglévő biztonsági mentési házirendek (8000 sorozat csak) |
> | Művelet | Microsoft.StorSimple/managers/devices/backupPolicies/read | A lista a biztonsági mentési házirendek (csak 8000 sorozat) |
> | Művelet | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | Egy meglévő ütemezés törlése |
> | Művelet | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/read | Az ütemezések felsorolása |
> | Művelet | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/write | Létrehoz egy új, vagy az ütemezések frissítése |
> | Művelet | Microsoft.StorSimple/managers/devices/backupPolicies/write | Létrehoz egy új, vagy frissíti a biztonsági mentési házirendek (8000 sorozat csak) |
> | Művelet | Microsoft.StorSimple/managers/devices/backups/delete | A biztonságimásolat-készlet törlése |
> | Művelet | Microsoft.StorSimple/managers/devices/backups/elements/clone/action | Klónozni egy fájlmegosztás vagy kötet egy biztonsági mentési elem használatával. |
> | Művelet | Microsoft.StorSimple/managers/devices/backups/read | Lekérdezi a biztonságimásolat-készletet vagy listája |
> | Művelet | Microsoft.StorSimple/managers/devices/backups/restore/action | Állítsa vissza az összes kötet a biztonságimásolat-készletből. |
> | Művelet | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | A biztonsági mentés ütemezése csoportok törlése |
> | Művelet | Microsoft.StorSimple/managers/devices/backupScheduleGroups/read | Sorolja fel, vagy a biztonsági mentési ütemezés csoportok beolvasása |
> | Művelet | Microsoft.StorSimple/managers/devices/backupScheduleGroups/write | A biztonsági mentési ütemezés csoportok létrehozása vagy frissítése |
> | Művelet | Microsoft.StorSimple/managers/devices/chapSettings/delete | Törli a Chap-beállításokat |
> | Művelet | Microsoft.StorSimple/managers/devices/chapSettings/read | Sorolja fel, vagy a Chap beállításainak beolvasása |
> | Művelet | Microsoft.StorSimple/managers/devices/chapSettings/write | A Chap beállításainak létrehozása vagy frissítése |
> | Művelet | Microsoft.StorSimple/managers/devices/deactivate/action | Egy eszköz inaktiválja. |
> | Művelet | Microsoft.StorSimple/managers/devices/delete | Az eszközök törlése |
> | Művelet | Microsoft.StorSimple/managers/devices/download/action | Egy eszköz letöltési frissítéseket. |
> | Művelet | Microsoft.StorSimple/managers/devices/failover/action | Az eszköz feladatátvétele. |
> | Művelet | Microsoft.StorSimple/managers/devices/fileservers/backup/action | Egy fájlkiszolgáló biztonsági másolatok készítéséhez. |
> | Művelet | Microsoft.StorSimple/managers/devices/fileservers/delete | Törli a fájlkiszolgálókon |
> | Művelet | Microsoft.StorSimple/managers/devices/fileservers/metrics/read | Sorolja fel, vagy a metrikák beolvasása |
> | Művelet | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | Sorolja fel, vagy a metrikák meghatározások beolvasása |
> | Művelet | Microsoft.StorSimple/managers/devices/fileservers/read | Lekérdezi a fájlkiszolgálók vagy listája |
> | Művelet | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | Törli a megosztások |
> | Művelet | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | Sorolja fel, vagy a metrikák beolvasása |
> | Művelet | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | Sorolja fel, vagy a metrikák meghatározások beolvasása |
> | Művelet | Microsoft.StorSimple/managers/devices/fileservers/shares/read | Lekérdezi a megosztásokat vagy listája |
> | Művelet | Microsoft.StorSimple/managers/devices/fileservers/shares/write | Létrehozni vagy frissíteni a megosztásokat |
> | Művelet | Microsoft.StorSimple/managers/devices/fileservers/write | Létrehozni vagy frissíteni a fájlkiszolgálókon |
> | Művelet | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | Hardver összetevőcsoportok vezérlő power állapotának módosítása |
> | Művelet | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | A hardver összetevő csoportokat |
> | Művelet | Microsoft.StorSimple/managers/devices/install/action | Frissítések telepítése egy eszközön. |
> | Művelet | Microsoft.StorSimple/managers/devices/installUpdates/action | Telepíti a frissítéseket az eszközökön |
> | Művelet | Microsoft.StorSimple/managers/devices/iscsiservers/backup/action | Az iSCSI-kiszolgálók biztonsági másolatok készítéséhez. |
> | Művelet | Microsoft.StorSimple/managers/devices/iscsiservers/delete | Törli az iSCSI-kiszolgálók |
> | Művelet | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | A lemezek törlése |
> | Művelet | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metrics/read | Sorolja fel, vagy a metrikák beolvasása |
> | Művelet | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | Sorolja fel, vagy a metrikák meghatározások beolvasása |
> | Művelet | Microsoft.StorSimple/managers/devices/iscsiservers/disks/read | Lekérdezi a lemezek vagy listája |
> | Művelet | Microsoft.StorSimple/managers/devices/iscsiservers/disks/write | Létrehozni vagy frissíteni a lemezek |
> | Művelet | Microsoft.StorSimple/managers/devices/iscsiservers/metrics/read | Sorolja fel, vagy a metrikák beolvasása |
> | Művelet | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | Sorolja fel, vagy a metrikák meghatározások beolvasása |
> | Művelet | Microsoft.StorSimple/managers/devices/iscsiservers/read | Sorolja fel, vagy az iSCSI kiszolgálók beolvasása |
> | Művelet | Microsoft.StorSimple/managers/devices/iscsiservers/write | Létrehozni vagy frissíteni az iSCSI-kiszolgálók |
> | Művelet | Microsoft.StorSimple/managers/devices/jobs/cancel/action | Egy futó feladat megszakítása |
> | Művelet | Microsoft.StorSimple/managers/devices/jobs/read | Sorolja fel, vagy a feladat beolvasása |
> | Művelet | Microsoft.StorSimple/managers/devices/listFailoverSets/action | A lista a feladatátvétel beállítja egy meglévő eszközt. |
> | Művelet | Microsoft.StorSimple/managers/devices/listFailoverTargets/action | Az eszközök feladatátvételi céljainak |
> | Művelet | Microsoft.StorSimple/managers/devices/metrics/read | Sorolja fel, vagy a metrikák beolvasása |
> | Művelet | Microsoft.StorSimple/managers/devices/metricsDefinitions/read | Sorolja fel, vagy a metrikák meghatározások beolvasása |
> | Művelet | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigration/action | Megerősíti, hogy a sikeres áttelepítéshez és véglegesítheti. |
> | Művelet | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action | Áttelepítési megerősítése állapotának beolvasása. |
> | Művelet | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | Az áttelepítési becslés feladat állapotának beolvasása. |
> | Művelet | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action | Az áttelepítés állapotának beolvasása. |
> | Művelet | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | Az áttelepítéshez forrás konfigurációk importálása |
> | Művelet | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigration/action | Forrás-konfigurációk használatával áttelepítés indítása |
> | Művelet | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action | Az áttelepítési folyamat időtartamának becsléséhez feladat indítása. |
> | Művelet | Microsoft.StorSimple/managers/devices/networkSettings/read | Sorolja fel, vagy a hálózati beállításainak beolvasása |
> | Művelet | Microsoft.StorSimple/managers/devices/networkSettings/write | Létrehoz egy új, vagy a hálózati beállítások frissítése |
> | Művelet | Microsoft.StorSimple/managers/devices/publicEncryptionKey/action | Lista nyilvános titkosítási kulcsot, az Eszközkezelőben |
> | Művelet | Microsoft.StorSimple/managers/devices/publishSupportPackage/action | Egy eszköz a Microsoft Support hibaelhárítási támogatási csomag közzététele. |
> | Művelet | Microsoft.StorSimple/managers/devices/read | Sorolja fel, vagy az eszköz beolvasása |
> | Művelet | Microsoft.StorSimple/managers/devices/scanForUpdates/action | Egy eszköz frissítések keresése. |
> | Művelet | Microsoft.StorSimple/managers/devices/securitySettings/read | A biztonsági beállítások |
> | Művelet | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | A távoli felügyeleti tanúsítvány eszköz szinkronizálása. |
> | Művelet | Microsoft.StorSimple/managers/devices/securitySettings/update/action | A biztonsági beállítások frissítése. |
> | Művelet | Microsoft.StorSimple/managers/devices/securitySettings/write | Létrehoz egy új, vagy a biztonsági beállítások frissítése |
> | Művelet | Microsoft.StorSimple/managers/devices/sendTestAlertEmail/action | Konfigurált küldési e-mail címzetteknek teszt figyelmeztető e-mailt küldeni. |
> | Művelet | Microsoft.StorSimple/managers/devices/timeSettings/read | Sorolja fel, vagy a idő beállításainak beolvasása |
> | Művelet | Microsoft.StorSimple/managers/devices/timeSettings/write | Létrehoz egy új, vagy frissíti az idő beállítása |
> | Művelet | Microsoft.StorSimple/managers/devices/updateSummary/read | Sorolja fel, vagy a frissítés összegzés beolvasása |
> | Művelet | Microsoft.StorSimple/managers/devices/volumeContainers/delete | Törli a meglévő Kötettárolók (8000 sorozat csak) |
> | Művelet | Microsoft.StorSimple/managers/devices/volumeContainers/listEncryptionKeys/action | Lista titkosítási kulcsokat a Kötettárolók |
> | Művelet | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | A metrikák felsorolása |
> | Művelet | Microsoft.StorSimple/managers/devices/volumeContainers/metricsDefinitions/read | A metrikák definíciók felsorolása |
> | Művelet | Microsoft.StorSimple/managers/devices/volumeContainers/read | Kötettárolók listázása (csak 8000 sorozat) |
> | Művelet | Microsoft.StorSimple/managers/devices/volumeContainers/rolloverEncryptionKey/action | Kötettárolók a helyettesítő titkosítási kulcsok |
> | Művelet | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/delete | Egy meglévő kötetek törlése |
> | Művelet | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | A metrikák felsorolása |
> | Művelet | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | A metrikák definíciók felsorolása |
> | Művelet | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/read | A kötetek felsorolása |
> | Művelet | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/write | Létrehoz egy új vagy frissít kötetek |
> | Művelet | Microsoft.StorSimple/managers/devices/volumeContainers/write | Létrehoz egy új vagy frissít Kötettárolók (8000 sorozat csak) |
> | Művelet | Microsoft.StorSimple/managers/devices/write | Létrehozása vagy frissítése az eszközöket |
> | Művelet | Microsoft.StorSimple/managers/encryptionSettings/read | Sorolja fel, vagy a titkosítási beállításainak beolvasása |
> | Művelet | Microsoft.StorSimple/Managers/extendedInformation/delete | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Művelet | Microsoft.StorSimple/Managers/extendedInformation/read | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Művelet | Microsoft.StorSimple/Managers/extendedInformation/write | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Művelet | Microsoft.StorSimple/managers/getActivationKey/action | Aktiválási kulcs lekérése az Eszközkezelőben. |
> | Művelet | Microsoft.StorSimple/managers/getEncryptionKey/action | Titkosítási kulcs beszerzése az Eszközkezelőben. |
> | Művelet | Microsoft.StorSimple/managers/listActivationKey/action | Az aktiválási kulcsot a StorSimple eszköz Manager lekérdezi. |
> | Művelet | Microsoft.StorSimple/managers/listPrivateEncryptionKey/action | Lekérdezi a személyes titkosítási kulcs egy StorSimple az Eszközkezelőben. |
> | Művelet | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | Nyilvános titkosítási kulcsok egy StorSimple Device Manager listában. |
> | Művelet | Microsoft.StorSimple/managers/metrics/read | Sorolja fel, vagy a metrikák beolvasása |
> | Művelet | Microsoft.StorSimple/managers/metricsDefinitions/read | Sorolja fel, vagy a metrikák meghatározások beolvasása |
> | Művelet | Microsoft.StorSimple/managers/provisionCloudAppliance/action | Hozzon létre egy új felhőalapú készülék. |
> | Művelet | Microsoft.StorSimple/managers/read | Sorolja fel, vagy az eszköz kezelők beolvasása |
> | Művelet | Microsoft.StorSimple/Managers/read | A tároló Get művelet lekérdezi az Azure-erőforrás "tárolóban" típusú képviselő objektum |
> | Művelet | Microsoft.StorSimple/managers/regenarateRegistationCertificate/action | Az eszköz kezelők a regisztrációs tanúsítvány újbóli előállítása. |
> | Művelet | Microsoft.StorSimple/managers/regenerateActivationKey/action | A kezelő aktiválási kulcs újragenerálása. |
> | Művelet | Microsoft.StorSimple/managers/storageAccountCredentials/delete | A Tárfiók hitelesítő adatainak törlése |
> | Művelet | Microsoft.StorSimple/managers/storageAccountCredentials/listAccessKey/action | A Tárfiók hitelesítő adatainak hozzáférési listázása |
> | Művelet | Microsoft.StorSimple/managers/storageAccountCredentials/read | Sorolja fel, vagy a Tárfiók hitelesítő adatainak beolvasása |
> | Művelet | Microsoft.StorSimple/managers/storageAccountCredentials/write | Létrehozni vagy frissíteni a Tárfiók hitelesítő adatait |
> | Művelet | Microsoft.StorSimple/managers/storageDomains/delete | A tárolási tartományok törlése |
> | Művelet | Microsoft.StorSimple/managers/storageDomains/read | Sorolja fel, vagy a tárolási tartományok beolvasása |
> | Művelet | Microsoft.StorSimple/managers/storageDomains/write | Hozzon létre vagy a Storage-tartományok frissítése |
> | Művelet | Microsoft.StorSimple/managers/write | Létrehozni vagy frissíteni az eszköz-kezelők |
> | Művelet | Microsoft.StorSimple/Managers/write | A tárlétrehozási művelettel vault típusú Azure-erőforrás hozható létre |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.StreamAnalytics/locations/quotas/Read | Olvasási Stream Analytics előfizetési kvóta |
> | Művelet | Microsoft.StreamAnalytics/operations/Read | Olvasási Stream Analytics-műveletet |
> | Művelet | Microsoft.StreamAnalytics/Register/action | Regisztrálni az előfizetést a Stream Analytics erőforrás-szolgáltató |
> | Művelet | Microsoft.StreamAnalytics/streamingjobs/Delete | A Stream Analytics-feladat törlése |
> | Művelet | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | Törölje a Stream Analytics-feladat függvény |
> | Művelet | Microsoft.StreamAnalytics/streamingjobs/functions/operationresults/Read | Olvassa el a művelet eredménye a Stream Analytics-feladat függvény |
> | Művelet | Microsoft.StreamAnalytics/streamingjobs/functions/Read | Olvasási Stream Analytics feladatcsoportot |
> | Művelet | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | A Stream Analytics feladat függvény az alapértelmezett definíció beolvasása |
> | Művelet | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | Teszt Stream Analytics feladatcsoportot |
> | Művelet | Microsoft.StreamAnalytics/streamingjobs/functions/Write | Írás a Stream Analytics-feladat függvény |
> | Művelet | Microsoft.StreamAnalytics/streamingjobs/inputs/Delete | Stream Analytics feladat bemeneti törlése |
> | Művelet | Microsoft.StreamAnalytics/streamingjobs/inputs/operationresults/Read | Olvassa el a művelet eredménye a Stream Analytics feladat |
> | Művelet | Microsoft.StreamAnalytics/streamingjobs/inputs/Read | Olvasási Stream Analytics feladat bemeneti |
> | Művelet | Microsoft.StreamAnalytics/streamingjobs/inputs/Sample/action | A minta Stream Analytics feladat bemeneti |
> | Művelet | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | Teszt Stream Analytics feladat bemeneti |
> | Művelet | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | Írás Analytics-feladat adatfolyam-bemenet |
> | Művelet | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | Olvassa el a metrikai meghatározásainak |
> | Művelet | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | Olvassa el a művelet eredménye Stream Analytics-feladat |
> | Művelet | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | Stream Analytics-feladat kimeneti törlése |
> | Művelet | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | Olvassa el a művelet eredménye a Stream Analytics Job Output |
> | Művelet | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | Olvasási Stream Analytics Feladatkiemenetét |
> | Művelet | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | Teszt Stream Analytics feladat eredménye |
> | Művelet | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | Írás Analytics-feladat kimeneti adatfolyam |
> | Művelet | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read | Olvassa el a diagnosztikai beállítást. |
> | Művelet | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write | Diagnosztikai beállításának írási. |
> | Művelet | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | Streamingjobs naplók beolvasása. |
> | Művelet | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | A streamingjobs elérhető metrikai meghatározások beolvasása |
> | Művelet | Microsoft.StreamAnalytics/streamingjobs/Read | Olvasási Stream Analytics-feladat |
> | Művelet | Microsoft.StreamAnalytics/streamingjobs/Start/action | A Stream Analytics-feladat indítása |
> | Művelet | Microsoft.StreamAnalytics/streamingjobs/Stop/action | A Stream Analytics-feladat leállítása |
> | Művelet | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | Törölje a Stream Analytics feladat átalakítása |
> | Művelet | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | Olvasási Stream Analytics feladat átalakítása |
> | Művelet | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | Írás a Stream Analytics feladat átalakítása |
> | Művelet | Microsoft.StreamAnalytics/streamingjobs/Write | Írás a Stream Analytics-feladat |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.Subscription/CreateSubscription/action | Azure-előfizetés létrehozása |
> | Művelet | Microsoft.Subscription/SubscriptionDefinitions/read | A felügyeleti csoporton belül az Azure-előfizetés definíciójának lekérdezése. |
> | Művelet | Microsoft.Subscription/SubscriptionDefinitions/write | Egy Azure-előfizetés-definíció létrehozása |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.Support/register/action | Regisztrálás a támogatási erőforrás-szolgáltatóra |
> | Művelet | Microsoft.Support/supportTickets/read | Egy támogatási jegy adatainak beolvasása (beleértve az állapotot, a súlyosságot, a kapcsolattartási adatokat és a kommunikációs elemeket), vagy az előfizetésekhez tartozó támogatási jegyek listájának beolvasása. |
> | Művelet | Microsoft.Support/supportTickets/write | Létrehozza vagy frissíti egy támogatási jegy. Létrehozhat egy támogatási jegy műszaki, számlázási, kvóták és előfizetés-kezeléssel kapcsolatos problémákat. Súlyossága, kapcsolattartási adatai és a meglévő támogatási jegyek kommunikációs frissítheti. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | A hozzáférési házirend törlése. |
> | Művelet | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | Hozzáférési házirend tulajdonságait olvassa be. |
> | Művelet | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | Létrehoz egy új hozzáférési házirendet egy olyan környezetben, vagy frissíti a meglévő hozzáférési házirendek. |
> | Művelet | Microsoft.TimeSeriesInsights/environments/delete | Törli a környezetben. |
> | Művelet | Microsoft.TimeSeriesInsights/environments/eventsources/delete | Törli a esemény forrását. |
> | Művelet | Microsoft.TimeSeriesInsights/environments/eventsources/eventsources/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Művelet | Microsoft.TimeSeriesInsights/environments/eventsources/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Művelet | Microsoft.TimeSeriesInsights/environments/eventsources/providers/Microsoft.Insights/metricDefinitions/read | A eventsources elérhető metrikai meghatározások beolvasása |
> | Művelet | Microsoft.TimeSeriesInsights/environments/eventsources/read | Egy eseményforrás tulajdonságait olvassa be. |
> | Művelet | Microsoft.TimeSeriesInsights/environments/eventsources/write | Létrehoz egy új eseményforrás a következőhöz egy olyan környezetben, vagy egy meglévő eseményforrás frissíti. |
> | Művelet | Microsoft.TimeSeriesInsights/environments/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Művelet | Microsoft.TimeSeriesInsights/environments/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Művelet | Microsoft.TimeSeriesInsights/environments/providers/Microsoft.Insights/metricDefinitions/read | A környezetek elérhető metrikai meghatározások beolvasása |
> | Művelet | Microsoft.TimeSeriesInsights/environments/read | A környezet tulajdonságait olvassa be. |
> | Művelet | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | Törli a referencia-adatkészlet. |
> | Művelet | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | A referencia-adatkészlet tulajdonságait olvassa be. |
> | Művelet | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | Létrehoz egy új referencia-adatkészlet környezet vagy frissít egy meglévő referencia-adatkészlet. |
> | Művelet | Microsoft.TimeSeriesInsights/environments/status/read | A környezet, a kapcsolódó műveleteket, például érkező állapotának állapotának beolvasása. |
> | Művelet | Microsoft.TimeSeriesInsights/environments/write | Létrehoz egy új környezetben, vagy frissíti a meglévő környezetben. |
> | Művelet | Microsoft.TimeSeriesInsights/register/action | Az idő adatsorozat Insights erőforrás-szolgáltató előfizetésének regisztrálása, és lehetővé teszi a idő adatsorozat Insights környezetek létrehozását. |

## <a name="microsoftvisualstudio"></a>Microsoft.visualstudio

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.VisualStudio/Account/Delete | Fiók törlése |
> | Művelet | Microsoft.VisualStudio/Account/Read | Olvassa el a fiókhoz |
> | Művelet | Microsoft.VisualStudio/Account/Write | Fiók beállítása |
> | Művelet | Microsoft.VisualStudio/Extension/Delete | Bővítmény törlése |
> | Művelet | Microsoft.VisualStudio/Extension/Read | Olvassa el a kiterjesztés |
> | Művelet | Microsoft.VisualStudio/Extension/Write | Virtuáliskapcsoló-kiterjesztés beállítása |
> | Művelet | Microsoft.VisualStudio/Project/Delete | Projekt törlése |
> | Művelet | Microsoft.VisualStudio/Project/Read | Olvasási projekt |
> | Művelet | Microsoft.VisualStudio/Project/Write | Beállíthatja a projekt |

## <a name="microsoftweb"></a>microsoft.web

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.Web/apimanagementaccounts/apiacls/Read | Api Management fiókok Apiacls beolvasása. |
> | Művelet | Microsoft.Web/apimanagementaccounts/APIs/apiacls/DELETE | Törli az Api Management fiókok API-k Apiacls. |
> | Művelet | Microsoft.Web/apimanagementaccounts/APIs/apiacls/Read | Api Management fiókok API-k Apiacls beolvasása. |
> | Művelet | Microsoft.Web/apimanagementaccounts/APIs/apiacls/Write | Frissítse az Api Management fiókok API-k Apiacls. |
> | Művelet | Microsoft.Web/apimanagementaccounts/APIs/connectionacls/Read | Api Management fiókok API-k Connectionacls beolvasása. |
> | Művelet | Microsoft.Web/apimanagementaccounts/APIs/Connections/confirmconsentcode/Action | Erősítse meg a hozzájárulási kód Api Management fiókok API-k kapcsolatok. |
> | Művelet | Microsoft.Web/apimanagementaccounts/APIs/Connections/connectionacls/DELETE | Törli az Api Management fiókok API-k kapcsolatok Connectionacls. |
> | Művelet | Microsoft.Web/apimanagementaccounts/APIs/Connections/connectionacls/Read | Api Management fiókok API-k kapcsolatok Connectionacls beolvasása. |
> | Művelet | Microsoft.Web/apimanagementaccounts/APIs/Connections/connectionacls/Write | Frissítse az Api Management fiókok API-k kapcsolatok Connectionacls. |
> | Művelet | Microsoft.Web/apimanagementaccounts/APIs/Connections/DELETE | Törli az Api Management fiókok API-k kapcsolatok. |
> | Művelet | Microsoft.Web/apimanagementaccounts/APIs/Connections/getconsentlinks/Action | Hozzájárulás hivatkozások Api Management fiókok API-k kapcsolatokhoz. |
> | Művelet | Microsoft.Web/apimanagementaccounts/APIs/Connections/listconnectionkeys/Action | Lista kapcsolat kulcsokat Api Management fiókok API-k kapcsolatok. |
> | Művelet | Microsoft.Web/apimanagementaccounts/APIs/Connections/listsecrets/Action | Lista titkok Api Management fiókok API-k kapcsolatok. |
> | Művelet | Microsoft.Web/apimanagementaccounts/APIs/Connections/Read | Lekérni az Api Management fiókok API-k kapcsolatokat. |
> | Művelet | Microsoft.Web/apimanagementaccounts/APIs/Connections/Write | Frissítse az Api Management fiókok API-k kapcsolatok. |
> | Művelet | Microsoft.Web/apimanagementaccounts/APIs/DELETE | Törli az Api Management fiókok API-k. |
> | Művelet | Microsoft.Web/apimanagementaccounts/APIs/localizeddefinitions/DELETE | Törli az Api Management fiókok API-k honosított definíciókat. |
> | Művelet | Microsoft.Web/apimanagementaccounts/APIs/localizeddefinitions/Read | Az Api Management beolvasása fiókok API-k honosított definíciókat. |
> | Művelet | Microsoft.Web/apimanagementaccounts/APIs/localizeddefinitions/Write | Frissítés Api Management fiókok API-k honosított definíciókat. |
> | Művelet | Microsoft.Web/apimanagementaccounts/APIs/Read | Az beszerzése Api fiókok API-val. |
> | Művelet | Microsoft.Web/apimanagementaccounts/APIs/Write | Frissítse az Api Management fiókok API-k. |
> | Művelet | Microsoft.Web/apimanagementaccounts/connectionacls/Read | Api Management fiókok Connectionacls beolvasása. |
> | Művelet | Microsoft.Web/availablestacks/Read | Rendelkezésre álló verem beolvasása. |
> | Művelet | Microsoft.Web/billingmeters/Read | Számlázási mérőszámok listájának beolvasása. |
> | Művelet | Microsoft.Web/certificates/Delete | Törölje a meglévő tanúsítványt. |
> | Művelet | Microsoft.Web/certificates/Read | A tanúsítványok listájának lekérdezése. |
> | Művelet | Microsoft.Web/certificates/Write | Adja hozzá az új tanúsítványt, vagy frissítsen egy meglévőt. |
> | Művelet | Microsoft.Web/checknameavailability/Read | Ellenőrizze, hogy az erőforrásnév érhető el. |
> | Művelet | Microsoft.Web/classicmobileservices/Read | Klasszikus mobilszolgáltatások beolvasása. |
> | Művelet | Microsoft.Web/connectionGateways/Delete | Egy kapcsolat átjáró törlése. |
> | Művelet | Microsoft.Web/connectionGateways/Join/Action | Egy kapcsolat átjáró csatlakozik. |
> | Művelet | Microsoft.Web/connectiongateways/liststatus/Action | Állapot kapcsolat átjáróit. |
> | Művelet | Microsoft.Web/connectionGateways/ListStatus/Action | Megadja a kapcsolat átjáró állapotát. |
> | Művelet | Microsoft.Web/connectionGateways/Move/Action | Egy kapcsolat átjáró helyezi. |
> | Művelet | Microsoft.Web/connectionGateways/Read | A kapcsolat átjárók listájának lekérdezése. |
> | Művelet | Microsoft.Web/connectionGateways/Write | Létrehozza vagy frissíti a kapcsolat átjáró. |
> | Művelet | Microsoft.Web/Connections/confirmconsentcode/Action | Kapcsolatok hozzájárulási kód megerősítése. |
> | Művelet | Microsoft.Web/connections/Delete | Törli a kapcsolatot. |
> | Művelet | Microsoft.Web/connections/Join/Action | A kapcsolat csatlakozik. |
> | Művelet | Microsoft.Web/Connections/listconsentlinks/Action | Hozzájárulás hivatkozások kapcsolatokhoz. |
> | Művelet | Microsoft.Web/connections/Move/Action | Egy kapcsolatot helyezi. |
> | Művelet | Microsoft.Web/connections/Read | Kapcsolatok listájának beolvasása. |
> | Művelet | Microsoft.Web/connections/Write | Létrehozza vagy frissíti a kapcsolatot. |
> | Művelet | Microsoft.Web/customApis/Delete | Törli az egyéni API. |
> | Művelet | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | API-definíció kiolvassa a egy WSDL. |
> | Művelet | Microsoft.Web/customApis/Join/Action | Egy egyéni API csatlakozik. |
> | Művelet | Microsoft.Web/customApis/listWsdlInterfaces/Action | Egy egyéni API felületek WSDL jeleníti meg. |
> | Művelet | Microsoft.Web/customApis/Move/Action | Egy egyéni API helyezi. |
> | Művelet | Microsoft.Web/customApis/Read | Egyéni API listájának. |
> | Művelet | Microsoft.Web/customApis/Write | Létrehozza vagy frissíti egy egyéni API-t. |
> | Művelet | Microsoft.Web/deploymentlocations/Read | Beolvasni a központi telepítési helyét. |
> | Művelet | Microsoft.Web/geoRegions/Read | A földrajzi régiók között listájának lekérdezése. |
> | Művelet | Microsoft.Web/hostingenvironments/capacities/Read | Az üzemeltetési környezetek kapacitások beolvasása. |
> | Művelet | Microsoft.Web/hostingEnvironments/Delete | App Service-környezet törlése |
> | Művelet | Microsoft.Web/hostingenvironments/Diagnostics/Read | Első üzemeltetési környezetekben diagnosztika. |
> | Művelet | Microsoft.Web/hostingenvironments/inboundnetworkdependenciesendpoints/Read | Az összes bejövő függősége a hálózati végpont beolvasása. |
> | Művelet | Microsoft.Web/hostingenvironments/metricdefinitions/Read | Az üzemeltetési környezetek metrikai meghatározásainak beolvasása. |
> | Művelet | Microsoft.Web/hostingenvironments/multirolepools/metricdefinitions/Read | Az üzemeltetési környezetek többcélú készletek metrikai meghatározásainak beolvasása. |
> | Művelet | Microsoft.Web/hostingenvironments/multirolepools/Metrics/Read | Első üzemeltetési környezetekben többcélú készletek metrikákat. |
> | Művelet | Microsoft.Web/hostingEnvironments/multiRolePools/providers/Microsoft.Insights/metricDefinitions/Read | Az App Service-környezet MultiRole elérhető metrikai meghatározások beolvasása |
> | Művelet | Microsoft.Web/hostingEnvironments/multiRolePools/Read | Egy App Service Environment-környezetben egy előtér-címkészlet tulajdonságainak beolvasása |
> | Művelet | Microsoft.Web/hostingenvironments/multirolepools/skus/Read | Első üzemeltetési környezetekben többcélú készletek SKU. |
> | Művelet | Microsoft.Web/hostingenvironments/multirolepools/usages/Read | Első üzemeltetési környezetekben többcélú készletek is érvényesek. |
> | Művelet | Microsoft.Web/hostingEnvironments/multiRolePools/Write | Előtér-készlet létrehozása az App Service-környezetben, vagy egy meglévő frissítése |
> | Művelet | Microsoft.Web/hostingenvironments/Operations/Read | Első üzemeltetési környezetekben műveletek. |
> | Művelet | Microsoft.Web/hostingenvironments/outboundnetworkdependenciesendpoints/Read | A hálózati végpont minden kimenő függőségi beolvasása. |
> | Művelet | microsoft.web/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Művelet | microsoft.web/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Művelet | Microsoft.Web/hostingEnvironments/Read | Az App Service-környezetek tulajdonságainak beolvasása |
> | Művelet | Microsoft.Web/hostingEnvironments/reboot/Action | Indítsa újra a egy App Service-környezetben lévő összes gépen |
> | Művelet | Microsoft.Web/hostingenvironments/Resume/Action | Végezze el újra üzemeltetési környezetekben. |
> | Művelet | Microsoft.Web/hostingenvironments/serverfarms/Read | Első üzemeltetési környezetekben App Service-csomagokról. |
> | Művelet | Microsoft.Web/hostingenvironments/Sites/Read | Első környezetek webalkalmazások üzemeltetéséhez. |
> | Művelet | Microsoft.Web/hostingenvironments/suspend/Action | Felfüggesztheti üzemeltetési környezetekben. |
> | Művelet | Microsoft.Web/hostingenvironments/usages/Read | Első üzemeltetési környezetekben is érvényesek. |
> | Művelet | Microsoft.Web/hostingenvironments/workerpools/metricdefinitions/Read | Első üzemeltetési környezetekben Workerpools metrika definíciókat. |
> | Művelet | Microsoft.Web/hostingenvironments/workerpools/Metrics/Read | Első üzemeltetési környezetekben Workerpools metrikákat. |
> | Művelet | Microsoft.Web/hostingEnvironments/workerPools/providers/Microsoft.Insights/metricDefinitions/Read | Az App Service-környezet WorkerPool elérhető metrikai meghatározások beolvasása |
> | Művelet | Microsoft.Web/hostingEnvironments/workerPools/Read | A Feldolgozókészleten egy App Service Environment-környezetben tulajdonságainak beolvasása |
> | Művelet | Microsoft.Web/hostingenvironments/workerpools/skus/Read | Első üzemeltetési környezetekben Workerpools SKU. |
> | Művelet | Microsoft.Web/hostingenvironments/workerpools/usages/Read | Első üzemeltetési környezetekben Workerpools módjait. |
> | Művelet | Microsoft.Web/hostingEnvironments/workerPools/Write | Hozzon létre egy új Feldolgozókészletek egy App Service Environment-környezetben, vagy egy meglévő frissítése |
> | Művelet | Microsoft.Web/hostingEnvironments/Write | Egy új App Service Environment-környezet létrehozása vagy meglévő ütemezés frissítése |
> | Művelet | Microsoft.Web/ishostingenvironmentnameavailable/Read | GET, ha üzemeltetési környezet neve érhető el. |
> | Művelet | Microsoft.Web/ishostnameavailable/Read | Ellenőrizze, hogy az állomásnév érhető el. |
> | Művelet | Microsoft.Web/isusernameavailable/Read | Ellenőrizze, hogy a felhasználónév érhető el. |
> | Művelet | Microsoft.Web/listSitesAssignedToHostName/Read | Lekérése az állomásnév rendelt helyek nevét. |
> | Művelet | Microsoft.Web/Locations/apioperations/Read | Helyek API műveleteinek beolvasása. |
> | Művelet | Microsoft.Web/Locations/connectiongatewayinstallations/Read | Helyek kapcsolat átjáró telepítések beolvasása. |
> | Művelet | Microsoft.Web/Locations/extractapidefinitionfromwsdl/Action | Api-definíció kinyerése WSDL helyeket. |
> | Művelet | Microsoft.Web/Locations/listwsdlinterfaces/Action | Lista WSDL-felületek helyeket. |
> | Művelet | Microsoft.Web/Locations/managedapis/apioperations/Read | Helyek felügyelt API-műveletek beolvasása. |
> | Művelet | Microsoft.Web/locations/managedapis/Join/Action | A felügyelt API csatlakozik. |
> | Művelet | Microsoft.Web/Locations/managedapis/Read | Helyek felügyelt API-k beolvasása. |
> | Művelet | Microsoft.Web/Operations/Read | Műveletek beolvasása. |
> | Művelet | Microsoft.Web/publishingusers/Read | Első közzététel számára. |
> | Művelet | Microsoft.Web/publishingusers/Write | A frissítés közzététele a felhasználók. |
> | Művelet | Microsoft.Web/recommendations/Read | Az előfizetések javaslatok listájának. |
> | Művelet | Microsoft.Web/register/Action | Az előfizetés Microsoft.Web erőforrás-szolgáltató regisztrálása. |
> | Művelet | Microsoft.Web/resourcehealthmetadata/Read | Erőforrás állapota metaadatot beszerezni. |
> | Művelet | Microsoft.Web/serverfarms/Capabilities/Read | Az alkalmazásszolgáltatási csomagok lehetőségek elérése. |
> | Művelet | Microsoft.Web/serverfarms/Delete | Egy meglévő App Service-csomag törlése |
> | Művelet | Microsoft.Web/serverfarms/firstpartyapps/Settings/DELETE | Törli az App Service csomagokban első entitás alkalmazásokra vonatkozó beállítások. |
> | Művelet | Microsoft.Web/serverfarms/firstpartyapps/Settings/Read | Az alkalmazásszolgáltatási csomagok első entitás alkalmazások beállításainak beolvasása. |
> | Művelet | Microsoft.Web/serverfarms/firstpartyapps/Settings/Write | Frissítés App Service-csomagok első entitás alkalmazásokra vonatkozó beállítások. |
> | Művelet | Microsoft.Web/serverfarms/hybridconnectionnamespaces/relays/DELETE | Az alkalmazásszolgáltatási csomagok hibrid kapcsolat névterek továbbítók törlése. |
> | Művelet | Microsoft.Web/serverfarms/hybridconnectionnamespaces/relays/Read | Az alkalmazásszolgáltatási csomagok hibrid kapcsolat névterek továbbítók beolvasása. |
> | Művelet | Microsoft.Web/serverfarms/hybridconnectionnamespaces/relays/Sites/Read | Az App Service csomagokban hibrid kapcsolat névterek továbbítók webes alkalmazások beszerzéséhez. |
> | Művelet | Microsoft.Web/serverfarms/hybridconnectionplanlimits/Read | Az alkalmazásszolgáltatási csomagok hibrid kapcsolat terv korlátok beolvasása. |
> | Művelet | Microsoft.Web/serverfarms/hybridconnectionrelays/Read | Az alkalmazásszolgáltatási csomagok hibrid kapcsolat továbbítók beolvasása. |
> | Művelet | Microsoft.Web/serverfarms/metricdefinitions/Read | Az alkalmazásszolgáltatási csomagok metrikai meghatározásainak beolvasása. |
> | Művelet | Microsoft.Web/serverfarms/Metrics/Read | Az alkalmazásszolgáltatási csomagok metrikákat kaphat. |
> | Művelet | Microsoft.Web/serverfarms/operationresults/Read | Az alkalmazásszolgáltatási csomagok művelet eredményt. |
> | Művelet | microsoft.web/serverfarms/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Művelet | microsoft.web/serverfarms/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Művelet | Microsoft.Web/serverfarms/providers/Microsoft.Insights/metricDefinitions/Read | App Service-csomag elérhető metrikai meghatározások beolvasása |
> | Művelet | Microsoft.Web/serverfarms/Read | Az egy App Service-csomag tulajdonságainak beolvasása |
> | Művelet | Microsoft.Web/serverfarms/restartSites/Action | Indítsa újra az összes webes alkalmazás az App Service-csomag |
> | Művelet | Microsoft.Web/serverfarms/Sites/Read | Az App Service csomagokban webes alkalmazások beszerzéséhez. |
> | Művelet | Microsoft.Web/serverfarms/skus/Read | Az alkalmazásszolgáltatási csomagok termékváltozatok beolvasása. |
> | Művelet | Microsoft.Web/serverfarms/usages/Read | Az alkalmazásszolgáltatási csomagok módjait beolvasása. |
> | Művelet | Microsoft.Web/serverfarms/virtualnetworkconnections/Gateways/Write | Az App Service csomagokban virtuális hálózati kapcsolatok átjárók frissítése. |
> | Művelet | Microsoft.Web/serverfarms/virtualnetworkconnections/Read | Az alkalmazásszolgáltatási csomagok virtuális hálózati kapcsolatok lekérése. |
> | Művelet | Microsoft.Web/serverfarms/virtualnetworkconnections/routes/DELETE | Az App Service csomagokban virtuális hálózati kapcsolatok útvonalak törlése. |
> | Művelet | Microsoft.Web/serverfarms/virtualnetworkconnections/routes/Read | Az App Service csomagokban virtuális hálózati kapcsolatok útvonalak beolvasása. |
> | Művelet | Microsoft.Web/serverfarms/virtualnetworkconnections/routes/Write | Az alkalmazásszolgáltatási csomagok virtuális hálózati kapcsolatok az útvonalak frissítése. |
> | Művelet | Microsoft.Web/serverfarms/workers/reboot/Action | Indítsa újra az App Service csomagokban munkavállalók. |
> | Művelet | Microsoft.Web/serverfarms/Write | Egy új App Service-csomag létrehozása vagy meglévő pillanatkép frissítése |
> | Művelet | Microsoft.Web/Sites/analyzecustomhostname/Read | Elemezze az egyéni állomásnevet. |
> | Művelet | Microsoft.Web/sites/applySlotConfig/Action | Webes alkalmazás tárolóhely konfigurációt cél tárolóhelyről az aktuális webes alkalmazás |
> | Művelet | Microsoft.Web/sites/backup/Action | Hozzon létre egy új webes alkalmazás biztonsági mentése |
> | Művelet | Microsoft.Web/Sites/Backup/Read | Web Apps biztonsági mentés beolvasása. |
> | Művelet | Microsoft.Web/Sites/Backup/Write | Web Apps másolat frissítéséhez. |
> | Művelet | Microsoft.Web/Sites/backups/DELETE | Delete Web Apps Backups. |
> | Művelet | Microsoft.Web/Sites/backups/List/Action | Lista Web Apps biztonsági mentéseket. |
> | Művelet | Microsoft.Web/sites/backups/Read | A webalkalmazás biztonsági mentése tulajdonságainak beolvasása |
> | Művelet | Microsoft.Web/Sites/backups/restore/Action | Állítsa vissza a Web Apps biztonsági mentéseket. |
> | Művelet | Microsoft.Web/Sites/config/DELETE | Delete Web Apps Config. |
> | Művelet | Microsoft.Web/sites/config/list/Action | Webes alkalmazás biztonsági bizalmas beállítások, például a hitelesítő adatokat, az alkalmazásbeállítások és az kapcsolati karakterláncok |
> | Művelet | Microsoft.Web/sites/config/Read | A webalkalmazás konfigurációs beállításainak beolvasása |
> | Művelet | Microsoft.Web/sites/config/Write | Webes alkalmazás konfigurációs beállításainak frissítése |
> | Művelet | Microsoft.Web/Sites/continuouswebjobs/DELETE | Delete Web Apps Continuous Web Jobs. |
> | Művelet | Microsoft.Web/Sites/continuouswebjobs/Read | Webes alkalmazások folyamatos webes feladatok beolvasása. |
> | Művelet | Microsoft.Web/Sites/continuouswebjobs/Start/Action | Indítsa el a webes alkalmazások folyamatos webes feladatok. |
> | Művelet | Microsoft.Web/Sites/continuouswebjobs/STOP/Action | Állítsa le a webes alkalmazások folyamatos webes feladatok. |
> | Művelet | Microsoft.Web/sites/Delete | Egy már meglévő webalkalmazás törlése |
> | Művelet | Microsoft.Web/Sites/Deployments/DELETE | Webes alkalmazások központi telepítéseit törli. |
> | Művelet | Microsoft.Web/Sites/Deployments/log/Read | Webes alkalmazások központi telepítésének napló beolvasása. |
> | Művelet | Microsoft.Web/Sites/Deployments/Read | Webes alkalmazások központi telepítésének beolvasása. |
> | Művelet | Microsoft.Web/Sites/Deployments/Write | Webes alkalmazások központi telepítések frissítése. |
> | Művelet | microsoft.web/sites/diagnostics/analyses/execute/Action | Webes alkalmazások diagnosztika elemzés futtatása. |
> | Művelet | Microsoft.Web/Sites/Diagnostics/analyses/Read | Web Apps diagnosztika elemzés beolvasása. |
> | Művelet | Microsoft.Web/Sites/Diagnostics/aspnetcore/Read | Az ASP.NET Core app a Web Apps diagnosztika beolvasása. |
> | Művelet | Microsoft.Web/Sites/Diagnostics/autoheal/Read | Web Apps diagnosztika elindulásáról beolvasása. |
> | Művelet | Microsoft.Web/Sites/Diagnostics/Deployment/Read | A webes alkalmazások Diagnostics telepítése beolvasása. |
> | Művelet | Microsoft.Web/Sites/Diagnostics/Deployments/Read | Web Apps diagnosztika központi telepítések beolvasása. |
> | Művelet | microsoft.web/sites/diagnostics/detectors/execute/Action | Futtassa a Web Apps diagnosztika érzékelő. |
> | Művelet | Microsoft.Web/Sites/Diagnostics/detectors/Read | Web Apps diagnosztika érzékelő beolvasása. |
> | Művelet | Microsoft.Web/Sites/Diagnostics/failedrequestsperuri/Read | Webes alkalmazások diagnosztika a sikertelen kérelmek / Uri beolvasása. |
> | Művelet | Microsoft.Web/Sites/Diagnostics/frebanalysis/Read | Web Apps diagnosztika FREB elemzés beolvasása. |
> | Művelet | Microsoft.Web/Sites/Diagnostics/loganalyzer/Read | Web Apps diagnosztikai naplófájl Analyzer beolvasása. |
> | Művelet | Microsoft.Web/Sites/Diagnostics/Read | Web Apps diagnosztika kategóriák beolvasása. |
> | Művelet | Microsoft.Web/Sites/Diagnostics/runtimeavailability/Read | Web Apps diagnosztika futásidejű rendelkezésre beolvasása. |
> | Művelet | Microsoft.Web/Sites/Diagnostics/servicehealth/Read | Webes alkalmazások diagnosztika szolgáltatás állapotának beolvasása. |
> | Művelet | Microsoft.Web/Sites/Diagnostics/sitecpuanalysis/Read | Web Apps diagnosztika hely CPU elemzés beolvasása. |
> | Művelet | Microsoft.Web/Sites/Diagnostics/sitecrashes/Read | Web Apps diagnosztika hely összeomlások beolvasása. |
> | Művelet | Microsoft.Web/Sites/Diagnostics/sitelatency/Read | Web Apps diagnosztika hely késés beolvasása. |
> | Művelet | Microsoft.Web/Sites/Diagnostics/sitememoryanalysis/Read | Web Apps diagnosztika hely memória elemzés beolvasása. |
> | Művelet | Microsoft.Web/Sites/Diagnostics/siterestartsettingupdate/Read | Web Apps diagnosztika hely újraindítás beállítás frissítésének letöltése. |
> | Művelet | Microsoft.Web/Sites/Diagnostics/siterestartuserinitiated/Read | Web Apps diagnosztika hely újraindítás felhasználó által kezdeményezett beolvasása. |
> | Művelet | Microsoft.Web/Sites/Diagnostics/siteswap/Read | Web Apps diagnosztika hely Swap beolvasása. |
> | Művelet | Microsoft.Web/Sites/Diagnostics/THREADCOUNT/Read | Web Apps diagnosztika szálak számának beolvasása. |
> | Művelet | Microsoft.Web/Sites/Diagnostics/workeravailability/Read | Web Apps diagnosztika Workeravailability beolvasása. |
> | Művelet | Microsoft.Web/Sites/Diagnostics/workerprocessrecycle/Read | Webes alkalmazások diagnosztika munkavégző folyamat újrahasznosítást beolvasása. |
> | Művelet | Microsoft.Web/Sites/domainownershipidentifiers/Read | Megkapja a Web Apps tartományi tulajdonjoga azonosítókat. |
> | Művelet | Microsoft.Web/Sites/domainownershipidentifiers/Write | Frissítse a Web Apps tartományi tulajdonjoga azonosítók. |
> | Művelet | Microsoft.Web/Sites/Functions/Action | Webalkalmazások funkciók. |
> | Művelet | Microsoft.Web/Sites/Functions/DELETE | Törölje a Web Apps funkciók. |
> | Művelet | Microsoft.Web/Sites/Functions/listsecrets/Action | Lista titkok Web Apps funkciók. |
> | Művelet | Microsoft.Web/Sites/Functions/masterkey/Read | Web Apps funkciók főkulcsos beolvasása. |
> | Művelet | Microsoft.Web/Sites/Functions/Read | Web Apps funkciók beolvasása. |
> | Művelet | Microsoft.Web/Sites/Functions/token/Read | Get Web Apps funkciók jogkivonat. |
> | Művelet | Microsoft.Web/Sites/Functions/Write | Frissítse a Web Apps funkciók. |
> | Művelet | Microsoft.Web/Sites/hostnamebindings/DELETE | Delete Web Apps Hostname Bindings. |
> | Művelet | Microsoft.Web/Sites/hostnamebindings/Read | Get Web Apps Hostname Bindings. |
> | Művelet | Microsoft.Web/Sites/hostnamebindings/Write | Update Web Apps Hostname Bindings. |
> | Művelet | Microsoft.Web/Sites/hybridconnection/DELETE | Web Apps hibrid kapcsolat törlése. |
> | Művelet | Microsoft.Web/Sites/hybridconnection/Read | Web Apps a hibrid kapcsolat beolvasása. |
> | Művelet | Microsoft.Web/Sites/hybridconnection/Write | Web Apps hibrid kapcsolat frissítése. |
> | Művelet | Microsoft.Web/Sites/hybridconnectionnamespaces/relays/DELETE | Törölje a Web Apps hibrid kapcsolat névterek továbbítók. |
> | Művelet | Microsoft.Web/Sites/hybridconnectionnamespaces/relays/listkeys/Action | Listában kulcsok Web Apps hibrid kapcsolat névterek továbbítók. |
> | Művelet | Microsoft.Web/Sites/hybridconnectionnamespaces/relays/Read | Web Apps hibrid kapcsolat névterek továbbítók beolvasása. |
> | Művelet | Microsoft.Web/Sites/hybridconnectionnamespaces/relays/Write | Frissítse a Web Apps hibrid kapcsolat névterek továbbítók. |
> | Művelet | Microsoft.Web/Sites/hybridconnectionrelays/Read | Web Apps hibrid kapcsolat továbbítók beolvasása. |
> | Művelet | Microsoft.Web/Sites/Instances/Deployments/DELETE | Törölje a Web Apps példányok központi telepítéseket. |
> | Művelet | Microsoft.Web/Sites/Instances/Deployments/Read | Web Apps példányok központi telepítések beolvasása. |
> | Művelet | Microsoft.Web/Sites/Instances/Extensions/log/Read | Web Apps példányok bővítmények napló beolvasása. |
> | Művelet | Microsoft.Web/Sites/Instances/Extensions/Read | Web Apps példányok kiterjesztések beolvasása. |
> | Művelet | Microsoft.Web/Sites/Instances/Processes/DELETE | Törölje a Web Apps példányok folyamatokat. |
> | Művelet | Microsoft.Web/Sites/Instances/Processes/Read | Web Apps példányok folyamatok beolvasása. |
> | Művelet | Microsoft.Web/Sites/Instances/Read | Web Apps-példányokat beszerezni. |
> | Művelet | Microsoft.Web/Sites/listsyncfunctiontriggerstatus/Action | Lista szinkronizálási függvény eseményindító állapot webalkalmazások. |
> | Művelet | Microsoft.Web/Sites/metricdefinitions/Read | Web Apps metrika meghatározások beolvasása. |
> | Művelet | Microsoft.Web/Sites/Metrics/Read | Web Apps metrikákat kaphat. |
> | Művelet | Microsoft.Web/Sites/metricsdefinitions/Read | Web Apps metrikák meghatározások beolvasása. |
> | Művelet | Microsoft.Web/Sites/migratemysql/Action | MySql Web Apps alkalmazások áttelepítése. |
> | Művelet | Microsoft.Web/Sites/migratemysql/Read | Első Web Apps alkalmazások áttelepítése a MySql. |
> | Művelet | Microsoft.Web/Sites/networktrace/Action | Hálózati nyomkövetés webalkalmazások. |
> | Művelet | Microsoft.Web/Sites/newpassword/Action | Newpassword Web Apps. |
> | Művelet | Microsoft.Web/Sites/operationresults/Read | Web Apps művelet eredményt. |
> | Művelet | Microsoft.Web/Sites/Operations/Read | Web Apps műveleteinek beolvasása. |
> | Művelet | Microsoft.Web/Sites/perfcounters/Read | Web Apps teljesítményszámlálók beolvasása. |
> | Művelet | Microsoft.Web/Sites/premieraddons/DELETE | Törölje a Web Apps Premier bővítményei. |
> | Művelet | Microsoft.Web/Sites/premieraddons/Read | Web Apps Premier bővítményei beolvasása. |
> | Művelet | Microsoft.Web/Sites/premieraddons/Write | Frissítse a Web Apps Premier bővítményei. |
> | Művelet | Microsoft.Web/Sites/Processes/Read | Web Apps folyamatok beolvasása. |
> | Művelet | microsoft.web/sites/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Művelet | microsoft.web/sites/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Művelet | Microsoft.Web/sites/providers/Microsoft.Insights/metricDefinitions/Read | Webalkalmazás elérhető metrikai meghatározások beolvasása |
> | Művelet | Microsoft.Web/Sites/publiccertificates/DELETE | Törli a Web Apps nyilvános tanúsítványokat. |
> | Művelet | Microsoft.Web/Sites/publiccertificates/Read | Web Apps nyilvános tanúsítványokat le. |
> | Művelet | Microsoft.Web/Sites/publiccertificates/Write | Web Apps nyilvános tanúsítványok frissítése. |
> | Művelet | Microsoft.Web/sites/publish/Action | A webes alkalmazás közzététele |
> | Művelet | Microsoft.Web/sites/publishxml/Action | Közzétételi profil xml egy webalkalmazást az beszerzése |
> | Művelet | Microsoft.Web/Sites/publishxml/Read | Kérhető le a webes közzétételi XML. |
> | Művelet | Microsoft.Web/sites/Read | A webes alkalmazás tulajdonságainak beolvasása |
> | Művelet | Microsoft.Web/Sites/recommendationhistory/Read | Webes alkalmazások javaslat előzmények beolvasása. |
> | Művelet | Microsoft.Web/Sites/recommendations/disable/Action | Tiltsa le a Web Apps javaslatokat. |
> | Művelet | Microsoft.Web/sites/recommendations/Read | A webalkalmazás javaslatok listája az beszerzése. |
> | Művelet | Microsoft.Web/Sites/RECOVER/Action | Recover Web Apps. |
> | Művelet | Microsoft.Web/sites/resetSlotConfig/Action | Alaphelyzetbe állítja a webes alkalmazás konfigurálása |
> | Művelet | Microsoft.Web/Sites/resourcehealthmetadata/Read | Web Apps erőforrás állapotának metaadatot beszerezni. |
> | Művelet | Microsoft.Web/sites/restart/Action | A webalkalmazás újraindítása |
> | Művelet | Microsoft.Web/Sites/restore/Read | Web Apps visszaállítási beolvasása. |
> | Művelet | Microsoft.Web/Sites/restore/Write | Állítsa vissza a webalkalmazásokat. |
> | Művelet | Microsoft.Web/Sites/restoresnapshot/Action | Állítson vissza egy Web Apps pillanatképet. |
> | Művelet | Microsoft.Web/Sites/siteextensions/DELETE | Törölje a Web Apps hely bővítmények. |
> | Művelet | Microsoft.Web/Sites/siteextensions/Read | Web Apps helyhez kiterjesztések beolvasása. |
> | Művelet | Microsoft.Web/Sites/siteextensions/Write | Web Apps hely bővítmények frissítése. |
> | Művelet | Microsoft.Web/Sites/slots/analyzecustomhostname/Read | Első webes alkalmazások üzembe helyezési ponti elemezheti az egyéni állomásnevet. |
> | Művelet | Microsoft.Web/sites/slots/applySlotConfig/Action | Webes alkalmazás tárolóhely konfiguráció cél tárolóhelyről az aktuális tárolóhelyre alkalmazásához. |
> | Művelet | Microsoft.Web/sites/slots/backup/Action | Hozzon létre új webalkalmazás biztonsági mentése. |
> | Művelet | Microsoft.Web/Sites/slots/Backup/Read | Webes alkalmazások üzembe helyezési ponti biztonsági mentés beolvasása. |
> | Művelet | Microsoft.Web/Sites/slots/Backup/Write | Webes alkalmazások üzembe helyezési ponti másolat frissítéséhez. |
> | Művelet | Microsoft.Web/Sites/slots/backups/DELETE | Törölje a webes alkalmazások üzembe helyezési ponti biztonsági mentéseket. |
> | Művelet | Microsoft.Web/Sites/slots/backups/List/Action | Lista webes alkalmazások üzembe helyezési ponti biztonsági mentéseket. |
> | Művelet | Microsoft.Web/sites/slots/backups/Read | A webalkalmazás tárolóhelyei biztonsági másolat tulajdonságainak beolvasása |
> | Művelet | Microsoft.Web/Sites/slots/backups/restore/Action | Állítsa vissza a webes alkalmazások üzembe helyezési ponti biztonsági mentéseket. |
> | Művelet | Microsoft.Web/Sites/slots/config/DELETE | Webes alkalmazások üzembe helyezési ponti konfiguráció törlése. |
> | Művelet | Microsoft.Web/sites/slots/config/list/Action | Webes alkalmazás a tárhely biztonsági bizalmas beállítások, például a hitelesítő adatokat, az alkalmazásbeállítások és az kapcsolati karakterláncok |
> | Művelet | Microsoft.Web/sites/slots/config/Read | Webes alkalmazás a tárhely konfigurációs beállításainak beolvasása |
> | Művelet | Microsoft.Web/sites/slots/config/Write | Webes alkalmazás a tárhely konfigurációs beállításainak frissítése |
> | Művelet | Microsoft.Web/Sites/slots/continuouswebjobs/DELETE | Webes alkalmazások üzembe helyezési ponti folyamatos webes feladatok törlése. |
> | Művelet | Microsoft.Web/Sites/slots/continuouswebjobs/Read | Webes alkalmazások üzembe helyezési ponti folyamatos webes feladatok beolvasása. |
> | Művelet | Microsoft.Web/Sites/slots/continuouswebjobs/Start/Action | Indítsa el a webes alkalmazások üzembe helyezési ponti folyamatos webes feladatok. |
> | Művelet | Microsoft.Web/Sites/slots/continuouswebjobs/STOP/Action | Állítsa le a webes alkalmazások üzembe helyezési ponti folyamatos webes feladatok. |
> | Művelet | Microsoft.Web/sites/slots/Delete | Törölje a meglévő Web App tárhelyek |
> | Művelet | Microsoft.Web/Sites/slots/Deployments/DELETE | Törölje a webes alkalmazások üzembe helyezési ponti központi telepítések. |
> | Művelet | Microsoft.Web/Sites/slots/Deployments/log/Read | Webes alkalmazások üzembe helyezési ponti központi telepítések napló beolvasása. |
> | Művelet | Microsoft.Web/Sites/slots/Deployments/Read | Webes alkalmazások üzembe helyezési ponti központi telepítések beolvasása. |
> | Művelet | Microsoft.Web/Sites/slots/Deployments/Write | Webes alkalmazások üzembe helyezési ponti központi telepítések frissítése. |
> | Művelet | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | Webes alkalmazások üzembe helyezési ponti diagnosztika elemzés futtatása. |
> | Művelet | Microsoft.Web/Sites/slots/Diagnostics/analyses/Read | Webes alkalmazások üzembe helyezési ponti diagnosztika elemzés beolvasása. |
> | Művelet | Microsoft.Web/Sites/slots/Diagnostics/aspnetcore/Read | Az ASP.NET Core app a webes alkalmazások üzembe helyezési ponti diagnosztika beolvasása. |
> | Művelet | Microsoft.Web/Sites/slots/Diagnostics/autoheal/Read | Webes alkalmazások üzembe helyezési ponti diagnosztika elindulásáról beolvasása. |
> | Művelet | Microsoft.Web/Sites/slots/Diagnostics/Deployment/Read | A webes alkalmazások üzembe helyezési ponti Diagnostics telepítése beolvasása. |
> | Művelet | Microsoft.Web/Sites/slots/Diagnostics/Deployments/Read | Webes alkalmazások üzembe helyezési ponti diagnosztika központi telepítések beolvasása. |
> | Művelet | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | Futtassa a webes alkalmazások üzembe helyezési ponti diagnosztika érzékelő. |
> | Művelet | Microsoft.Web/Sites/slots/Diagnostics/detectors/Read | Webes alkalmazások üzembe helyezési ponti diagnosztika érzékelő beolvasása. |
> | Művelet | Microsoft.Web/Sites/slots/Diagnostics/frebanalysis/Read | Webes alkalmazások üzembe helyezési ponti diagnosztika FREB elemzés beolvasása. |
> | Művelet | Microsoft.Web/Sites/slots/Diagnostics/loganalyzer/Read | Webes alkalmazások üzembe helyezési ponti diagnosztikai naplófájl Analyzer beolvasása. |
> | Művelet | Microsoft.Web/Sites/slots/Diagnostics/Read | Webes alkalmazások üzembe helyezési ponti diagnosztika beolvasása. |
> | Művelet | Microsoft.Web/Sites/slots/Diagnostics/runtimeavailability/Read | Webes alkalmazások üzembe helyezési ponti diagnosztika futásidejű rendelkezésre beolvasása. |
> | Művelet | Microsoft.Web/Sites/slots/Diagnostics/servicehealth/Read | Első webes alkalmazások üzembe helyezési ponti diagnosztika szolgáltatás állapotát. |
> | Művelet | Microsoft.Web/Sites/slots/Diagnostics/sitecpuanalysis/Read | Webes alkalmazások üzembe helyezési ponti diagnosztika hely CPU elemzés beolvasása. |
> | Művelet | Microsoft.Web/Sites/slots/Diagnostics/sitecrashes/Read | Webes alkalmazások üzembe helyezési ponti diagnosztika hely összeomlások beolvasása. |
> | Művelet | Microsoft.Web/Sites/slots/Diagnostics/sitelatency/Read | Webes alkalmazások üzembe helyezési ponti diagnosztika hely késés beolvasása. |
> | Művelet | Microsoft.Web/Sites/slots/Diagnostics/sitememoryanalysis/Read | Webes alkalmazások üzembe helyezési ponti diagnosztika hely memória elemzés beolvasása. |
> | Művelet | Microsoft.Web/Sites/slots/Diagnostics/siterestartsettingupdate/Read | Webes alkalmazások üzembe helyezési ponti diagnosztika hely újraindítás beállítás frissítésének letöltése. |
> | Művelet | Microsoft.Web/Sites/slots/Diagnostics/siterestartuserinitiated/Read | Webes alkalmazások üzembe helyezési ponti diagnosztika webhely újraindítását felhasználó által kezdeményezett beolvasása. |
> | Művelet | Microsoft.Web/Sites/slots/Diagnostics/siteswap/Read | Webes alkalmazások üzembe helyezési ponti diagnosztika hely Swap beolvasása. |
> | Művelet | Microsoft.Web/Sites/slots/Diagnostics/THREADCOUNT/Read | Webes alkalmazások üzembe helyezési ponti diagnosztika szálak számának beolvasása. |
> | Művelet | Microsoft.Web/Sites/slots/Diagnostics/workeravailability/Read | Webes alkalmazások üzembe helyezési ponti diagnosztika Workeravailability beolvasása. |
> | Művelet | Microsoft.Web/Sites/slots/Diagnostics/workerprocessrecycle/Read | Webes alkalmazások üzembe helyezési ponti diagnosztika munkavégző folyamat újrahasznosítást beolvasása. |
> | Művelet | Microsoft.Web/Sites/slots/domainownershipidentifiers/Read | Megkapja a webes alkalmazások üzembe helyezési ponti tartomány tulajdonjoga azonosítókat. |
> | Művelet | Microsoft.Web/Sites/slots/hostnamebindings/DELETE | Delete Web Apps Slots Hostname Bindings. |
> | Művelet | Microsoft.Web/Sites/slots/hostnamebindings/Read | Get Web Apps Slots Hostname Bindings. |
> | Művelet | Microsoft.Web/Sites/slots/hostnamebindings/Write | Update Web Apps Slots Hostname Bindings. |
> | Művelet | Microsoft.Web/Sites/slots/hybridconnection/DELETE | Webes alkalmazások üzembe helyezési ponti hibrid kapcsolat törlése. |
> | Művelet | Microsoft.Web/Sites/slots/hybridconnection/Read | Webes alkalmazások üzembe helyezési ponti hibrid kapcsolat lekérdezése. |
> | Művelet | Microsoft.Web/Sites/slots/hybridconnection/Write | Webes alkalmazások üzembe helyezési ponti hibrid kapcsolat frissítése. |
> | Művelet | Microsoft.Web/Sites/slots/hybridconnectionnamespaces/relays/DELETE | Webes alkalmazások üzembe helyezési ponti hibrid kapcsolat névterek továbbítók törlése. |
> | Művelet | Microsoft.Web/Sites/slots/hybridconnectionnamespaces/relays/Write | Frissítse a webes alkalmazások üzembe helyezési ponti hibrid kapcsolat névterek továbbítók. |
> | Művelet | Microsoft.Web/Sites/slots/hybridconnectionrelays/Read | Webes alkalmazások üzembe helyezési ponti hibrid kapcsolat továbbítók beolvasása. |
> | Művelet | Microsoft.Web/Sites/slots/Instances/Deployments/Read | Webes alkalmazások üzembe helyezési ponti példányok központi telepítések beolvasása. |
> | Művelet | Microsoft.Web/Sites/slots/Instances/Processes/DELETE | Törölje a webes alkalmazások üzembe helyezési ponti példányok folyamatokat. |
> | Művelet | Microsoft.Web/Sites/slots/Instances/Processes/Read | Webes alkalmazások üzembe helyezési ponti példányok folyamatok beolvasása. |
> | Művelet | Microsoft.Web/Sites/slots/Instances/Read | Webes alkalmazások üzembe helyezési ponti-példányokat beszerezni. |
> | Művelet | Microsoft.Web/Sites/slots/metricdefinitions/Read | Webes alkalmazások üzembe helyezési ponti metrikai meghatározásainak beolvasása. |
> | Művelet | Microsoft.Web/Sites/slots/Metrics/Read | Webes alkalmazások üzembe helyezési ponti metrikákat kaphat. |
> | Művelet | Microsoft.Web/Sites/slots/migratemysql/Read | Első webes alkalmazások üzembe helyezési ponti MySql áttelepíteni. |
> | Művelet | Microsoft.Web/Sites/slots/networktrace/Action | Hálózati nyomkövetés Web Apps tárolóhelye. |
> | Művelet | Microsoft.Web/Sites/slots/newpassword/Action | ÚjJelszó Web Apps tárolóhelye. |
> | Művelet | Microsoft.Web/Sites/slots/operationresults/Read | Webes alkalmazások üzembe helyezési ponti művelet eredményekhez juthat. |
> | Művelet | Microsoft.Web/Sites/slots/Operations/Read | Webes alkalmazások üzembe helyezési ponti műveleteinek beolvasása. |
> | Művelet | Microsoft.Web/Sites/slots/perfcounters/Read | Webes alkalmazások üzembe helyezési ponti teljesítményszámlálók beolvasása. |
> | Művelet | Microsoft.Web/Sites/slots/phplogging/Read | Webes alkalmazások üzembe helyezési ponti Phplogging beolvasása. |
> | Művelet | Microsoft.Web/Sites/slots/premieraddons/DELETE | Webes alkalmazások üzembe helyezési ponti Premier bővítményei törlése. |
> | Művelet | Microsoft.Web/Sites/slots/premieraddons/Read | Webes alkalmazások üzembe helyezési ponti Premier bővítményei beolvasása. |
> | Művelet | Microsoft.Web/Sites/slots/premieraddons/Write | Frissítse a webes alkalmazások üzembe helyezési ponti Premier bővítményei. |
> | Művelet | microsoft.web/sites/slots/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Művelet | microsoft.web/sites/slots/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Művelet | Microsoft.Web/sites/slots/providers/Microsoft.Insights/metricDefinitions/Read | A webalkalmazás elérhető metrikai meghatározások beolvasása |
> | Művelet | Microsoft.Web/Sites/slots/publiccertificates/Read | Webes alkalmazások üzembe helyezési ponti nyilvános tanúsítványokat le. |
> | Művelet | Microsoft.Web/Sites/slots/publiccertificates/Write | Hozzon létre, vagy a Web Apps üzembe helyezési ponti nyilvános tanúsítványok frissítése. |
> | Művelet | Microsoft.Web/sites/slots/publish/Action | Egy webes tárolóhelye közzététele |
> | Művelet | Microsoft.Web/sites/slots/publishxml/Action | Profil xml közzétételt a webalkalmazás beolvasása |
> | Művelet | Microsoft.Web/sites/slots/Read | A webalkalmazás üzembe helyezési pont tulajdonságainak beolvasása |
> | Művelet | Microsoft.Web/Sites/slots/RECOVER/Action | Állítsa helyre a Web Apps tárolóhelye. |
> | Művelet | Microsoft.Web/sites/slots/resetSlotConfig/Action | Webes alkalmazás helyezési pont konfigurációjának visszaállítása |
> | Művelet | Microsoft.Web/Sites/slots/resourcehealthmetadata/Read | Webes alkalmazások üzembe helyezési ponti Resource Health metaadatot beszerezni. |
> | Művelet | Microsoft.Web/sites/slots/restart/Action | Indítsa újra a webes alkalmazás tárhely |
> | Művelet | Microsoft.Web/Sites/slots/restore/Read | Webes alkalmazások üzembe helyezési ponti visszaállítási beolvasása. |
> | Művelet | Microsoft.Web/Sites/slots/restore/Write | Állítsa vissza a Web Apps tárolóhelye. |
> | Művelet | Microsoft.Web/Sites/slots/restoresnapshot/Action | Állítson vissza egy webes alkalmazások üzembe helyezési ponti pillanatképet. |
> | Művelet | Microsoft.Web/Sites/slots/siteextensions/DELETE | Webes alkalmazások üzembe helyezési ponti helyhez kiterjesztések törlése. |
> | Művelet | Microsoft.Web/Sites/slots/siteextensions/Read | Webes alkalmazások üzembe helyezési ponti helyhez kiterjesztések beolvasása. |
> | Művelet | Microsoft.Web/Sites/slots/siteextensions/Write | Webes alkalmazások üzembe helyezési ponti hely bővítmények frissítése. |
> | Művelet | Microsoft.Web/sites/slots/slotsdiffs/Action | Konfiguráció web app és az üzembe helyezési ponti közötti különbségek beolvasása |
> | Művelet | Microsoft.Web/sites/slots/slotsswap/Action | Felcserélni a webalkalmazás üzembe helyezési |
> | Művelet | Microsoft.Web/Sites/slots/snapshots/Read | Webes alkalmazások üzembe helyezési ponti pillanatképek beolvasása. |
> | Művelet | Microsoft.Web/sites/slots/sourcecontrols/Delete | Webes alkalmazás a tárhely forrás a konfigurációs beállítások törlése |
> | Művelet | Microsoft.Web/sites/slots/sourcecontrols/Read | Webes alkalmazás a tárhely verziókezelő konfigurációs beállításainak beolvasása |
> | Művelet | Microsoft.Web/sites/slots/sourcecontrols/Write | Webes alkalmazás a tárhely forrás a konfigurációs beállítások frissítése |
> | Művelet | Microsoft.Web/sites/slots/start/Action | Indítsa el a webes alkalmazás tárhely |
> | Művelet | Microsoft.Web/sites/slots/stop/Action | Állítsa le a webes alkalmazás tárhely |
> | Művelet | Microsoft.Web/Sites/slots/Sync/Action | Szinkronizálási Web Apps tárolóhelye. |
> | Művelet | Microsoft.Web/Sites/slots/triggeredwebjobs/DELETE | Webes alkalmazások üzembe helyezési ponti indított webjobs-feladatok törlése. |
> | Művelet | Microsoft.Web/Sites/slots/triggeredwebjobs/Read | Webes alkalmazások üzembe helyezési ponti indított webjobs-feladatok beolvasása. |
> | Művelet | Microsoft.Web/Sites/slots/triggeredwebjobs/Run/Action | Webes alkalmazások üzembe helyezési ponti indított webjobs-feladatok futtatásához. |
> | Művelet | Microsoft.Web/Sites/slots/usages/Read | Webes alkalmazások üzembe helyezési ponti módjait beolvasása. |
> | Művelet | Microsoft.Web/Sites/slots/virtualnetworkconnections/DELETE | Törölje a webes alkalmazások üzembe helyezési ponti virtuális hálózati kapcsolatokat. |
> | Művelet | Microsoft.Web/Sites/slots/virtualnetworkconnections/Gateways/Write | Frissítse a webes alkalmazások üzembe helyezési ponti virtuális hálózati kapcsolatok átjárókat. |
> | Művelet | Microsoft.Web/Sites/slots/virtualnetworkconnections/Read | Lekérni a webes alkalmazások üzembe helyezési ponti virtuális hálózati kapcsolatokat. |
> | Művelet | Microsoft.Web/Sites/slots/virtualnetworkconnections/Write | Frissítse a webes alkalmazások üzembe helyezési ponti virtuális hálózati kapcsolatokat. |
> | Művelet | Microsoft.Web/Sites/slots/webjobs/Read | Webes alkalmazások üzembe helyezési ponti webjobs-feladatok beolvasása. |
> | Művelet | Microsoft.Web/sites/slots/Write | Webes alkalmazás új tárhely létrehozása vagy meglévő pillanatkép frissítése |
> | Művelet | Microsoft.Web/sites/slotsdiffs/Action | Konfiguráció web app és az üzembe helyezési ponti közötti különbségek beolvasása |
> | Művelet | Microsoft.Web/sites/slotsswap/Action | Felcserélni a webalkalmazás üzembe helyezési |
> | Művelet | Microsoft.Web/Sites/snapshots/Read | Web Apps pillanatképek beolvasása. |
> | Művelet | Microsoft.Web/sites/sourcecontrols/Delete | Webes alkalmazás forrás a konfigurációs beállítások törlése |
> | Művelet | Microsoft.Web/sites/sourcecontrols/Read | Webalkalmazás verziókövetésének konfigurációs beállításainak beolvasása |
> | Művelet | Microsoft.Web/sites/sourcecontrols/Write | Webes alkalmazás forrás a konfigurációs beállítások frissítése |
> | Művelet | Microsoft.Web/sites/start/Action | A webalkalmazás elindítása |
> | Művelet | Microsoft.Web/sites/stop/Action | A webalkalmazás leállítása |
> | Művelet | Microsoft.Web/Sites/Sync/Action | Sync Web Apps. |
> | Művelet | Microsoft.Web/Sites/syncfunctiontriggers/Action | Szinkronizálási függvény eseményindítók webes alkalmazásokhoz. |
> | Művelet | Microsoft.Web/Sites/triggeredwebjobs/DELETE | Web Apps indított webjobs-feladatok törlése. |
> | Művelet | Microsoft.Web/Sites/triggeredwebjobs/history/Read | Webes alkalmazások indított webjobs-feladatok előzményeinek lekérése. |
> | Művelet | Microsoft.Web/Sites/triggeredwebjobs/Read | Web Apps indított webjobs-feladatok beolvasása. |
> | Művelet | Microsoft.Web/Sites/triggeredwebjobs/Run/Action | Web Apps indított webjobs-feladatok futtatásához. |
> | Művelet | Microsoft.Web/Sites/usages/Read | Web Apps módjait beolvasása. |
> | Művelet | Microsoft.Web/Sites/virtualnetworkconnections/DELETE | Törölje a Web Apps virtuális hálózati kapcsolatokat. |
> | Művelet | Microsoft.Web/Sites/virtualnetworkconnections/Gateways/Read | Web Apps virtuális hálózati kapcsolatok átjárók beolvasása. |
> | Művelet | Microsoft.Web/Sites/virtualnetworkconnections/Gateways/Write | Frissítse a Web Apps virtuális hálózati kapcsolatok átjárókat. |
> | Művelet | Microsoft.Web/Sites/virtualnetworkconnections/Read | Lekérni a Web Apps virtuális hálózati kapcsolatokat. |
> | Művelet | Microsoft.Web/Sites/virtualnetworkconnections/Write | Frissítse a Web Apps virtuális hálózati kapcsolatokat. |
> | Művelet | Microsoft.Web/Sites/webjobs/Read | Web Apps webjobs-feladatok beolvasása. |
> | Művelet | Microsoft.Web/sites/Write | Új webalkalmazás létrehozása vagy meglévő pillanatkép frissítése |
> | Művelet | Microsoft.Web/skus/Read | SKU beolvasása. |
> | Művelet | Microsoft.Web/sourcecontrols/Read | Adatforrás-vezérlők beolvasása. |
> | Művelet | Microsoft.Web/sourcecontrols/Write | Frissítse az adatforrás-vezérlők. |
> | Művelet | Microsoft.Web/unregister/Action | Az előfizetés Microsoft.Web erőforrás-szolgáltató regisztrációját. |
> | Művelet | Microsoft.Web/Validate/Action | Ellenőrzése. |
> | Művelet | Microsoft.Web/verifyhostingenvironmentvnet/Action | Ellenőrizze, hogy üzemeltetési környezet Vnet. |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Művelet | Microsoft.WorkloadMonitor/components/read | Olvasási műveletek erőforrások |
> | Művelet | Microsoft.WorkloadMonitor/healthInstances/read | Olvasási műveletek erőforrások |
> | Művelet | Microsoft.WorkloadMonitor/Operations/read | Olvasási műveletek erőforrások |
> | Művelet | Microsoft.WorkloadMonitor/workloads/delete | A munkaterhelés erőforrás törlése |
> | Művelet | Microsoft.WorkloadMonitor/workloads/read | A munkaterhelés erőforrás beolvasása |
> | Művelet | Microsoft.WorkloadMonitor/workloads/write | Írja a munkaterhelés erőforrás |

## <a name="next-steps"></a>További lépések

- [Egyéni szerepkörök](custom-roles.md)
- [Beépített szerepkörök](built-in-roles.md)
