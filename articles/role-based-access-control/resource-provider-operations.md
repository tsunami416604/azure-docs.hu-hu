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
ms.date: 05/18/2018
ms.author: rolyon
ms.openlocfilehash: a381d428f1ecf07836151e6c7d9c88c6fc99736e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34643192"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Az Azure Resource Manager erőforrás-szolgáltatói műveletekhez

Ez a cikk az egyes Azure Resource Manager erőforrás-szolgáltató elérhető műveleteinek listázása. Ezek a műveletek használható [egyéni szerepkörök](custom-roles.md) biztosításához részletes [szerepköralapú hozzáférés-vezérlést (RBAC)](overview.md) erőforrások az Azure-ban. A művelet karakterláncok formátuma a következő: `Microsoft.{ProviderName}/{ChildResourceType}/{action}`

Az erőforrás-szolgáltatói műveletekhez mindig fejlesztik vannak. Megtekintéséhez használja a legújabb műveletek [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) vagy [az szolgáltató műveletlista](/cli/azure/provider/operation#az-provider-operation-list).

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.AAD/domainServices/delete | Deletes Domain Services. |
> | Műveletek | Microsoft.AAD/domainServices/read | Reads Domain Services. |
> | Műveletek | Microsoft.AAD/domainServices/write | Write Domain Services |
> | Műveletek | Microsoft.AAD/locations/operationresults/read | Egy aszinkron művelet állapotát olvassa. |
> | Műveletek | Microsoft.AAD/Operations/read | A művelet, mert a honosított rövid leírás megjelenjen-e a felhasználó. |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.aadiam/diagnosticsettings/DELETE | A diagnosztikai beállítás törlése |
> | Műveletek | Microsoft.aadiam/diagnosticsettings/Read | A diagnosztikai beállítás beolvasásakor. |
> | Műveletek | Microsoft.aadiam/diagnosticsettings/Write | A diagnosztikai beállításának írása |
> | Műveletek | Microsoft.aadiam/diagnosticsettingscategories/Read | A diagnosztikai beállításának kategóriák olvasása |
> | Műveletek | microsoft.aadiam/tenants/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | microsoft.aadiam/tenants/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | microsoft.aadiam/tenants/providers/Microsoft.Insights/logDefinitions/read | A bérlők számára a naplók beolvasása |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Addons/operations/read | Lekérdezi a függő Entitás műveletek támogatott. |
> | Műveletek | Microsoft.Addons/register/action | A megadott előfizetés regisztrálása Microsoft.Addons |
> | Műveletek | Microsoft.Addons/supportProviders/listsupportplaninfo/action | A megadott előfizetés aktuális támogatási terv információkat sorolja fel. |
> | Műveletek | Microsoft.Addons/supportProviders/supportPlanTypes/delete | Eltávolítja a megadott kanonikus támogatási csomag |
> | Műveletek | Microsoft.Addons/supportProviders/supportPlanTypes/read | A megadott kanonikus támogatási terv állapot beolvasása. |
> | Műveletek | Microsoft.Addons/supportProviders/supportPlanTypes/write | A megadott típus kanonikus támogatási terv hozzáadja. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/action | Új erdő létrehozása a bérlő számára. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | Minden kiszolgáló lekérdezi a megadott szolgáltatás neve. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/alerts/read | Lekérdezi a riasztás részleteit a erdő például alertid, riasztás jelenik meg, dátum, a riasztás utolsó észlelt, a riasztás leírásában, az utolsó frissített, a riasztási szint, riasztás állapota, riasztás hibaelhárítási hivatkozások stb. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/configuration/read | Lekérdezi a szolgáltatás konfigurációját az erdőhöz. Példa - erdő neve, Functionla szint, tartomány-kiosztási főkiszolgáló műveleti Főkiszolgálói szerepkörének, séma-főkiszolgáló műveleti Főkiszolgálói szerepkör stb. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/delete | Szolgáltatás törlése, és azt-kiszolgálókból, valamint az egészségügyi adatokat. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/dimensions/read | A tartományok és a helyek adatokat lekérdezi az erdőhöz. Példa-állapot, aktív riasztások feloldotta a riasztásokat, például a tartomány működési szintjét, erdő, infrastruktúra-főkiszolgálóhoz, az elsődleges tartományvezérlő, tulajdonságok relatív azonosítókat kezelő fő stb.  |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/features/userpreference/read | Lekérdezi a felhasználói beállítás, az erdőhöz.<br>Példa - pl. ldapsuccessfulbinds ntlmauthentications, kerberosauthentications, addsinsightsagentprivatebytes, ldapsearches MetricCounterName.<br>A felhasználói felület diagramok stb beállításait. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/forestsummary/read | Erdő összefoglaló lekérdezi a megadott erdő esetén, például az erdő neve, ebben az erdőben a tartományok száma, a helyek számát és helyek részletek stb. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | Beolvassa a támogatott metrikák listáját egy adott szolgáltatáshoz.<br>Példa extranetes fiókzárolást, sikertelen kérelmek teljes, függőben lévő a jogkivonat-kérelmeket (Proxy), a jogkivonat-kérelmeket/másodperc az AD FS szolgáltatás stb.<br>NTLM-hitelesítés/mp, LDAP sikeres kötések/mp, LDAP-kötési idő, LDAP aktív szálak, Kerberos hitelesítési események másodpercenkénti stb ATQ szálak teljes ADDomainService a.<br>Futtassa a profil késés, a TCP-kapcsolatok létrehozott, a Insights Agent a saját memória, a exportálási statisztikák az Azure AD-ADSync szolgáltatás. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | Megadott egy szolgáltatást, ez az API lekérdezi a mérőszámok-adatokat.<br>Például az API segítségével kapcsolatos információk: extranetes fiókzárolást, a sikertelen kérelmek teljes, a függőben lévő a jogkivonat-kérelmeket (Proxy), a jogkivonat-kérelmeket/sec ADFederation szolgáltatás stb.<br>NTLM-hitelesítés/mp, LDAP sikeres kötések/mp, LDAP-kötési idő, LDAP aktív szálak, Kerberos hitelesítési események másodpercenkénti stb ATQ szálak teljes ADDomain szolgáltatáshoz.<br>Profil késést, az TCP-kapcsolatok létrehozása Insights Agent a saját memória, Exportálás futtatása statisztikák az Azure AD Sync szolgáltatás. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/premiumcheck/read | Ez az API premium-bérlő minden előkészítve ADDomainServices listájának beolvasása. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/read | A megadott szolgáltatásnév lekéri a szolgáltatás részletes adatai. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | Lekéri a megadott szolgáltatásnevéhez tartozó összes kiszolgáló replikációs adatokat. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | Lekérdezi a tartományvezérlők és a replikálási hibákat, ha van ilyen. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | Lekérdezi és replikálási adatainak az adott erdőben a tartománynév-vezérlő listája végezze el. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/servicemembers/action | Adja hozzá a szolgáltatás a server-példány. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | Ez az api során a kiszolgáló regisztrációjának ADDomainService, a hitelesítő adatokat lekérni a bevezetéshez új kiszolgálók neve. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | Egy adott szolgáltatáshoz, és a bérlői kiszolgáló törlése. |
> | Műveletek | Microsoft.ADHybridHealthService/addsservices/write | Létrehozza vagy frissíti a ADDomainService példány a bérlő számára. |
> | Műveletek | Microsoft.ADHybridHealthService/configuration/action | Frissítések bérlő konfigurációjához. |
> | Műveletek | Microsoft.ADHybridHealthService/configuration/read | Beolvassa a bérlő konfigurációjához. |
> | Műveletek | Microsoft.ADHybridHealthService/configuration/write | Létrehoz egy bérlői konfigurációt. |
> | Műveletek | Microsoft.ADHybridHealthService/logs/contents/read | Az ügynök telepítése és a blob tárolt regisztrációs naplók tartalmának beolvasása. |
> | Műveletek | Microsoft.ADHybridHealthService/logs/read | Az ügynök telepítése és a regisztrációs naplókat lekérdezi a bérlő számára. |
> | Műveletek | Microsoft.ADHybridHealthService/operations/read | Beolvassa a rendszer által támogatott műveletek listáját. |
> | Műveletek | Microsoft.ADHybridHealthService/register/action | Állapotfigyelő szolgáltatás ADHybrid erőforrás-szolgáltató regisztrálja, és lehetővé teszi, hogy a ADHybrid Állapotfigyelő szolgáltatás-erőforrást. |
> | Műveletek | Microsoft.ADHybridHealthService/reports/availabledeployments/read | Beolvassa a felhasználói incidensek támogatásához DevOps által használt elérhető régiók listáját. |
> | Műveletek | Microsoft.ADHybridHealthService/reports/badpassword/read | Beolvassa a rossz jelszót listáját az Active Directory összevonási szolgáltatás összes felhasználója számára. |
> | Műveletek | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | Lekérdezi a Blob SAS URI-t tartalmazó állapotát, és a / / IP-cím, egy adott bérlő naponta UserId kísérletek gyakorisága rossz felhasználónév/jelszó újonnan a várólistában levő jelentés feladat végső eredménye. |
> | Műveletek | Microsoft.ADHybridHealthService/reports/consentedtodevopstenants/read | Beolvassa a DevOps listáját átadni kívánt hozzájárult e bérlők. Általában használt ügyfél-támogatási szolgálatához. |
> | Műveletek | Microsoft.ADHybridHealthService/reports/isdevops/read | Lekérdezi a érték azt jelzi, hogy a teannt időjárási DevOps átadni kívánt hozzájárult e vagy sem. |
> | Műveletek | Microsoft.ADHybridHealthService/reports/selectdevopstenant/read | A kijelölt fejlesztői ops bérlői userid(objectid) frissítések. |
> | Műveletek | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | Lekérdezi a megadott tenant kijelölt telepítése. |
> | Műveletek | Microsoft.ADHybridHealthService/reports/tenantassigneddeployment/read | A bérlő azonosítója megadott lekérdezi a bérlő tárolási helyét. |
> | Műveletek | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | Lekérdezi a földrajzi hely, ahonnan adatokat érhető el. |
> | Műveletek | Microsoft.ADHybridHealthService/services/action | Frissíti a szolgáltatáspéldány a bérlőben. |
> | Műveletek | Microsoft.ADHybridHealthService/services/alerts/read | Beolvassa a riasztásokat, a szolgáltatás számára. |
> | Műveletek | Microsoft.ADHybridHealthService/services/alerts/read | Beolvassa a riasztásokat, a szolgáltatás számára. |
> | Műveletek | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | A szolgáltatás a megadott neve ellenőrzi, egy szolgáltatás rendelkezik-e minden szükséges, hogy a szolgáltatás használatához. |
> | Műveletek | Microsoft.ADHybridHealthService/services/delete | Törli a szolgáltatáspéldány a bérlőben. |
> | Műveletek | Microsoft.ADHybridHealthService/services/exporterrors/read | Az exportálási hibák lekérdezi a megadott szinkronizálási szolgáltatás. |
> | Műveletek | Microsoft.ADHybridHealthService/services/exportstatus/read | Exportálás állapotát olvassa be egy adott szolgáltatáshoz. |
> | Műveletek | Microsoft.ADHybridHealthService/services/feedbacktype/feedback/read | Riasztások visszajelzést kap egy adott szolgáltatáshoz, és a kiszolgáló. |
> | Műveletek | Microsoft.ADHybridHealthService/services/metricmetadata/read | Beolvassa a támogatott metrikák listáját egy adott szolgáltatáshoz.<br>Példa extranetes fiókzárolást, sikertelen kérelmek teljes, függőben lévő a jogkivonat-kérelmeket (Proxy), a jogkivonat-kérelmeket/másodperc az AD FS szolgáltatás stb.<br>NTLM-hitelesítés/mp, LDAP sikeres kötések/mp, LDAP-kötési idő, LDAP aktív szálak, Kerberos hitelesítési események másodpercenkénti stb ATQ szálak teljes ADDomainService a.<br>Futtassa a profil késés, a TCP-kapcsolatok létrehozott, a Insights Agent a saját memória, a exportálási statisztikák az Azure AD-ADSync szolgáltatás. |
> | Műveletek | Microsoft.ADHybridHealthService/services/metrics/groups/average/read | Megadott egy szolgáltatás, az API lekérdezi a átlagos metrikáihoz egy adott szolgáltatáshoz.<br>Például az API segítségével kapcsolatos információk: extranetes fiókzárolást, a sikertelen kérelmek teljes, a függőben lévő a jogkivonat-kérelmeket (Proxy), a jogkivonat-kérelmeket/sec ADFederation szolgáltatás stb.<br>NTLM-hitelesítés/mp, LDAP sikeres kötések/mp, LDAP-kötési idő, LDAP aktív szálak, Kerberos hitelesítési események másodpercenkénti stb ATQ szálak teljes ADDomain szolgáltatáshoz.<br>Profil késést, az TCP-kapcsolatok létrehozása Insights Agent a saját memória, Exportálás futtatása statisztikák az Azure AD Sync szolgáltatás. |
> | Műveletek | Microsoft.ADHybridHealthService/services/metrics/groups/read | Megadott egy szolgáltatást, ez az API lekérdezi a mérőszámok-adatokat.<br>Például az API segítségével kapcsolatos információk: extranetes fiókzárolást, a sikertelen kérelmek teljes, a függőben lévő a jogkivonat-kérelmeket (Proxy), a jogkivonat-kérelmeket/sec ADFederation szolgáltatás stb.<br>NTLM-hitelesítés/mp, LDAP sikeres kötések/mp, LDAP-kötési idő, LDAP aktív szálak, Kerberos hitelesítési események másodpercenkénti stb ATQ szálak teljes ADDomain szolgáltatáshoz.<br>Profil késést, az TCP-kapcsolatok létrehozása Insights Agent a saját memória, Exportálás futtatása statisztikák az Azure AD Sync szolgáltatás. |
> | Műveletek | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | Megadott egy szolgáltatást, ez az API lekérdezi a összesített nézet metrikáihoz egy adott szolgáltatáshoz.<br>Például az API segítségével kapcsolatos információk: extranetes fiókzárolást, a sikertelen kérelmek teljes, a függőben lévő a jogkivonat-kérelmeket (Proxy), a jogkivonat-kérelmeket/sec ADFederation szolgáltatás stb.<br>NTLM-hitelesítés/mp, LDAP sikeres kötések/mp, LDAP-kötési idő, LDAP aktív szálak, Kerberos hitelesítési események másodpercenkénti stb ATQ szálak teljes ADDomain szolgáltatáshoz.<br>Profil késést, az TCP-kapcsolatok létrehozása Insights Agent a saját memória, Exportálás futtatása statisztikák az Azure AD Sync szolgáltatás. |
> | Műveletek | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | Adja hozzá, vagy a szolgáltatás figyelési konfigurációjának frissítése. |
> | Műveletek | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | Lekéri egy adott szolgáltatáshoz a figyelési konfigurációhoz. |
> | Műveletek | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | Adja hozzá, vagy egy szolgáltatás figyelési beállítások frissítése. |
> | Műveletek | Microsoft.ADHybridHealthService/services/premiumcheck/read | Ez az API lekérdezi egy premium-bérlő minden előkészítve szolgáltatások listájában. |
> | Műveletek | Microsoft.ADHybridHealthService/services/read | A szolgáltatáspéldány a bérlő beolvasása. |
> | Műveletek | Microsoft.ADHybridHealthService/services/reports/details/read | Lekérdezi a jelentés az 50 legfontosabb felhasználók rossz jelszó hibák az elmúlt 7 napban |
> | Műveletek | Microsoft.ADHybridHealthService/services/servicemembers/action | A szolgáltatás hoz létre egy kiszolgálópéldányt. |
> | Műveletek | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | A riasztások beolvassa a kiszolgálón. |
> | Műveletek | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | Kiszolgáló regisztrálása során a hitelesítő adatokat lekérni a bevezetéshez új kiszolgálók az api neve. |
> | Műveletek | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | Egy adott kiszolgálóhoz az API megjelenő a kiszolgálókat és a legújabb minden feltöltésének idejét feltöltése listájának lekérése. |
> | Műveletek | Microsoft.ADHybridHealthService/services/servicemembers/delete | Törli a szolgáltatás a server-példányra. |
> | Műveletek | Microsoft.ADHybridHealthService/services/servicemembers/exportstatus/read | A szinkronizálás exportálása hiba részletes adatait lekérdezi a megadott szinkronizálási szolgáltatás. |
> | Műveletek | Microsoft.ADHybridHealthService/services/servicemembers/metrics/groups/read | Megadott egy szolgáltatást, ez az API lekérdezi a mérőszámok-adatokat.<br>Például az API segítségével kapcsolatos információk: extranetes fiókzárolást, a sikertelen kérelmek teljes, a függőben lévő a jogkivonat-kérelmeket (Proxy), a jogkivonat-kérelmeket/sec ADFederation szolgáltatás stb.<br>NTLM-hitelesítés/mp, LDAP sikeres kötések/mp, LDAP-kötési idő, LDAP aktív szálak, Kerberos hitelesítési események másodpercenkénti stb ATQ szálak teljes ADDomain szolgáltatáshoz.<br>Profil késést, az TCP-kapcsolatok létrehozása Insights Agent a saját memória, Exportálás futtatása statisztikák az Azure AD Sync szolgáltatás. |
> | Műveletek | Microsoft.ADHybridHealthService/services/servicemembers/read | Olvassa be a szolgáltatás a server-példányra. |
> | Műveletek | Microsoft.ADHybridHealthService/services/servicemembers/serviceconfiguration/read | Lekéri egy adott bérlő szolgáltatás konfigurációját. |
> | Műveletek | Microsoft.ADHybridHealthService/services/tenantwhitelisting/read | Lekéri egy adott bérlő az funkció engedélyezett állapotát. |
> | Műveletek | Microsoft.ADHybridHealthService/services/write | Hozza létre a szolgáltatáspéldány, a bérlő. |
> | Műveletek | Microsoft.ADHybridHealthService/unregister/action | Az előfizetés ADHybrid Állapotfigyelő szolgáltatás erőforrás-szolgáltató regisztrációjának törlése. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Advisor/configurations/read | Konfigurációk beolvasása |
> | Műveletek | Microsoft.Advisor/configurations/write | Létrehozza vagy frissítések konfigurációja |
> | Műveletek | Microsoft.Advisor/generateRecommendations/action | Ajánlatok generálása |
> | Műveletek | Microsoft.Advisor/generateRecommendations/read | Lekérdezi készítése javaslatok állapota |
> | Műveletek | Microsoft.Advisor/operations/read | Lekérdezi a műveleteket, a Microsoft Advisor |
> | Műveletek | Microsoft.Advisor/recommendations/read | Javaslatok beolvasása |
> | Műveletek | Microsoft.Advisor/recommendations/suppressions/delete | Tiltási törlése |
> | Műveletek | Microsoft.Advisor/recommendations/suppressions/read | Lekérdezi a suppressions |
> | Műveletek | Microsoft.Advisor/recommendations/suppressions/write | Suppressions hoz létre, frissítések |
> | Műveletek | Microsoft.Advisor/register/action | A Microsoft Advisor előfizetésének regisztrálása |
> | Műveletek | Microsoft.Advisor/suppressions/delete | Tiltási törlése |
> | Műveletek | Microsoft.Advisor/suppressions/read | Lekérdezi a suppressions |
> | Műveletek | Microsoft.Advisor/suppressions/write | Suppressions hoz létre, frissítések |
> | Műveletek | Microsoft.Advisor/unregister/action | A Microsoft Advisor az előfizetés regisztrációjának törlése |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.AlertsManagement/alerts/changestate/action | Módosítsa a riasztás állapotát. |
> | Műveletek | Microsoft.AlertsManagement/alerts/read | A riasztások lekérése a bemeneti szűrőket. |
> | Műveletek | Microsoft.AlertsManagement/alertsSummary/read | A riasztások összefoglaló adatait beolvasása |
> | Műveletek | Microsoft.AlertsManagement/Operations/read | A megadott műveletek beolvasása |
> | Műveletek | Microsoft.AlertsManagement/smartGroups/changestate/action | Az intelligens csoport állapotának módosítása |
> | Műveletek | Microsoft.AlertsManagement/smartGroups/read | Az intelligens csoportok lekérése a bemeneti szűrők |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.AnalysisServices/locations/checkNameAvailability/action | Ellenőrzi, hogy a megadott Analysis Server neve érvényes, és nincs használatban. |
> | Műveletek | Microsoft.AnalysisServices/locations/operationresults/read | A megadott művelet eredményének adatainak beolvasása. |
> | Műveletek | Microsoft.AnalysisServices/locations/operationstatuses/read | A megadott műveleti állapotának adatainak beolvasása. |
> | Műveletek | Microsoft.AnalysisServices/operations/read | A műveletek adatainak beolvasása |
> | Műveletek | Microsoft.AnalysisServices/register/action | Regisztrálja az Analysis Services erőforrás-szolgáltató. |
> | Műveletek | Microsoft.AnalysisServices/servers/delete | Az elemzési kiszolgáló törlése. |
> | Műveletek | Microsoft.AnalysisServices/servers/listGatewayStatus/action | Az átjáró a kiszolgálóhoz társított állapotát listázza. |
> | Műveletek | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/diagnosticSettings/read | Az Analysis Server diagnosztikai beállításának beolvasása. |
> | Műveletek | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti az Analysis Server diagnosztikai beállításának |
> | Műveletek | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/logDefinitions/read | A kiszolgálók naplók beolvasása. |
> | Műveletek | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/metricDefinitions/read | Az elemzési kiszolgáló elérhető metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.AnalysisServices/servers/read | A megadott elemzési kiszolgáló adatainak beolvasása. |
> | Műveletek | Microsoft.AnalysisServices/servers/resume/action | Az elemzési kiszolgáló folytatja. |
> | Műveletek | Microsoft.AnalysisServices/servers/skus/read | A kiszolgáló elérhető SKU információinak lekérése |
> | Műveletek | Microsoft.AnalysisServices/servers/suspend/action | Felfüggeszti az elemzési kiszolgáló. |
> | Műveletek | Microsoft.AnalysisServices/servers/write | Létrehozza vagy frissíti a megadott Analysis-kiszolgálóhoz. |
> | Műveletek | Microsoft.AnalysisServices/skus/read | Az SKU adatainak beolvasása |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ApiManagement/checkNameAvailability/read | Ellenőrzi, hogy a megadott szolgáltatásnév érhető el |
> | Műveletek | Microsoft.ApiManagement/operations/read | Minden API-műveleteket elérhető Microsoft.ApiManagement erőforrás olvasása |
> | Műveletek | Microsoft.ApiManagement/register/action | Előfizetés regisztrálása a Microsoft.ApiManagement erőforrás-szolgáltató |
> | Műveletek | Microsoft.ApiManagement/reports/read | Összesítve időszakokat, földrajzi régiót, fejlesztők, termékek, API-k, műveletek, előfizetés és byRequest jelentések lekérése. |
> | Műveletek | Microsoft.ApiManagement/service/apis/delete | Távolítsa el a meglévő API |
> | Műveletek | Microsoft.ApiManagement/service/apis/diagnostics/delete | Távolítsa el a meglévő diagnosztikai |
> | Műveletek | Microsoft.ApiManagement/service/apis/diagnostics/loggers/delete | Távolítsa el a naplózó diagnosztikai beállítással leképezése |
> | Műveletek | Microsoft.ApiManagement/service/apis/diagnostics/loggers/read | Meglévő diagnosztikai figyelő szoftverek listájának beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/apis/diagnostics/loggers/write | Térkép naplózó diagnosztikai beállítások |
> | Műveletek | Microsoft.ApiManagement/service/apis/diagnostics/read | Diagnosztika vagy a diagnosztika Get részleteinek listáját |
> | Műveletek | Microsoft.ApiManagement/service/apis/diagnostics/write | Új diagnosztikai hozzáadásakor vagy módosításakor a meglévő diagnosztikai részletek |
> | Műveletek | Microsoft.ApiManagement/service/apis/issues/attachments/delete | Eltávolítja a meglévő melléklet |
> | Műveletek | Microsoft.ApiManagement/service/apis/issues/attachments/read | Ezzel a melléklet adatokat probléma mellékletek vagy lekérdezi az API Management probléma |
> | Műveletek | Microsoft.ApiManagement/service/apis/issues/attachments/write | Api probléma melléklet hozzáadása |
> | Műveletek | Microsoft.ApiManagement/service/apis/issues/comments/delete | Eltávolítja a meglévő megjegyzés |
> | Műveletek | Microsoft.ApiManagement/service/apis/issues/comments/read | Lekérdezi probléma megjegyzések vagy lekérdezi az API Management probléma Megjegyzés részletei |
> | Műveletek | Microsoft.ApiManagement/service/apis/issues/comments/write | Api probléma Megjegyzés hozzáadása |
> | Műveletek | Microsoft.ApiManagement/service/apis/issues/delete | Eltávolítja a meglévő probléma |
> | Műveletek | Microsoft.ApiManagement/service/apis/issues/read | Probléma merül fel az API-t vagy lekérdezi az API Management probléma részletei beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/apis/issues/write | Api-probléma hozzáadásakor vagy módosításakor api probléma |
> | Műveletek | Microsoft.ApiManagement/service/apis/operations/delete | Távolítsa el a meglévő API-művelet |
> | Műveletek | Microsoft.ApiManagement/service/apis/operations/policies/delete | API-művelet házirendek házirend-konfiguráció eltávolítása |
> | Műveletek | Microsoft.ApiManagement/service/apis/operations/policies/read | API-művelet vagy Get házirend konfigurációs adatait az API-művelet házirendek lekéréséhez |
> | Műveletek | Microsoft.ApiManagement/service/apis/operations/policies/write | Állítsa be a házirend konfigurációs adatait az API-művelet |
> | Műveletek | Microsoft.ApiManagement/service/apis/operations/policy/delete | Távolítsa el a házirend-konfigurációt a művelet |
> | Műveletek | Microsoft.ApiManagement/service/apis/operations/policy/read | Ezzel a házirend konfigurációs adatokat művelet |
> | Műveletek | Microsoft.ApiManagement/service/apis/operations/policy/write | Adja meg a művelet házirend konfigurációs részleteit |
> | Műveletek | Microsoft.ApiManagement/service/apis/operations/read | A meglévő API műveletek listájának vagy részletes API művelet |
> | Műveletek | Microsoft.ApiManagement/service/apis/operations/tags/delete | Meglévő címke meglévő műveletet a társítás törlése |
> | Műveletek | Microsoft.ApiManagement/service/apis/operations/tags/read | A művelet vagy a kód első részletek társított címkék beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/apis/operations/tags/write | Meglévő címke társítása meglévő művelet |
> | Műveletek | Microsoft.ApiManagement/service/apis/operations/write | Új API-művelet létrehozni vagy frissíteni a meglévő API-művelet |
> | Műveletek | Microsoft.ApiManagement/service/apis/operationsByTags/read | Művelet/címke társítások listájának beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/apis/policies/delete | Távolítsa el a házirend-konfigurációt a API-házirendek |
> | Műveletek | Microsoft.ApiManagement/service/apis/policies/read | Házirendek lekérése az API-t vagy a Get házirend konfigurációs adatait az API-hoz |
> | Műveletek | Microsoft.ApiManagement/service/apis/policies/write | Állítsa be a házirend-konfigurációs adatait az API-hoz |
> | Műveletek | Microsoft.ApiManagement/service/apis/policy/delete | Távolítsa el a házirend-konfigurációs API |
> | Műveletek | Microsoft.ApiManagement/service/apis/policy/read | Ezzel a házirend konfigurációs adatokat az API-hoz |
> | Műveletek | Microsoft.ApiManagement/service/apis/policy/write | Állítsa be a házirend-konfigurációs adatait az API-hoz |
> | Műveletek | Microsoft.ApiManagement/service/apis/products/read | Az API-t részét képező összes termék beszerzése |
> | Műveletek | Microsoft.ApiManagement/service/apis/read | Szerezze be az összes regisztrált API-k vagy Get részleteit API listát |
> | Műveletek | Microsoft.ApiManagement/service/apis/releases/delete | Eltávolítja az összes kiadásaiban az API-t vagy API eltávolítása kiadás |
> | Műveletek | Microsoft.ApiManagement/service/apis/releases/read | Lekérni kiadások API reelase API-t vagy a Get részleteit |
> | Műveletek | Microsoft.ApiManagement/service/apis/releases/write | Új API-verziót létrehozása vagy meglévő API-kiadás frissítése |
> | Műveletek | Microsoft.ApiManagement/service/apis/revisions/delete | Eltávolítja az összes változatának az API-k |
> | Műveletek | Microsoft.ApiManagement/service/apis/revisions/read | Az API-k tartozó változatok beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/apis/schemas/delete | Eltávolítja a meglévő séma |
> | Műveletek | Microsoft.ApiManagement/service/apis/schemas/document/read | A dokumentum a séma leíró lekérése |
> | Műveletek | Microsoft.ApiManagement/service/apis/schemas/document/write | A dokumentum leíró a séma frissítése |
> | Műveletek | Microsoft.ApiManagement/service/apis/schemas/read | Lekérdezi a sémák az API által használt vagy a sémák lekérdezi a megadott API-hoz. |
> | Műveletek | Microsoft.ApiManagement/service/apis/schemas/write | Beállítja a az API által használt sémák |
> | Műveletek | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | Távolítsa el az API-t címke leírása |
> | Műveletek | Microsoft.ApiManagement/service/apis/tagDescriptions/read | Címkék leírások beszerzése API-t vagy az beszerzése címke leírás hatókörében API-hatóköre |
> | Műveletek | Microsoft.ApiManagement/service/apis/tagDescriptions/write | Hatókör leírása létrehozása/módosítása címke API |
> | Műveletek | Microsoft.ApiManagement/service/apis/tags/delete | Meglévő API/címke társításának megszüntetése |
> | Műveletek | Microsoft.ApiManagement/service/apis/tags/read | Minden API/címke társítás API/címke társítás API-t vagy a Get részleteinek beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/apis/tags/write | Új API/címke társítás hozzáadása |
> | Műveletek | Microsoft.ApiManagement/service/apis/write | Hozzon létre új API-JÁVAL vagy a meglévő API részleteinek frissítése |
> | Műveletek | Microsoft.ApiManagement/service/apisByTags/read | Szerezze be az API/címke társítások listát |
> | Műveletek | Microsoft.ApiManagement/service/api-version-sets/delete | Távolítsa el a meglévő VersionSet |
> | Műveletek | Microsoft.ApiManagement/service/api-version-sets/read | Itt olvashatók verzió csoport entitásokat vagy egy VersionSet lekérdezi részleteit listája |
> | Műveletek | Microsoft.ApiManagement/service/api-version-sets/versions/read | Verzió entitások listájának beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/api-version-sets/write | Új VersionSet létrehozása vagy meglévő VersionSet részleteinek frissítése |
> | Műveletek | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | Frissíti a Microsoft.ApiManagement erőforrásaihoz, a virtuális hálózatot válasszon frissített hálózati beállításokat. |
> | Műveletek | Microsoft.ApiManagement/service/authorizationServers/delete | Távolítsa el a meglévő engedélyezési kiszolgáló |
> | Műveletek | Microsoft.ApiManagement/service/authorizationServers/read | Engedélyezési kiszolgálók listája vagy az engedélyezési kiszolgáló adatait az beszerzése |
> | Műveletek | Microsoft.ApiManagement/service/authorizationServers/write | Hozzon létre egy új hitelesítési kiszolgáló vagy egy meglévő engedélyezési kiszolgáló frissítés részletei |
> | Műveletek | Microsoft.ApiManagement/service/backends/delete | Távolítsa el a létező háttérrendszerek |
> | Műveletek | Microsoft.ApiManagement/service/backends/read | Háttérkiszolgálókon listáját, vagy a részletek a háttér |
> | Műveletek | Microsoft.ApiManagement/service/backends/reconnect/action | Újracsatlakozás kérelem létrehozása |
> | Műveletek | Microsoft.ApiManagement/service/backends/write | Adja hozzá egy új háttér vagy a meglévő háttér részleteinek frissítése |
> | Műveletek | Microsoft.ApiManagement/service/backup/action | A biztonsági mentési API Management szolgáltatás a felhasználó a megadott tárolóhoz megadott tárfiók |
> | Műveletek | Microsoft.ApiManagement/service/certificates/delete | Távolítsa el a meglévő tanúsítvány |
> | Műveletek | Microsoft.ApiManagement/service/certificates/read | A tanúsítványok listájának vagy a tanúsítvány adatainak lekérése |
> | Műveletek | Microsoft.ApiManagement/service/certificates/write | Új tanúsítvány felvétele |
> | Műveletek | Microsoft.ApiManagement/service/delete | Törli az API Management Service-példány |
> | Műveletek | Microsoft.ApiManagement/service/diagnostics/delete | Távolítsa el a meglévő diagnosztikai |
> | Műveletek | Microsoft.ApiManagement/service/diagnostics/loggers/delete | Távolítsa el a naplózó diagnosztikai beállítással leképezése |
> | Műveletek | Microsoft.ApiManagement/service/diagnostics/loggers/read | Meglévő diagnosztikai figyelő szoftverek listájának beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/diagnostics/loggers/write | Térkép naplózó diagnosztikai beállítások |
> | Műveletek | Microsoft.ApiManagement/service/diagnostics/read | Diagnosztika vagy a diagnosztika Get részleteinek listáját |
> | Műveletek | Microsoft.ApiManagement/service/diagnostics/write | Új diagnosztikai hozzáadásakor vagy módosításakor a meglévő diagnosztikai részletek |
> | Műveletek | Microsoft.ApiManagement/service/getssotoken/action | Lekérdezi SSO token használható API Management szolgáltatás örökölt portáljára rendszergazdaként történő bejelentkezéshez |
> | Műveletek | Microsoft.ApiManagement/service/groups/delete | Távolítsa el a meglévő csoporthoz |
> | Műveletek | Microsoft.ApiManagement/service/groups/read | Csoportok vagy egy csoport lekérdezi részleteinek listáját |
> | Műveletek | Microsoft.ApiManagement/service/groups/users/delete | Távolítsa el a meglévő felhasználói meglévő csoportból |
> | Műveletek | Microsoft.ApiManagement/service/groups/users/read | Csoport felhasználók listájának beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/groups/users/write | Meglévő felhasználó hozzáadása meglévő csoporthoz |
> | Műveletek | Microsoft.ApiManagement/service/groups/write | Új csoport létrehozása vagy meglévő csoport részleteinek frissítése |
> | Műveletek | Microsoft.ApiManagement/service/identityProviders/delete | Távolítsa el a meglévő identitásszolgáltató |
> | Műveletek | Microsoft.ApiManagement/service/identityProviders/read | Az identitás-szolgáltatóktól vagy identitásszolgáltató Get részleteinek listáját beszerzése |
> | Műveletek | Microsoft.ApiManagement/service/identityProviders/write | Hozzon létre egy új identitásszolgáltató vagy a frissítés részleteit egy meglévő identitásszolgáltató |
> | Műveletek | Microsoft.ApiManagement/service/locations/networkstatus/read | Lekérdezi a hálózati hozzáférési állapota az erőforrások, amelyen a szolgáltatás függ a helyen. |
> | Műveletek | Microsoft.ApiManagement/service/loggers/delete | Távolítsa el a meglévő naplózó |
> | Műveletek | Microsoft.ApiManagement/service/loggers/read | Figyelő szoftverek listája vagy az naplózó részleteinek beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/loggers/write | Adja hozzá az új naplózó vagy meglévő naplózó részleteinek frissítése |
> | Műveletek | Microsoft.ApiManagement/service/managedeployments/action | Módosítsa a Termékváltozat jegyek, hozzáadása regionális egy szolgáltatás üzemelő példányainak API Management |
> | Műveletek | Microsoft.ApiManagement/service/networkstatus/read | Lekérdezi a hálózati hozzáférési állapota az erőforrások, amelyen a szolgáltatás függ. |
> | Műveletek | Microsoft.ApiManagement/service/notifications/action | Értesítést küld egy adott felhasználó |
> | Műveletek | Microsoft.ApiManagement/service/notifications/read | Lekérdezi az összes API Management publisher értesítések vagy beszerzése API Management publisher értesítési részletei |
> | Műveletek | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | Eltávolítja a meglévő E-mail értesítést társított |
> | Műveletek | Microsoft.ApiManagement/service/notifications/recipientEmails/read | Társított API Management Publisher értesítési e-mailek címzettjeinek beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/notifications/recipientEmails/write | Hozzon létre új E-mail címzettet az értesítés |
> | Műveletek | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | Az értesítési címzettek társított felhasználó eltávolítása |
> | Műveletek | Microsoft.ApiManagement/service/notifications/recipientUsers/read | Az értesítés társított címzett felhasználók |
> | Műveletek | Microsoft.ApiManagement/service/notifications/recipientUsers/write | Felhasználó hozzáadása az értesítési címzettek |
> | Műveletek | Microsoft.ApiManagement/service/notifications/write | Hozzon létre vagy az Update API Management publisher értesítés |
> | Műveletek | Microsoft.ApiManagement/service/openidConnectProviders/delete | Távolítsa el a meglévő OpenID Connect szolgáltató |
> | Műveletek | Microsoft.ApiManagement/service/openidConnectProviders/read | Szerezze be az OpenID Connect-szolgáltatókkal vagy az OpenID Connect szolgáltató Get részleteit listát |
> | Műveletek | Microsoft.ApiManagement/service/openidConnectProviders/write | Hozzon létre egy új OpenID Connect szolgáltató vagy a frissítés adatait egy meglévő OpenID Connect-szolgáltató |
> | Műveletek | Microsoft.ApiManagement/service/operationresults/read | Hosszú ideig futó művelet aktuális állapotának beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/policies/delete | Távolítsa el a házirend-konfigurációt a bérlő házirendek |
> | Műveletek | Microsoft.ApiManagement/service/policies/read | Házirendek bérlő bérlői vagy Get házirend konfigurációs részletek lekérése |
> | Műveletek | Microsoft.ApiManagement/service/policies/write | Állítsa be a házirend-konfiguráció részletei bérlő számára |
> | Műveletek | Microsoft.ApiManagement/service/policySnippets/read | Az összes házirend kódtöredékek beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/portalsettings/read | Bejelentkezési beállítások beszerezni a portál vagy a Get jelentkezzen be a portál beállításait, vagy a portál delegálási beállítások beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/portalsettings/write | Regisztráció beállítások vagy frissítés regisztráció beállítások vagy frissítés bejelentkezés beállítások vagy frissítés bejelentkezés beállítások vagy frissítés delegálási beállításokat vagy frissítés delegálási beállítások frissítése |
> | Műveletek | Microsoft.ApiManagement/service/products/apis/delete | Távolítsa el a meglévő API a meglévő termék |
> | Műveletek | Microsoft.ApiManagement/service/products/apis/read | Szerezze be az API-k meglévő termékhez hozzáadott listát |
> | Műveletek | Microsoft.ApiManagement/service/products/apis/write | Meglévő API hozzáadása meglévő termék |
> | Műveletek | Microsoft.ApiManagement/service/products/delete | Távolítsa el a meglévő termék |
> | Műveletek | Microsoft.ApiManagement/service/products/groups/delete | Meglévő termék társítás meglévő fejlesztői csoport törlése |
> | Műveletek | Microsoft.ApiManagement/service/products/groups/read | Termék társított fejlesztői csoportok listájának beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/products/groups/write | Meglévő fejlesztői csoport társítása meglévő termék |
> | Műveletek | Microsoft.ApiManagement/service/products/policies/delete | Távolítsa el a házirend-konfigurációt a termék házirendek |
> | Műveletek | Microsoft.ApiManagement/service/products/policies/read | A termék vagy a Get házirend konfigurációs részletek termék házirendek lekérése |
> | Műveletek | Microsoft.ApiManagement/service/products/policies/write | Állítsa be a házirend-konfiguráció részletei termékhez |
> | Műveletek | Microsoft.ApiManagement/service/products/policy/delete | Távolítsa el a házirend-konfigurációt a meglévő termék |
> | Műveletek | Microsoft.ApiManagement/service/products/policy/read | Házirend-konfigurációt meglévő termék beszerzése |
> | Műveletek | Microsoft.ApiManagement/service/products/policy/write | A termék meglévő házirend-konfiguráció |
> | Műveletek | Microsoft.ApiManagement/service/products/read | Termékek listáját, vagy a részletek a termék |
> | Műveletek | Microsoft.ApiManagement/service/products/subscriptions/read | Termék előfizetések listájának beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/products/tags/delete | Meglévő címke meglévő termékkel társítás törlése |
> | Műveletek | Microsoft.ApiManagement/service/products/tags/read | A termék vagy az beszerzése címke részletek társított címkék beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/products/tags/write | Meglévő címke társítása meglévő termék |
> | Műveletek | Microsoft.ApiManagement/service/products/write | Új termék létrehozása vagy meglévő termékadatok frissítése |
> | Műveletek | Microsoft.ApiManagement/service/properties/delete | Eltávolítja a meglévő tulajdonság |
> | Műveletek | Microsoft.ApiManagement/service/properties/read | Beolvassa az összes tulajdonság listáját, és lekérdezi a megadott tulajdonság részleteit |
> | Műveletek | Microsoft.ApiManagement/service/properties/write | Új tulajdonság létrehozása vagy frissítése a megadott tulajdonság értéke |
> | Műveletek | Microsoft.ApiManagement/service/providers/Microsoft.Insights/diagnosticSettings/read | Az API Management szolgáltatás diagnosztikai beállításának beolvasása. |
> | Műveletek | Microsoft.ApiManagement/service/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti az API-kezelés szolgáltatás a diagnosztikai beállítása |
> | Műveletek | Microsoft.ApiManagement/service/providers/Microsoft.Insights/logDefinitions/read | Az API Management szolgáltatás naplók beolvasása. |
> | Műveletek | Microsoft.ApiManagement/service/providers/Microsoft.Insights/metricDefinitions/read | Az API Management szolgáltatáshoz elérhető metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/quotas/periods/read | Kvóta számlálóérték beolvasása időszak |
> | Műveletek | Microsoft.ApiManagement/service/quotas/periods/write | Kvóta számláló aktuális érték |
> | Műveletek | Microsoft.ApiManagement/service/quotas/read | Értékek lekérése kvóta |
> | Műveletek | Microsoft.ApiManagement/service/quotas/write | Kvóta számláló aktuális érték |
> | Műveletek | Microsoft.ApiManagement/service/read | Olvassa el a metaadatokat az API Management szolgáltatáspéldány |
> | Műveletek | Microsoft.ApiManagement/service/reports/read | Összesítve időszakra vagy a földrajzi régió vagy fejlesztők összesítve Get jelentés összesítve Get jelentés jelentés lekérése.<br>vagy a jelentés megtekintése a termékek alapján összesíti.<br>vagy a műveletek vagy Get jelentés-előfizetés összesítve összesítve API-k vagy Get jelentés összesítve jelentés megtekintése.<br>vagy a jelentés adatainak kérelmek |
> | Műveletek | Microsoft.ApiManagement/service/restore/action | Állítsa vissza egy felhasználó által megadott tárfiók a megadott tároló API Management szolgáltatás |
> | Műveletek | Microsoft.ApiManagement/service/subscriptions/delete | Előfizetés törlése. Ez a művelet segítségével előfizetés törlése |
> | Műveletek | Microsoft.ApiManagement/service/subscriptions/read | A termék előfizetések listáját, vagy a részletek a termék előfizetés |
> | Műveletek | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | Előfizetés elsődleges kulcs újragenerálása |
> | Műveletek | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | Előfizetés másodlagos kulcs újragenerálása |
> | Műveletek | Microsoft.ApiManagement/service/subscriptions/write | Egy meglévő felhasználó számára a meglévő termék előfizetés, vagy frissítse a meglévő előfizetés részletei. Ez a művelet használható előfizetés megújításához |
> | Műveletek | Microsoft.ApiManagement/service/tagResources/read | Társított erőforrásokkal rendelkező címkék listájának beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/tags/delete | Meglévő címke eltávolítása |
> | Műveletek | Microsoft.ApiManagement/service/tags/read | Címkék listáját és a részletek a címke |
> | Műveletek | Microsoft.ApiManagement/service/tags/write | Új címke hozzáadása vagy meglévő címke részleteinek frissítése |
> | Műveletek | Microsoft.ApiManagement/service/templates/delete | Alapértelmezett API Management e-mail sablon alaphelyzetbe állítása |
> | Műveletek | Microsoft.ApiManagement/service/templates/read | Lekérdezi az összes e-mail-sablonokkal vagy lekérdezi az API Management e-mail sablon részletei |
> | Műveletek | Microsoft.ApiManagement/service/templates/write | Létrehozni vagy frissíteni az API Management e-mail sablon vagy frissíti az API Management e-mail sablon |
> | Műveletek | Microsoft.ApiManagement/service/tenant/delete | A bérlőhöz tartozó házirend-konfiguráció eltávolítása |
> | Műveletek | Microsoft.ApiManagement/service/tenant/deploy/action | A megadott git fiókirodai módosítások alkalmazása a konfigurációs adatbázis telepítési feladat fut. |
> | Műveletek | Microsoft.ApiManagement/service/tenant/operationResults/read | A művelet eredménye listája vagy az beszerzése az adott művelet eredménye |
> | Műveletek | Microsoft.ApiManagement/service/tenant/read | Házirend-konfiguráció beolvasása a bérlő vagy a Get-bérlő hozzáférés adatai |
> | Műveletek | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | Elsődleges elérési kulcs újragenerálása |
> | Műveletek | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | Másodlagos elérési kulcs újragenerálása |
> | Műveletek | Microsoft.ApiManagement/service/tenant/save/action | A tárházban található a megadott ágra konfigurációs pillanatképet hoz létre a véglegesítési |
> | Műveletek | Microsoft.ApiManagement/service/tenant/syncState/read | Az utolsó git szinkronizálás állapotának beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/tenant/validate/action | Ellenőrzi a megadott git fiókirodai módosítások |
> | Műveletek | Microsoft.ApiManagement/service/tenant/write | A bérlő vagy az Update bérlői hozzáférés adatai házirend-konfiguráció |
> | Műveletek | Microsoft.ApiManagement/service/updatecertificate/action | Az API Management szolgáltatás SSL-tanúsítvány feltöltése |
> | Műveletek | Microsoft.ApiManagement/service/updatehostname/action | A telepítő, frissítésére, vagy távolítsa el az API Management szolgáltatás egyéni tartománynevek |
> | Műveletek | Microsoft.ApiManagement/service/users/action | Új felhasználó regisztrálása |
> | Műveletek | Microsoft.ApiManagement/service/users/applications/attachments/delete | Eltávolít egy melléklet |
> | Műveletek | Microsoft.ApiManagement/service/users/applications/attachments/read | Lekérdezi az alkalmazás mellékletek vagy lekérdezi melléklet |
> | Műveletek | Microsoft.ApiManagement/service/users/applications/attachments/write | Melléklet alkalmazás hozzáadása |
> | Műveletek | Microsoft.ApiManagement/service/users/applications/delete | Eltávolítja a meglévő alkalmazáshoz |
> | Műveletek | Microsoft.ApiManagement/service/users/applications/read | Minden felhasználó az alkalmazások és az API Management lekérdezi az alkalmazás részleteinek listájának |
> | Műveletek | Microsoft.ApiManagement/service/users/applications/write | Az API Management és a frissítéseket az alkalmazás részleteinek alkalmazás regisztrálása |
> | Műveletek | Microsoft.ApiManagement/service/users/delete | Felhasználói fiók eltávolítása |
> | Műveletek | Microsoft.ApiManagement/service/users/generateSsoUrl/action | Egyszeri bejelentkezési URL-címet létrehozni. Az URL-cím használható felügyeleti portál eléréséhez |
> | Műveletek | Microsoft.ApiManagement/service/users/groups/read | Felhasználói csoportok listájának beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/users/keys/read | Felhasználói kulcsok listájának beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/users/read | A regisztrált felhasználók listáját, vagy felhasználói fiók adatainak lekérése |
> | Műveletek | Microsoft.ApiManagement/service/users/subscriptions/read | Felhasználói előfizetések listájának beolvasása |
> | Műveletek | Microsoft.ApiManagement/service/users/token/action | Hozzáférés a jogkivonatokhoz-token beszerzése a felhasználó számára |
> | Műveletek | Microsoft.ApiManagement/service/users/write | Új felhasználó vagy egy meglévő felhasználó frissítés fiókadatok regisztrálása |
> | Műveletek | Microsoft.ApiManagement/service/write | Az API Management szolgáltatást egy új példányának létrehozása |
> | Műveletek | Microsoft.ApiManagement/unregister/action | Előfizetés regisztrációjának Microsoft.ApiManagement erőforrás-szolgáltató |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Authorization/checkAccess/action | Ellenőrzi, hogy a hívó jogosult-e egy adott művelet végrehajtására |
> | Műveletek | Microsoft.Authorization/classicAdministrators/delete | Az előfizetés rendszergazdájának eltávolítása. |
> | Műveletek | Microsoft.Authorization/classicAdministrators/read | Az előfizetés rendszergazdáinak beolvasása. |
> | Műveletek | Microsoft.Authorization/classicAdministrators/write | Az előfizetés rendszergazdájának beállítása vagy módosítása. |
> | Műveletek | Microsoft.Authorization/elevateAccess/action | Felhasználói hozzáférés rendszergazdai szerepkörének megadása a hívónak a bérlői hatókörben |
> | Műveletek | Microsoft.Authorization/locks/delete | A megadott hatókör zárolásainak törlése. |
> | Műveletek | Microsoft.Authorization/locks/read | A megadott hatókör zárolásainak beolvasása. |
> | Műveletek | Microsoft.Authorization/locks/write | A megadott hatókör zárolásainak beállítása. |
> | Műveletek | Microsoft.Authorization/permissions/read | A hívó adott hatókörben érvényes engedélyeinek listázása. |
> | Műveletek | Microsoft.Authorization/policyAssignments/delete | Szabályzat-hozzárendelés törlése a megadott hatókörben. |
> | Műveletek | Microsoft.Authorization/policyAssignments/read | Szabályzat-hozzárendelés adatainak lekérése. |
> | Műveletek | Microsoft.Authorization/policyAssignments/write | Szabályzat-hozzárendelés létrehozása a megadott hatókörben. |
> | Műveletek | Microsoft.Authorization/policyDefinitions/delete | Szabályzat-definíció törlése. |
> | Műveletek | Microsoft.Authorization/policyDefinitions/read | Szabályzat-definíció adatainak lekérése. |
> | Műveletek | Microsoft.Authorization/policyDefinitions/write | Egyéni szabályzat-definíció létrehozása. |
> | Műveletek | Microsoft.Authorization/policySetDefinitions/delete | Szabályzatkészlet-definíció törlése. |
> | Műveletek | Microsoft.Authorization/policySetDefinitions/read | Egy szabályzatkészlet-definíció adatainak beolvasása. |
> | Műveletek | Microsoft.Authorization/policySetDefinitions/write | Egyéni szabályzatkészlet-definíció létrehozása. |
> | Műveletek | Microsoft.Authorization/providerOperations/read | Az összes, szerepkör-definíciókban használható erőforrás-szolgáltató műveleteinek beolvasása. |
> | Műveletek | Microsoft.Authorization/roleAssignments/delete | Szerepkör-hozzárendelés törlése a megadott hatókörből. |
> | Műveletek | Microsoft.Authorization/roleAssignments/read | Információk beolvasása a szerepkör-hozzárendelésről. |
> | Műveletek | Microsoft.Authorization/roleAssignments/write | Szerepkör-hozzárendelés létrehozása a megadott hatókörben. |
> | Műveletek | Microsoft.Authorization/roleDefinitions/delete | A megadott egyéni szerepkör-definíció törlése. |
> | Műveletek | Microsoft.Authorization/roleDefinitions/read | Szerepkör-definíció adatainak beolvasása. |
> | Műveletek | Microsoft.Authorization/roleDefinitions/write | Egy egyéni szerepkör-definíció létrehozása vagy módosítása a megadott engedélyekkel és hozzárendelhető hatókörökkel. |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | Olvassa el a regisztrációs adatokat egy Azure Automation DSC |
> | Műveletek | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | Azure Automation DSC kulcsok újragenerálása kérelem írási műveletek |
> | Műveletek | Microsoft.Automation/automationAccounts/certificates/delete | Egy Azure Automation szolgáltatásbeli tanúsítványeszköz törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/certificates/getCount/action | Olvassa be a tanúsítványok száma |
> | Műveletek | Microsoft.Automation/automationAccounts/certificates/read | Egy Azure Automation szolgáltatásbeli tanúsítványeszköz beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/certificates/write | Létrehozza vagy frissíti az Azure Automation szolgáltatásbeli tanúsítványeszköz |
> | Műveletek | Microsoft.Automation/automationAccounts/compilationjobs/read | Egy Azure Automation DSC-fordítási beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/compilationjobs/read | Egy Azure Automation DSC-fordítási beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/compilationjobs/write | Egy Azure Automation DSC-fordítási írási műveletek |
> | Műveletek | Microsoft.Automation/automationAccounts/compilationjobs/write | Egy Azure Automation DSC-fordítási írási műveletek |
> | Műveletek | Microsoft.Automation/automationAccounts/configurations/content/read | A konfigurációs hordozó tartalmának beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/configurations/delete | Egy Azure Automation DSC tartalmának törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/configurations/getCount/action | A szám egy Azure Automation DSC tartalmának beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/configurations/read | Egy Azure Automation DSC tartalmának beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/configurations/write | Egy Azure Automation DSC tartalmának írása |
> | Műveletek | Microsoft.Automation/automationAccounts/connections/delete | Azure Automation szolgáltatásbeli kapcsolódási eszköz törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/connections/getCount/action | Beolvassa a kapcsolatok száma |
> | Műveletek | Microsoft.Automation/automationAccounts/connections/read | Azure Automation szolgáltatásbeli kapcsolódási eszköz beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/connections/write | Létrehozza vagy frissíti az Azure Automation szolgáltatásbeli kapcsolódási eszköz |
> | Műveletek | Microsoft.Automation/automationAccounts/connectionTypes/delete | Egy Azure Automation szolgáltatásbeli kapcsolattípus-eszköz törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/connectionTypes/read | Egy Azure Automation szolgáltatásbeli kapcsolattípus-eszköz beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/connectionTypes/write | Egy Azure Automation szolgáltatásbeli kapcsolattípus-eszköz létrehozása |
> | Műveletek | Microsoft.Automation/automationAccounts/credentials/delete | Egy Azure Automation szolgáltatásbeli hitelesítőadat-eszköz törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/credentials/getCount/action | Olvassa be a hitelesítő adatok száma |
> | Műveletek | Microsoft.Automation/automationAccounts/credentials/read | Egy Azure Automation szolgáltatásbeli hitelesítőadat-eszköz beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/credentials/write | Létrehozza vagy frissíti egy Azure Automation szolgáltatásbeli hitelesítőadat-eszköz |
> | Műveletek | Microsoft.Automation/automationAccounts/delete | Egy Azure Automation-fiók törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/diagnosticSettings/write | Az erőforrás diagnosztikai beállításának |
> | Műveletek | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | Hibrid forgatókönyv-feldolgozó erőforrások törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Beolvassa a hibrid forgatókönyv-feldolgozó erőforrások |
> | Műveletek | Microsoft.Automation/automationAccounts/jobs/output/read | Egy feladat kimenetének beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/jobs/read | Egy Azure Automation-feladat beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/jobs/resume/action | Egy Azure Automation-feladat folytatása |
> | Műveletek | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | A feladat végrehajtásának időpontjában az Azure Automation-runbook tartalmának beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/jobs/stop/action | Egy Azure Automation-feladat leállítása |
> | Műveletek | Microsoft.Automation/automationAccounts/jobs/streams/read | Egy Azure Automation-feladatstream beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/jobs/streams/read | Egy Azure Automation-feladatstream beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/jobs/suspend/action | Egy Azure Automation-feladat felfüggesztése |
> | Műveletek | Microsoft.Automation/automationAccounts/jobs/write | Egy Azure Automation-feladat létrehozása |
> | Műveletek | Microsoft.Automation/automationAccounts/jobSchedules/delete | Egy Azure Automation-feladatütemezés törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/jobSchedules/read | Egy Azure Automation-feladatütemezés beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/jobSchedules/write | Egy Azure Automation-feladatütemezés létrehozása |
> | Műveletek | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Lekérdezi a munkaterület az automation-fiókhoz csatolva. |
> | Műveletek | Microsoft.Automation/automationAccounts/listKeys/action | Beolvassa a kulcsokat az automation-fiók |
> | Műveletek | Microsoft.Automation/automationAccounts/logDefinitions/read | Az automatizálási fiók naplók beolvasása. |
> | Műveletek | Microsoft.Automation/automationAccounts/modules/activities/read | Lekérdezi az Azure Automation-tevékenységek |
> | Műveletek | Microsoft.Automation/automationAccounts/modules/delete | Egy Azure Automation-modul törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/modules/getCount/action | Lekérdezi az Automation-fiók modulok száma |
> | Műveletek | Microsoft.Automation/automationAccounts/modules/read | Egy Azure Automation-modul beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/modules/write | Létrehozza vagy frissíti az Azure Automation-modul |
> | Műveletek | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | Egy Azure Automation DSC-csomópont-konfiguráció törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/nodeConfigurations/rawContent/action | Egy Azure Automation DSC-csomópont konfigurációjának tartalma beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/nodeConfigurations/read | Egy Azure Automation DSC-csomópont-konfiguráció beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/nodeConfigurations/write | Ír egy Azure Automation DSC-csomópont-konfiguráció |
> | Műveletek | Microsoft.Automation/automationAccounts/nodecounts/read | Beolvassa a megadott típus összesített száma |
> | Műveletek | Microsoft.Automation/automationAccounts/nodes/delete | Azure Automation DSC-csomópontok törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/nodes/read | Azure Automation DSC-csomópontok beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/nodes/reports/content/read | Azure Automation DSC-jelentés tartalmának beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/nodes/reports/read | Azure Automation DSC-jelentéseket beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/nodes/write | Létrehozza vagy frissíti az Azure Automation DSC-csomópontok |
> | Műveletek | Microsoft.Automation/automationAccounts/objectDataTypes/fields/read | Azure Automation szolgáltatásbeli TypeFields beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/providers/Microsoft.Insights/metricDefinitions/read | Automatizálási metrikai meghatározásainak beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/read | Egy Azure Automation-fiók beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/content/read | Egy Azure Automation-runbook tartalmának beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/delete | Egy Azure Automation-runbook törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | Egy Azure Automation-runbookvázlat tartalmának létrehozása |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | Azure Automation-runbook beolvasása vázlat a művelet eredménye |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/draft/publish/action | Egy Azure Automation-runbookvázlat közzététele |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/draft/read | Egy Azure Automation-runbookvázlat beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | Egy Azure Automation runbookvázlat-tesztelési feladat beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | Egy Azure Automation runbookvázlat-tesztelési feladat folytatása |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/stop/action | Egy Azure Automation runbookvázlat-tesztelési feladat leállítása |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | Egy Azure Automation runbookvázlat-tesztelési feladat felfüggesztése |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | Egy Azure Automation runbookvázlat-tesztelési feladat létrehozása |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | Egy Azure Automation-runbookvázlat szerkesztésének visszavonása |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/getCount/action | Azure Automation-runbook számának beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/read | Egy Azure Automation-runbook beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/runbooks/write | Létrehozza vagy frissíti az Azure Automation-runbook |
> | Műveletek | Microsoft.Automation/automationAccounts/schedules/delete | Egy Azure Automation szolgáltatásbeli ütemezéseszköz törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/schedules/getCount/action | Lekérdezi az Azure Automation ütemezések száma |
> | Műveletek | Microsoft.Automation/automationAccounts/schedules/read | Egy Azure Automation szolgáltatásbeli ütemezési eszköz beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/schedules/write | Létrehozza vagy frissíti az Azure Automation szolgáltatásbeli ütemezési eszköz |
> | Műveletek | Microsoft.Automation/automationAccounts/statistics/read | Azure Automation szolgáltatásbeli statisztika beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | Egy Azure Automation-frissítés központi telepítési gép beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | Egy Azure Automation frissítés felügyeleti javítás feladat beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/usages/read | Lekérdezi az Azure Automation-használat |
> | Műveletek | Microsoft.Automation/automationAccounts/variables/delete | Egy Azure Automation szolgáltatásbeli változóeszköz törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/variables/read | Egy Azure Automation szolgáltatásbeli változóeszköz beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/variables/write | Létrehozza vagy frissíti egy Azure Automation szolgáltatásbeli változóeszköz |
> | Műveletek | Microsoft.Automation/automationAccounts/watchers/delete | Egy Azure Automation-figyelő feladat törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/watchers/read | Egy Azure Automation-figyelő feladat beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/watchers/start/action | Egy Azure Automation-figyelő feladat indítása |
> | Műveletek | Microsoft.Automation/automationAccounts/watchers/stop/action | Egy Azure Automation-figyelő feladat leállítása |
> | Műveletek | Microsoft.Automation/automationAccounts/watchers/streams/read | Egy Azure Automation-megfigyelő feladatstream beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/watchers/watcherActions/delete | Egy Azure Automation-figyelő feladat műveletek törlése |
> | Műveletek | Microsoft.Automation/automationAccounts/watchers/watcherActions/read | Lekérdezi az Azure Automation-figyelőjét Feladatműveletek |
> | Műveletek | Microsoft.Automation/automationAccounts/watchers/watcherActions/write | Hozzon létre egy Azure Automation-figyelő feladat műveletek |
> | Műveletek | Microsoft.Automation/automationAccounts/watchers/write | Létrehoz egy Azure Automation-figyelő feladat |
> | Műveletek | Microsoft.Automation/automationAccounts/webhooks/action | URI generálása Azure Automation-webhook |
> | Műveletek | Microsoft.Automation/automationAccounts/webhooks/delete | Egy Azure Automation-webhook törlése  |
> | Műveletek | Microsoft.Automation/automationAccounts/webhooks/read | Egy Azure Automation-webhook beolvasása |
> | Műveletek | Microsoft.Automation/automationAccounts/webhooks/write | Létrehozza vagy frissíti az Azure Automation-webhook |
> | Műveletek | Microsoft.Automation/automationAccounts/write | Létrehozza vagy frissíti az Azure Automation-fiók |
> | Műveletek | Microsoft.Automation/operations/read | Azure Automation-erőforrások elérhető műveletek beolvasása |
> | Műveletek | Microsoft.Automation/register/action | Az Azure Automation előfizetés regisztrálása |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.AzureActiveDirectory/b2cDirectories/delete | B2C-címtárerőforrás törlése |
> | Műveletek | Microsoft.AzureActiveDirectory/b2cDirectories/read | B2C-címtárerőforrás megtekintése |
> | Műveletek | Microsoft.AzureActiveDirectory/b2cDirectories/write | B2C-címtárerőforrás létrehozása vagy frissítése |
> | Műveletek | Microsoft.AzureActiveDirectory/operations/read | A Microsoft.AzureActiveDirectory erőforrás-szolgáltatóhoz választható összes API-művelet olvasása |
> | Műveletek | Microsoft.AzureActiveDirectory/register/action | A Microsoft.AzureActiveDirectory erőforrás-szolgáltatóhoz tartozó előfizetés regisztrálása |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.AzureStack/Operations/read | Egy erőforrás-szolgáltató művelet tulajdonságait olvassa be |
> | Műveletek | Microsoft.AzureStack/register/action | Az előfizetés regisztrálása a Microsoft.AzureStack erőforrás-szolgáltató |
> | Műveletek | Microsoft.AzureStack/registrations/customerSubscriptions/delete | Egy Azure verem ügyfél-előfizetést törlése |
> | Műveletek | Microsoft.AzureStack/registrations/customerSubscriptions/read | Az Azure-verem felhasználói előfizetés tulajdonságainak beolvasása |
> | Műveletek | Microsoft.AzureStack/registrations/customerSubscriptions/write | Létrehozza vagy frissíti az Azure-verem felhasználói előfizetéssel |
> | Műveletek | Microsoft.AzureStack/registrations/delete | Egy Azure verem regisztráció törlése |
> | Műveletek | Microsoft.AzureStack/registrations/getActivationKey/action | A legújabb Azure verem használható aktiválási kulcs beolvasása |
> | Műveletek | Microsoft.AzureStack/registrations/products/listDetails/action | Lekéri a kiterjesztett egy Azure verem piactér megoldás részletei |
> | Műveletek | Microsoft.AzureStack/registrations/products/read | Egy Azure verem piactér termék tulajdonságainak beolvasása |
> | Műveletek | Microsoft.AzureStack/registrations/read | Egy Azure verem regisztrációs tulajdonságait olvassa be |
> | Műveletek | Microsoft.AzureStack/registrations/write | Létrehozza vagy frissíti az Azure-verem regisztrációs |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Batch/batchAccounts/applications/delete | Az alkalmazás törlése |
> | Műveletek | Microsoft.Batch/batchAccounts/applications/read | Megjeleníti azokat alkalmazásokat, vagy az alkalmazás tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Batch/batchAccounts/applications/versions/activate/action | Az alkalmazáscsomag aktiválása |
> | Műveletek | Microsoft.Batch/batchAccounts/applications/versions/delete | Az alkalmazáscsomag törlése |
> | Műveletek | Microsoft.Batch/batchAccounts/applications/versions/read | Alkalmazáscsomag tulajdonságait olvassa be |
> | Műveletek | Microsoft.Batch/batchAccounts/applications/versions/write | Létrehoz egy új alkalmazás-csomagot, vagy frissíti a meglévő alkalmazáscsomag |
> | Műveletek | Microsoft.Batch/batchAccounts/applications/write | Létrehoz egy új alkalmazást, vagy egy meglévő alkalmazás frissítése |
> | Műveletek | Microsoft.Batch/batchAccounts/certificateOperationResults/read | Batch-fiók tanúsítvány egy hosszú ideig futó művelet eredményének beolvasása |
> | Műveletek | Microsoft.Batch/batchAccounts/certificates/cancelDelete/action | A tanúsítvány a Batch-fiók törlésére tett sikertelen kísérlet megszakítása |
> | Műveletek | Microsoft.Batch/batchAccounts/certificates/delete | Töröl egy tanúsítványt a Batch-fiókból |
> | Műveletek | Microsoft.Batch/batchAccounts/certificates/read | A Batch-fiók-tanúsítványokat sorolja fel, vagy a tanúsítvány tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Batch/batchAccounts/certificates/write | Létrehoz egy új tanúsítványt a Batch-fiók, vagy egy meglévő tanúsítvány frissítése |
> | Műveletek | Microsoft.Batch/batchAccounts/delete | Batch-fiók törlése |
> | Műveletek | Microsoft.Batch/batchAccounts/listkeys/action | Listák hívóbetűk Batch-fiókhoz |
> | Műveletek | Microsoft.Batch/batchAccounts/operationResults/read | A Batch-fiók hosszú ideig futó művelet eredményének beolvasása |
> | Műveletek | Microsoft.Batch/batchAccounts/poolOperationResults/read | Batch-fiók készlet egy hosszú ideig futó művelet eredményének beolvasása |
> | Műveletek | Microsoft.Batch/batchAccounts/pools/delete | Egy készlet törlése Batch-fiók |
> | Műveletek | Microsoft.Batch/batchAccounts/pools/disableAutoscale/action | A Batch-fiók készlet automatikus skálázás letiltása |
> | Műveletek | Microsoft.Batch/batchAccounts/pools/read | A Batch-fiók készletek sorolja fel, vagy a készlet tulajdonságait olvassa be |
> | Műveletek | Microsoft.Batch/batchAccounts/pools/stopResize/action | Leállítja egy folyamatban lévő műveletet a Batch-fiók készlet átméretezése |
> | Műveletek | Microsoft.Batch/batchAccounts/pools/upgradeOs/action | Frissíti az operációs rendszert, a Batch-fiók címkészlet |
> | Műveletek | Microsoft.Batch/batchAccounts/pools/write | Új készlet létrehozása a Batch-fiók, vagy egy meglévő készlet frissítése |
> | Műveletek | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/logDefinitions/read | A Batch szolgáltatás naplók beolvasása. |
> | Műveletek | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/metricDefinitions/read | A Batch szolgáltatás elérhető metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.Batch/batchAccounts/read | Batch-fiókok listája, vagy a Batch-fiók tulajdonságait olvassa be |
> | Műveletek | Microsoft.Batch/batchAccounts/regeneratekeys/action | A Batch-fiókhoz hívóbetűk újragenerálása |
> | Műveletek | Microsoft.Batch/batchAccounts/syncAutoStorageKeys/action | Szinkronizálja a Batch-fiók beállítása automatikus tárfiók hozzáférési kulcsainak listázása |
> | Műveletek | Microsoft.Batch/batchAccounts/write | Új Batch-fiók létrehozása vagy frissítése egy meglévő Batch-fiók |
> | Műveletek | Microsoft.Batch/locations/checkNameAvailability/action | Ellenőrzi, hogy a fiók neve érvényes, és nincs használatban. |
> | Műveletek | Microsoft.Batch/locations/quotas/read | A megadott Azure-régiót kötegelt a megadott előfizetés kvótájának beolvasása |
> | Műveletek | Microsoft.Batch/register/action | A kötegelt erőforrás-szolgáltató előfizetésének regisztrálása, és lehetővé teszi, hogy a Batch-fiókok létrehozása |
> | Műveletek | Microsoft.Batch/unregister/action | A Batch-fiókok megelőzése kötegelt erőforrás-szolgáltató az előfizetés regisztrációjának törlése |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.BatchAI/clusters/delete | A kötegelt AI fürt törlése |
> | Műveletek | Microsoft.BatchAI/clusters/read | Felsorolja a kötegelt AI fürtök vagy egy kötegelt AI fürt tulajdonságait olvassa be |
> | Műveletek | Microsoft.BatchAI/clusters/remoteLoginInformation/action | Távoli-bejelentkezési adatok kötegelt AI fürt listája |
> | Műveletek | Microsoft.BatchAI/clusters/write | Egy új kötegelt AI fürtöt hoz létre, vagy kötegelt AI meglévő fürt frissítése |
> | Műveletek | Microsoft.BatchAI/fileservers/delete | A kötegelt AI fájlkiszolgáló törlése |
> | Műveletek | Microsoft.BatchAI/fileservers/read | Kötegelt AI fileservers sorolja fel, vagy egy kötegelt AI fájlkiszolgálóhoz tulajdonságait olvassa be |
> | Műveletek | Microsoft.BatchAI/fileservers/resume/action | A kötegelt AI fájlkiszolgáló folytatása |
> | Műveletek | Microsoft.BatchAI/fileservers/suspend/action | A kötegelt AI fájlkiszolgáló felfüggesztése |
> | Műveletek | Microsoft.BatchAI/fileservers/write | Egy új kötegelt AI fájlkiszolgálóhoz létrehoz vagy frissít egy meglévő kötegelt AI fájlkiszolgáló |
> | Műveletek | Microsoft.BatchAI/jobs/delete | A kötegelt AI feladatok törlése |
> | Műveletek | Microsoft.BatchAI/jobs/read | Felsorolja a kötegelt AI feladatokat, vagy egy kötegelt AI feladat tulajdonságait olvassa be |
> | Műveletek | Microsoft.BatchAI/jobs/remoteLoginInformation/action | Távoli-bejelentkezési adatok kötegelt AI feladat listája |
> | Műveletek | Microsoft.BatchAI/jobs/terminate/action | A kötegelt AI feladat leállítása |
> | Műveletek | Microsoft.BatchAI/jobs/write | Létrehoz egy új kötegelt AI feladatot, vagy egy meglévő kötegelt AI feladat frissítése |
> | Műveletek | Microsoft.BatchAI/register/action | A kötegelt AI erőforrás-szolgáltató előfizetésének regisztrálása és létrehozását lehetővé tevő kötegelt AI erőforrások |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Billing/billingPeriods/read | Megjeleníti az elérhető elszámolási időszakok |
> | Műveletek | Microsoft.Billing/invoices/read | Rendelkezésre álló listák számlák |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.BingMaps/mapApis/Delete | Törlési művelet |
> | Műveletek | Microsoft.BingMaps/mapApis/listSecrets/action | A titkos kulcsok listázása |
> | Műveletek | Microsoft.BingMaps/mapApis/listSingleSignOnToken/action | Az erőforráshoz tartozó egyszeri bejelentkezési engedélyezési jogkivonat beolvasása |
> | Műveletek | Microsoft.BingMaps/mapApis/Read | Olvasási művelet |
> | Műveletek | Microsoft.BingMaps/mapApis/regenerateKey/action | A kulcs újragenerálása |
> | Műveletek | Microsoft.BingMaps/mapApis/Write | Írási művelet |
> | Műveletek | Microsoft.BingMaps/Operations/read | A művelet leírását. |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Cache/checknameavailability/action | Annak ellenőrzése, hogy a név rendelkezésre áll-e egy új Redis Cache-hez való használatra |
> | Műveletek | Microsoft.Cache/locations/operationresults/read | Egy olyan hosszú ideje futó művelet eredményének beolvasása, amelynek esetében a Location (Hely) fejléc vissza lett küldve az ügyfélnek |
> | Műveletek | Microsoft.Cache/operations/read | A Microsoft.Cache szolgáltató által támogatott műveletek listázása. |
> | Műveletek | Microsoft.Cache/redis/delete | Teljes Redis gyorsítótár törlése |
> | Műveletek | Microsoft.Cache/redis/export/action | Redis-adatok exportálása előre megadott tárolási blobokba meghatározott formátumban |
> | Műveletek | Microsoft.Cache/redis/firewallRules/delete | Egy Redis Cache IP-tűzfalszabályainak törlése |
> | Műveletek | Microsoft.Cache/redis/firewallRules/read | Egy Redis Cache IP-tűzfalszabályainak beolvasása |
> | Műveletek | Microsoft.Cache/redis/firewallRules/write | Egy Redis Cache IP-tűzfalszabályainak szerkesztése |
> | Műveletek | Microsoft.Cache/redis/forceReboot/action | Egy gyorsítótárpéldány kényszerített újraindítása, mely adatvesztést okozhat. |
> | Műveletek | Microsoft.Cache/redis/import/action | Meghatározott formátumú adatok importálása a Redis szolgáltatásba több blobból |
> | Műveletek | Microsoft.Cache/redis/linkedservers/delete | Egy Redis gyorsítótárhoz csatolt kiszolgáló törlése |
> | Műveletek | Microsoft.Cache/redis/linkedservers/read | Egy Redis gyorsítótárhoz hozzárendelt csatolt kiszolgálók beolvasása. |
> | Műveletek | Microsoft.Cache/redis/linkedservers/write | Csatolt kiszolgáló hozzáadása egy Redis gyorsítótárhoz |
> | Műveletek | Microsoft.Cache/redis/listKeys/action | Redis gyorsítótár elérésikulcs-értékeinek megtekintése a felügyeleti portálon |
> | Műveletek | Microsoft.Cache/redis/listUpgradeNotifications/read | A gyorsítótár bérlője legutóbbi csomagváltási értesítéseinek listázása. |
> | Műveletek | Microsoft.Cache/redis/metricDefinitions/read | Egy Redis gyorsítótárhoz elérhető metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.Cache/redis/patchSchedules/delete | Egy Redis Cache javítási ütemezésének törlése |
> | Műveletek | Microsoft.Cache/redis/patchSchedules/read | Egy Redis Cache javítási ütemezésének beolvasása |
> | Műveletek | Microsoft.Cache/redis/patchSchedules/write | Egy Redis Cache javítási ütemezésének módosítása |
> | Műveletek | Microsoft.Cache/redis/read | Redis gyorsítótár beállításainak és konfigurációjának megtekintése a felügyeleti portálon |
> | Műveletek | Microsoft.Cache/redis/regenerateKey/action | Redis gyorsítótár elérésikulcs-értékeinek módosítása a felügyeleti portálon |
> | Műveletek | Microsoft.Cache/redis/start/action | Gyorsítótárpéldány elindítása. |
> | Műveletek | Microsoft.Cache/redis/stop/action | Gyorsítótárpéldány leállítása. |
> | Műveletek | Microsoft.Cache/redis/write | Redis gyorsítótár beállításainak és konfigurációjának módosítása a felügyeleti portálon |
> | Műveletek | Microsoft.Cache/register/action | A Microsoft.Cache erőforrás-szolgáltató regisztrálása egy előfizetéshez |
> | Műveletek | Microsoft.Cache/unregister/action | A Microsoft.Cache erőforrás-szolgáltató egy előfizetéshez tartozó regisztrációjának törlése |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Capacity/register/action | A kapacitás erőforrás-szolgáltató regisztrálja, és lehetővé teszi a kapacitás erőforrások létrehozását. |
> | Műveletek | Microsoft.Capacity/reservationorders/action | Bármely foglalásának frissítése |
> | Műveletek | Microsoft.Capacity/reservationorders/delete | A foglalás törlése |
> | Műveletek | Microsoft.Capacity/reservationorders/read | Olvassa el az összes foglalások |
> | Műveletek | Microsoft.Capacity/reservationorders/reservations/action | Bármely foglalásának frissítése |
> | Műveletek | Microsoft.Capacity/reservationorders/reservations/delete | A foglalás törlése |
> | Műveletek | Microsoft.Capacity/reservationorders/reservations/read | Olvassa el az összes foglalások |
> | Műveletek | Microsoft.Capacity/reservationorders/reservations/revisions/read | Olvassa el az összes foglalások |
> | Műveletek | Microsoft.Capacity/reservationorders/reservations/write | A foglalás létrehozása |
> | Műveletek | Microsoft.Capacity/reservationorders/write | A foglalás létrehozása |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Cdn/CheckNameAvailability/action |  |
> | Műveletek | Microsoft.Cdn/CheckResourceUsage/action |  |
> | Műveletek | Microsoft.Cdn/edgenodes/delete |  |
> | Műveletek | Microsoft.Cdn/edgenodes/read |  |
> | Műveletek | Microsoft.Cdn/edgenodes/write |  |
> | Műveletek | Microsoft.Cdn/operationresults/delete |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/CheckResourceUsage/action |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/delete |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/endpointresults/CheckResourceUsage/action |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/delete |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/action |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/action |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/read |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/write |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/endpointresults/delete |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/endpointresults/Load/action |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/delete |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/read |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/write |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/endpointresults/Purge/action |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/endpointresults/read |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/endpointresults/Start/action |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/endpointresults/Stop/action |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/endpointresults/ValidateCustomDomain/action |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/endpointresults/write |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/GenerateSsoUri/action |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/GetSupportedOptimizationTypes/action |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/read |  |
> | Műveletek | Microsoft.Cdn/operationresults/profileresults/write |  |
> | Műveletek | Microsoft.Cdn/operationresults/read |  |
> | Műveletek | Microsoft.Cdn/operationresults/write |  |
> | Műveletek | Microsoft.Cdn/operations/read |  |
> | Műveletek | Microsoft.Cdn/profiles/CheckResourceUsage/action |  |
> | Műveletek | Microsoft.Cdn/profiles/delete |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/CheckResourceUsage/action |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/customdomains/delete |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/customdomains/DisableCustomHttps/action |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/customdomains/EnableCustomHttps/action |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/customdomains/read |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/customdomains/write |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/delete |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/Load/action |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/origins/delete |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/origins/read |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/origins/write |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti az erőforrás diagnosztikai beállításait |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/providers/Microsoft.Insights/logDefinitions/read | Microsoft.Cdn naplók beolvasása. |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/Purge/action |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/read |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/Start/action |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/Stop/action |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/ValidateCustomDomain/action |  |
> | Műveletek | Microsoft.Cdn/profiles/endpoints/write |  |
> | Műveletek | Microsoft.Cdn/profiles/GenerateSsoUri/action |  |
> | Műveletek | Microsoft.Cdn/profiles/GetSupportedOptimizationTypes/action |  |
> | Műveletek | Microsoft.Cdn/profiles/read |  |
> | Műveletek | Microsoft.Cdn/profiles/write |  |
> | Műveletek | Microsoft.Cdn/register/action | Az előfizetés regisztrálása a CDN erőforrás-szolgáltatónál, és a CDN-profilok létrehozásának engedélyezése. |
> | Műveletek | Microsoft.Cdn/ValidateProbe/action |  |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/certificates/Delete | Meglévő tanúsítvány törlése |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/certificates/Read | A tanúsítványok listájának lekérdezése |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/certificates/Write | Adja hozzá az új tanúsítványt, vagy egy meglévő frissítése |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/Delete | Egy meglévő AppServiceCertificate törlése |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/Read | A CertificateOrders listájának lekérdezése |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | Hajtsa végre újból egy meglévő certificateorder |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/renew/Action | Egy meglévő certificateorder megújítása |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | Tanúsítvány e-mail újraküldése |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Küldje el újra a kérelmet e-mailt egy másik e-mail címet |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | App Service kiállított tanúsítvány hely lezárása beolvasása |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | A tanúsítvány műveletek listájának beolvasása |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | Tanúsítvány e-mail előzmények beolvasása |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | Tartomány tulajdonjogának ellenőrzése |
> | Műveletek | Microsoft.CertificateRegistration/certificateOrders/Write | Adja hozzá egy új certificateOrder vagy egy meglévő frissítése |
> | Műveletek | Microsoft.CertificateRegistration/operations/Read | Lista összes műveleteket az app service-tanúsítvány regisztrálása |
> | Műveletek | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | Az alkalmazás egyszerű kiépítés egyszerű szolgáltatásnév |
> | Műveletek | Microsoft.CertificateRegistration/register/action | Az előfizetés a Microsoft Certificates erőforrás-szolgáltató regisztrálása |
> | Műveletek | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | Ellenőrizze a beszerzési tanúsítványobjektum lefolytatása nélkül |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ClassicCompute/capabilities/read | Képességek megjelenítése |
> | Műveletek | Microsoft.ClassicCompute/checkDomainNameAvailability/action | Az adott tartománynév elérhetőségének ellenőrzése. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/active/write | Az aktív tartománynév beállítása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/availabilitySets/read | Az erőforrás rendelkezésre állási csoportjának megjelenítése. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/capabilities/read | A tartománynév-képességek megjelenítése |
> | Műveletek | Microsoft.ClassicCompute/domainNames/delete | Erőforrások tartománynevének eltávolítása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/extensions/delete | A tartománynév-kiterjesztések eltávolítása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/extensions/operationStatuses/read | A tartománynév-kiterjesztések műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/extensions/read | A tartománynév-kiterjesztéseket adja vissza. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/extensions/write | Tartománynév-kiterjesztések hozzáadása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/delete | Új belső terheléselosztás eltávolítása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | A tartománynevek belső terheléselosztói műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/read | A belső terheléselosztók beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/write | Új belső terheléselosztás létrehozása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | A tartománynevek elosztott terhelésű végpontcsoportjai műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/read | Az elosztott terhelésű végpontcsoportok megjelenítése |
> | Műveletek | Microsoft.ClassicCompute/domainNames/read | Erőforrások tartománynevének visszaadása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/serviceCertificates/delete | Az alkalmazott szolgáltatási tanúsítványok törlése. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | A tartománynevek szolgáltatási tanúsítványai műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | Az alkalmazott szolgáltatási tanúsítványok visszaadása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/serviceCertificates/write | Alkalmazott szolgáltatási tanúsítványok hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/delete | A megadott üzembe helyezési pont törlése. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | A tartománynevek üzembe helyezési pontjai műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/read | Üzembe helyezési pontok megjelenítése. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | Eltávolítja az üzembe helyezési ponti szerepkör kiterjesztéshivatkozását. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | A tartománynevek üzembe helyezési ponti szerepkörei kiterjesztéshivatkozásai műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | Az üzembe helyezési ponti szerepkör kiterjesztéshivatkozását adja vissza. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | Az üzembe helyezési ponti szerepkör kiterjesztéshivatkozásának hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read | Diagnosztikai beállítások beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write | Diagnosztikai beállítások hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | A metrikai meghatározások beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/read | Üzembe helyezési pont szerepkörének beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | A tartománynevek üzembe helyezési ponti szerepkörei szerepkörpéldányai műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | Szerepkörpéldány beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | A szerepkörpéldány újraépítése. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | Szerepkörpéldány rendszerképének alaphelyzetbe állítása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | A szerepkörpéldányok újraindítása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/start/action | Üzembe helyezési pont indítása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/state/start/write | Az üzembe helyezési pont állapotának leállítottra állítása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/state/stop/write | Az üzembe helyezési pont állapotának elindítottra állítása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/stop/action | Üzembe helyezési pont felfüggesztése. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/upgradeDomain/write | Verzióváltási tartomány feldolgozása. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/slots/write | Telepítés létrehozása vagy frissítése. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/swap/action | Előkészítési pont cseréje üzemelési pontra. |
> | Műveletek | Microsoft.ClassicCompute/domainNames/write | Erőforrások tartománynevének hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.ClassicCompute/moveSubscriptionResources/action | Minden hagyományos erőforrás áthelyezése egy másik előfizetésbe. |
> | Műveletek | Microsoft.ClassicCompute/operatingSystemFamilies/read | Ez a művelet a Microsoft Azure-ban elérhető vendég operációsrendszer-családokat, valamint az egyes családokon belül elérhető operációsrendszer-verziókat listázza. |
> | Műveletek | Microsoft.ClassicCompute/operatingSystems/read | Ez a művelet a Microsoft Azure-ban jelenleg elérhető vendég operációsrendszer-verziókat listázza. |
> | Műveletek | Microsoft.ClassicCompute/quotas/read | Az előfizetés kvótájának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/register/action | Regisztrálás a Classic Compute szolgáltatónál |
> | Műveletek | Microsoft.ClassicCompute/resourceTypes/skus/read | Lekérdezi a támogatott erőforrástípusok termékváltozat-listáját. |
> | Műveletek | Microsoft.ClassicCompute/validateSubscriptionMoveAvailability/action | Annak ellenőrzése, hogy áthelyezhető-e az előfizetés hagyományos áthelyezéssel. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | A virtuális géphez társított hálózati biztonsági csoport törlése. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | A virtuális gépek társított hálózati biztonsági csoportjai műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | A virtuális géphez társított hálózati biztonsági csoport lekérése. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | A virtuális géphez társított hálózati biztonsági csoport hozzáadása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/asyncOperations/read | Lehetséges aszinkron műveletek beolvasása |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/attachDisk/action | Adatlemez csatlakoztatása a virtuális géphez. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/delete | Virtuális gépek eltávolítása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/detachDisk/action | Adatlemez leválasztása a virtuális gépről. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/disks/read | Adatlemezek listájának beolvasása |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/action | Virtuális gép RDP-fájljának letöltése. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | A virtuálisgép-bővítmények műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/extensions/read | Virtuálisgép-bővítmény beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/extensions/write | Virtuálisgép-bővítmény hozzáadása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/metrics/read | Metrika beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | A hálózati interfészhez társított hálózati biztonsági csoport törlése. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | A virtuális gépek társított hálózati biztonsági csoportjai műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | A hálózati interfészhez társított hálózati biztonsági csoport lekérése. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | A hálózati interfészhez társított hálózati biztonsági csoport hozzáadása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | A virtuális gépek műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/performMaintenance/action | A művelet elvégzi a virtuális gép karbantartását. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | Diagnosztikai beállítások beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write | Diagnosztikai beállítások hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | A metrikai meghatározások beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/read | Virtuális gépek listájának beolvasása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/redeploy/action | A virtuális gép ismételt üzembe helyezése. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/restart/action | Virtuális gépek újraindítása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/shutdown/action | Virtuális gép rendszerének leállítása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/start/action | Virtuális gép elindítása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/stop/action | Virtuális gép leállítása. |
> | Műveletek | Microsoft.ClassicCompute/virtualMachines/write | Virtuális gépek hozzáadása vagy módosítása. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ClassicNetwork/gatewaySupportedDevices/read | Támogatott eszközök listájának beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/networkSecurityGroups/delete | A hálózati biztonsági csoport törlése. |
> | Műveletek | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | A hálózati biztonsági csoport műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/networkSecurityGroups/read | A hálózati biztonsági csoport lekérése. |
> | Műveletek | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | A biztonsági szabály törlése. |
> | Műveletek | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | A hálózati biztonsági csoport biztonsági szabályai műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | A biztonsági szabály beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | Egy biztonsági szabály hozzáadása vagy frissítése. |
> | Műveletek | Microsoft.ClassicNetwork/networkSecurityGroups/write | Új hálózati biztonsági csoport hozzáadása. |
> | Műveletek | Microsoft.ClassicNetwork/quotas/read | Az előfizetés kvótájának beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/register/action | Regisztrálás a Classic Network szolgáltatónál |
> | Műveletek | Microsoft.ClassicNetwork/reservedIps/delete | Fenntartott IP-cím törlése. |
> | Műveletek | Microsoft.ClassicNetwork/reservedIps/join/action | Csatlakozás egy fenntartott IP-címhez |
> | Műveletek | Microsoft.ClassicNetwork/reservedIps/link/action | Egy fenntartott IP-cím hivatkozása |
> | Műveletek | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | A fenntartott IP-címek műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/reservedIps/read | A fenntartott IP-címek beolvasása |
> | Műveletek | Microsoft.ClassicNetwork/reservedIps/write | Új fenntartott IP-cím felvétele |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/capabilities/read | Képességek megjelenítése |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/checkIPAddressAvailability/action | Az adott IP-cím virtuális hálózaton belüli elérhetőségét ellenőrzi. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/delete | A virtuális hálózat törlése. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/delete | Az ügyféltanúsítvány visszavonásának megszüntetése. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/read | Visszavont ügyféltanúsítványok beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/write | Ügyféltanúsítvány visszavonása. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | A virtuális hálózati átjáró ügyféltanúsítványának törlése. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/download/action | Tanúsítvány letöltése ujjlenyomat útján. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | A virtuális hálózati átjáró tanúsítványcsomagjának listázása. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | Ügyfél főtanúsítványainak megkeresése. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | Ügyfél új főtanúsítványának feltöltése. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/connect/action | Webhelyek közötti átjárókapcsolat létesítése. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/disconnect/action | Webhelyek közötti átjárókapcsolat leválasztása. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/read | Kapcsolatok listájának beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/test/action | Webhelyek közötti átjárókapcsolat tesztelése. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/delete | Virtuális hálózati átjáró törlése. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/action | Eszközkonfigurációs parancsfájl letöltése. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDiagnostics/action | Átjáródiagnosztika letöltése. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/action | Kör szolgáltatáskulcsának beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/listPackage/action | A virtuális hálózati átjárócsomag listázása. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/operationStatuses/read | A virtuális hálózati átjárók műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/packages/read | A virtuális hálózati átjárócsomag beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/read | Virtuális hálózati átjárók beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/startDiagnostics/action | Virtuális hálózati átjáró diagnosztikájának indítása. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/action | Virtuális hálózati átjáró diagnosztikájának leállítása. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/gateways/write | Virtuális hálózati átjáró hozzáadása. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/join/action | Csatlakozás a virtuális hálózathoz. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | A virtuális hálózatok műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/peer/action | Társviszony-létesítés két virtuális hálózat között. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/read | Virtuális hálózat beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | Az alhálózathoz társított hálózati biztonsági csoport törlése. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | A virtuális hálózat alhálózatához társított hálózati biztonsági csoport műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | Az alhálózathoz társított hálózati biztonsági csoport lekérése. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | Az alhálózathoz társított hálózati biztonsági csoport hozzáadása. |
> | Műveletek | Microsoft.ClassicNetwork/virtualNetworks/write | Új virtuális hálózat hozzáadása. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ClassicStorage/capabilities/read | Képességek megjelenítése |
> | Műveletek | Microsoft.ClassicStorage/checkStorageAccountAvailability/action | A tárfiók elérhetőségének ellenőrzése. |
> | Műveletek | Microsoft.ClassicStorage/disks/read | A tárfióklemez beolvasása. |
> | Műveletek | Microsoft.ClassicStorage/images/read | A lemezkép visszaadása. |
> | Műveletek | Microsoft.ClassicStorage/osImages/read | A operációs rendszerkép beolvasása. |
> | Műveletek | Microsoft.ClassicStorage/publicImages/read | A nyilvános virtuális gép lemezképének beolvasása. |
> | Műveletek | Microsoft.ClassicStorage/quotas/read | Az előfizetés kvótájának beolvasása. |
> | Műveletek | Microsoft.ClassicStorage/register/action | Regisztrálás egy hagyományos tárolóba. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/delete | Tárfiók törlése. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/disks/delete | A megadott tárfióklemez törlése. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | Az erőforrás műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/disks/read | A tárfióklemez beolvasása. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/disks/write | Új tárfióklemez felvétele. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/images/delete | A megadott tárfióklemezkép törlése. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/images/read | A tárfióklemezkép beolvasása. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Tárfiókok elérési kulcsainak listázása. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | Az erőforrás műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/osImages/delete | A megadott tárfiók operációsrendszer-lemezképének törlése. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/osImages/read | A tárfiók operációsrendszer-lemezképének visszaadása. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/read | Tárfiók visszaadása az adott fiókhoz. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/regenerateKey/action | Tárfiók meglévő elérési kulcsainak újragenerálása. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | Diagnosztikai beállítások beolvasása. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | Diagnosztikai beállítások hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/services/metricDefinitions/read | A metrikai meghatározások beolvasása. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/services/metrics/read | Metrika beolvasása. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/services/read | Elérhető szolgáltatások beolvasása. |
> | Műveletek | Microsoft.ClassicStorage/storageAccounts/write | Új tárfiók hozzáadása. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.CognitiveServices/accounts/delete | API-fiókok törlése |
> | Műveletek | Microsoft.CognitiveServices/accounts/listKeys/action | Kulcsok listázása |
> | Műveletek | Microsoft.CognitiveServices/accounts/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításának beolvasása. |
> | Műveletek | Microsoft.CognitiveServices/accounts/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése. |
> | Műveletek | Microsoft.CognitiveServices/accounts/providers/Microsoft.Insights/metricDefinitions/read | A Cognitive Serviceshez elérhető metrikák beolvasása. |
> | Műveletek | Microsoft.CognitiveServices/accounts/read | API-fiókok beolvasása. |
> | Műveletek | Microsoft.CognitiveServices/accounts/regenerateKey/action | Kulcs újragenerálása |
> | Műveletek | Microsoft.CognitiveServices/accounts/skus/read | Az egy adott meglévő erőforráshoz rendelkezésre álló termékváltozatok beolvasása. |
> | Műveletek | Microsoft.CognitiveServices/accounts/usages/read | Egy meglévő erőforrás kvótahasználatának beolvasása. |
> | Műveletek | Microsoft.CognitiveServices/accounts/write | API-fiókok írása. |
> | Műveletek | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Egy előfizetéshez elérhető termékváltozatok beolvasása. |
> | Műveletek | Microsoft.CognitiveServices/Operations/read | Az összes elérhető művelet listázása |
> | Műveletek | Microsoft.CognitiveServices/register/action | Cognitive Services-előfizetés regisztrálása |
> | Műveletek | Microsoft.CognitiveServices/skus/read | A rendelkezésre álló termékváltozatok beolvasása a Cognitive Services számára. |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Commerce/RateCard/read | Értéket ad vissza adatokat, Erőforrás-kijelző metaadatok és az adott előfizetéshez tartozó díjszabás kínálnak. |
> | Műveletek | Microsoft.Commerce/UsageAggregates/read | Lekéri a Microsoft Azure használat előfizetés. Az eredmény összesítések tartalmaz a kapcsolódó információk, egy adott időtartományt a használati adatok, az előfizetés és az erőforrás. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Compute/availabilitySets/delete | Törli a rendelkezésre állási csoportot |
> | Műveletek | Microsoft.Compute/availabilitySets/read | Egy adott rendelkezésre állási csoport tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/availabilitySets/vmSizes/read | A virtuális gépek a rendelkezésre állási csoportban való létrehozásához vagy frissítéséhez használható méretek listázása |
> | Műveletek | Microsoft.Compute/availabilitySets/write | Létrehoz egy új vagy frissít egy meglévő rendelkezésre állási csoportot |
> | Műveletek | Microsoft.Compute/disks/beginGetAccess/action | Lemez SAS URI-jának beolvasása blobhozzáféréshez |
> | Műveletek | Microsoft.Compute/disks/delete | A lemez törlése |
> | Műveletek | Microsoft.Compute/disks/endGetAccess/action | A lemez SAS URI-jának visszavonása |
> | Műveletek | Microsoft.Compute/disks/read | A lemez tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/disks/write | Új lemez létrehozása vagy meglévő lemez frissítése |
> | Műveletek | Microsoft.Compute/images/delete | A lemezkép törlése |
> | Műveletek | Microsoft.Compute/images/read | A lemezkép tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/images/write | Új lemezkép létrehozása vagy meglévő frissítése |
> | Műveletek | Microsoft.Compute/locations/capsOperations/read | Aszinkron Caps művelet állapotának beolvasása |
> | Műveletek | Microsoft.Compute/locations/diskOperations/read | Lemez aszinkron művelet állapotát olvassa be |
> | Műveletek | Microsoft.Compute/locations/operations/read | Egy aszinkron művelet állapotát olvassa be |
> | Műveletek | Microsoft.Compute/locations/publishers/artifacttypes/offers/read | Platformlemezképet tartalmazó ajánlat tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | Platformlemezképet tartalmazó termékváltozat tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | Platformlemezkép-verzió tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/locations/publishers/artifacttypes/types/read | VM-bővítmény-típus tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/locations/publishers/artifacttypes/types/versions/read | VM-bővítmény-verzió tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/locations/publishers/read | Gyártó tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/locations/runCommands/read | A helyen elérhető futtatási parancsok listázása |
> | Műveletek | Microsoft.Compute/locations/usages/read | Az előfizetés adott helyen lévő számítási erőforrásaihoz tartozó szolgáltatások korlátait és az aktuális felhasználási mennyiségeket olvassa be |
> | Műveletek | Microsoft.Compute/locations/vmSizes/read | Listázza az adott helyen elérhető virtuálisgép-méreteket |
> | Műveletek | Microsoft.Compute/operations/read | A Microsoft.Compute erőforrás-szolgáltató elérhető műveleteinek listázása |
> | Műveletek | Microsoft.Compute/register/action | Az előfizetés regisztrálása a Microsoft.Compute erőforrás-szolgáltatónál |
> | Műveletek | Microsoft.Compute/restorePointCollections/delete | A visszaállításipont-gyűjtemény és az abban található visszaállítási pontok törlése |
> | Műveletek | Microsoft.Compute/restorePointCollections/read | Egy visszaállításipont-gyűjtemény tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/restorePointCollections/restorePoints/delete | A visszaállítási pont törlése |
> | Műveletek | Microsoft.Compute/restorePointCollections/restorePoints/read | Egy visszaállítási pont tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | Egy visszaállítási pont tulajdonságainak beolvasása a blob SAS URI azonosítóival |
> | Műveletek | Microsoft.Compute/restorePointCollections/restorePoints/write | Új visszaállítási pont létrehozása |
> | Műveletek | Microsoft.Compute/restorePointCollections/write | Egy új visszaállításipont-gyűjtemény létrehozása, vagy egy meglévő módosítása |
> | Műveletek | Microsoft.Compute/sharedVMImages/delete | A megosztott VM-lemezkép törlése |
> | Műveletek | Microsoft.Compute/sharedVMImages/read | Megosztott VM-lemezkép tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/sharedVMImages/versions/delete | A megosztott VM-lemezkép-verzió törlése |
> | Műveletek | Microsoft.Compute/sharedVMImages/versions/read | Megosztott VM-lemezkép-verzió tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/sharedVMImages/versions/replicate/action | Megosztott VM-lemezkép-verzió replikálása a célrégiókba |
> | Műveletek | Microsoft.Compute/sharedVMImages/versions/write | Hozzon létre egy új megosztott VM-lemezkép-verziót, vagy frissítsen egy meglévőt |
> | Műveletek | Microsoft.Compute/sharedVMImages/write | Új megosztott VM-lemezkép létrehozása vagy egy meglévő frissítése |
> | Műveletek | Microsoft.Compute/skus/read | Az előfizetéssel elérhető Microsoft Compute-termékváltozatok listájának beolvasása |
> | Műveletek | Microsoft.Compute/snapshots/beginGetAccess/action | A SAS URI-jának a pillanatkép beolvasása blobhozzáféréshez |
> | Műveletek | Microsoft.Compute/snapshots/delete | A pillanatkép törlése |
> | Műveletek | Microsoft.Compute/snapshots/endGetAccess/action | A pillanatkép SAS URI visszavonása |
> | Műveletek | Microsoft.Compute/snapshots/read | Pillanatkép tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/snapshots/write | Új pillanatkép létrehozása vagy meglévő pillanatkép frissítése |
> | Műveletek | Microsoft.Compute/virtualMachines/capture/action | Rögzíti a virtuális gépet úgy, hogy másolatot készít a virtuális merevlemezekről és létrehoz egy sablont, mellyel hasonló virtuális gépek hozhatók létre |
> | Műveletek | Microsoft.Compute/virtualMachines/convertToManagedDisks/action | A virtuális gép blobalapú lemezeinek konvertálása felügyelt lemezekké |
> | Műveletek | Microsoft.Compute/virtualMachines/deallocate/action | Kikapcsolja a virtuális gépet és felszabadítja a számítási erőforrásokat |
> | Műveletek | Microsoft.Compute/virtualMachines/delete | Törli a virtuális gépet |
> | Műveletek | Microsoft.Compute/virtualMachines/extensions/delete | Törli a virtuálisgép-bővítményt |
> | Műveletek | Microsoft.Compute/virtualMachines/extensions/read | Egy virtuálisgép-bővítmény tulajdonságait olvassa be |
> | Műveletek | Microsoft.Compute/virtualMachines/extensions/write | Létrehoz egy új vagy frissít egy meglévő virtuálisgép-bővítményt |
> | Műveletek | Microsoft.Compute/virtualMachines/generalize/action | Általánosítottra állítja a virtuális gép állapotát és előkészíti a virtuális gépet a rögzítéshez |
> | Műveletek | Microsoft.Compute/virtualMachines/instanceView/read | A virtuális gép és az erőforrásai részletes futási állapotát olvassa be |
> | DataAction | Microsoft.Compute/virtualMachines/login/action | Bejelentkezés egy virtuális gépre normál felhasználóként |
> | DataAction | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Bejelentkezés egy virtuális gépre a Windows rendszergazdai vagy a Linux gyökérszintű felhasználójának jogosultságaival |
> | Műveletek | Microsoft.Compute/virtualMachines/performMaintenance/action | Karbantartási művelet végrehajtása a virtuális gépen. |
> | Műveletek | Microsoft.Compute/virtualMachines/powerOff/action | Kikapcsolja a virtuális gép. Vegye figyelembe, hogy a virtuális gép továbbra is fizetnie kell ezért. |
> | Műveletek | Microsoft.Compute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Egy virtuális gép metrikadefinícióinak beolvasása |
> | Műveletek | Microsoft.Compute/virtualMachines/read | Egy virtuális gép tulajdonságait olvassa be |
> | Műveletek | Microsoft.Compute/virtualMachines/redeploy/action | A virtuális gép újbóli üzembe helyezése |
> | Műveletek | Microsoft.Compute/virtualMachines/reimage/action | Különbséglemezt használó virtuális gép rendszerképét állítja alaphelyzetbe. |
> | Műveletek | Microsoft.Compute/virtualMachines/restart/action | Újraindítja a virtuális gépet |
> | Műveletek | Microsoft.Compute/virtualMachines/runCommand/action | Egy előre definiált parancsfájl végrehajtása a virtuális gépen |
> | Műveletek | Microsoft.Compute/virtualMachines/start/action | Elindítja a virtuális gépet |
> | Műveletek | Microsoft.Compute/virtualMachines/vmSizes/read | Listázza az elérhető méreteket, melyekre a virtuális gép frissíthető |
> | Műveletek | Microsoft.Compute/virtualMachines/write | Létrehoz egy új virtuális gépet vagy frissít egy meglévő virtuális gépet |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/deallocate/action | A virtuálisgép-méretezési csoport példányaihoz hozzárendelt számítási erőforrások kikapcsolása és felszabadítása  |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/delete | A virtuálisgép-méretezési csoport törlése |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/delete/action | A virtuálisgép-méretezési csoport példányainak törlése |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/extensions/delete | A virtuálisgép-méretezési csoport bővítményének törlése |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/extensions/read | Egy virtuálisgép-méretezési csoport bővítménye tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/extensions/write | Egy virtuálisgép-méretezési csoport egy új bővítményének létrehozása, vagy egy meglévő frissítése |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action | Service Fabric-beli virtuálisgép-méretezési csoport platformfrissítési tartományainak manuális bejárása egy elakadt függőben lévő frissítés befejezéséhez |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/instanceView/read | A virtuálisgép-méretezési csoport példánynézetének beolvasása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/manualUpgrade/action | A példányok manuális frissítése a virtuálisgép-méretezési csoport legújabb modelljére |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | Egy virtuálisgép-méretezési csoport összes hálózati adaptere tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | Virtuálisgép-méretezési csoport gépein végrehajtott operációsrendszer-frissítések előzményeinek beolvasása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/performMaintenance/action | A virtuálisgép-méretezési csoport példányai tervezett karbantartásának végrehajtása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | A virtuálisgép-méretezési csoport példányainak kikapcsolása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/providers/Microsoft.Insights/metricDefinitions/read | Egy virtuálisgép-méretezési csoport metrikadefinícióinak beolvasása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | Egy virtuálisgép-méretezési csoport összes nyilvános IP-címe tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/read | Virtuálisgép-méretezési csoport tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | A virtuálisgép-méretezési csoport példányainak ismételt üzembe helyezése |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/reimage/action | Ezzel a művelettel alaphelyzetbe állíthatja a virtuálisgép-méretezési csoport példányainak rendszerképét |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/restart/action | Virtuálisgép-méretezési csoport példányainak újraindítása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/cancel/action | Ezzel a művelettel megszakítja a virtuálisgép-méretezési csoport működés közbeni frissítését |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | Virtuálisgép-méretezési csoport legutóbbi működés közbeni frissítési állapotának beolvasása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/scale/action | Annak ellenőrzése, hogy van-e olyan meglévő virtuálisgép-méretezési csoport, mely horizontálisan le- vagy felskálázható a megadott példányszámra |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/skus/read | Egy meglévő virtuálisgép-méretezési csoport érvényes termékváltozatainak listázása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/start/action | Virtuálisgép-méretezési csoport példányainak elindítása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/deallocate/action | Kikapcsol egy virtuális gépet egy virtuálisgép-méretezési csoportban, és felszabadítja az ahhoz tartozó számítási erőforrásokat. |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/delete | Töröl egy virtuális gépet egy virtuálisgép-méretezési csoportban. |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | Egy virtuálisgép-méretezési csoport egy virtuális gépének példányait tartalmazó nézetet adja meg. |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | Nyilvános IP-cím létre virtuálisgép-méretezési csoport tulajdonságainak beolvasása. Virtuálisgép-méretezési csoport hozhat létre, legfeljebb egy nyilvános IP-cím ipconfiguration (magánhálózati IP-címe) |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | Egy hálózati adapter virtuálisgép-méretezési készlet használatával létrehozott összes vagy egy IP-konfiguráció tulajdonságainak beolvasása. IP-konfigurációk meghatároznia magánhálózati IP-címek |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | Egy virtuálisgép-méretezési csoportban létrehozott virtuális gép egy vagy minden hálózati adaptere tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/action | Virtuálisgép-méretezési csoportban lévő virtuálisgép-példány tervezett karbantartásának végrehajtása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/powerOff/action | Kikapcsol egy virtuálisgép-példányt egy virtuálisgép-méretezési csoportban. |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Egy méretezési csoportbeli virtuális gép metrikadefinícióinak beolvasása |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | Egy virtuálisgép-méretezési csoport egy virtuális gépének tulajdonságait adja meg |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | Virtuálisgép-méretezési csoportban lévő virtuálisgép-példány ismételt üzembe helyezése |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | A művelet alaphelyzetbe állítja a virtuálisgép-méretezési csoportban lévő virtuálisgép-példány rendszerképét. |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/restart/action | Újraindít egy virtuálisgép-méretezési csoportban lévő virtuálisgép-példányt. |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/start/action | Elindít egy virtuálisgép-méretezési csoportban lévő virtuálisgép-példányt. |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write | Egy virtuálisgép-méretezési csoportban lévő virtuális gép tulajdonságainak frissítése |
> | Műveletek | Microsoft.Compute/virtualMachineScaleSets/write | Új virtuálisgép-méretezési csoport létrehozása vagy egy meglévő frissítése |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Consumption/balances/read | A számlázott időszak egy felügyeleti csoport összegzése kihasználtsága a listában. |
> | Műveletek | Microsoft.Consumption/budgets/read | Az előfizetés vagy a felügyeleti csoport költségvetések listában. |
> | Műveletek | Microsoft.Consumption/budgets/write | Hoz, frissítése és törlése a költségvetések előfizetés vagy a felügyeleti csoport. |
> | Műveletek | Microsoft.Consumption/marketplaces/read | Egy hatókör EA és WebDirect előfizetésekhez piactér erőforrás látogatottsági adatait listázza. |
> | Műveletek | Microsoft.Consumption/operations/read | Minden támogatott műveleteket Microsoft.Consumption erőforrás-szolgáltató a listában. |
> | Műveletek | Microsoft.Consumption/pricesheets/read | Az előfizetés vagy egy felügyeleti csoport Pricesheets adatainak listában. |
> | Műveletek | Microsoft.Consumption/reservationDetails/read | A szalagfelhasználási adatok foglalás sorrend, illetve a felügyeleti csoportok által fenntartott példányok felsorolása. A részletek adata nap szintenként példányonként. |
> | Műveletek | Microsoft.Consumption/reservationRecommendations/read | Lista egyetlen vagy megosztott javaslatok-előfizetéshez tartozó foglalt példányok. |
> | Műveletek | Microsoft.Consumption/reservationSummaries/read | Foglalási sorrend, illetve a felügyeleti csoportok által fenntartott példányok összefoglaló kihasználtsága a listában. Az összefoglaló adatokat vagy a napi vagy havi szinten. |
> | Műveletek | Microsoft.Consumption/reservationTransactions/read | A tranzakció előzmények felügyeleti csoportok által fenntartott példányok felsorolása. |
> | Műveletek | Microsoft.Consumption/terms/read | Listázza az előfizetés vagy a felügyeleti csoport feltételeit. |
> | Műveletek | Microsoft.Consumption/usageDetails/read | A használat részleteiről EA és WebDirect előfizetésekhez hatókör listában. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ContainerInstance/containerGroups/containers/logs/read | Egy adott tároló naplóinak beolvasása. |
> | Műveletek | Microsoft.ContainerInstance/containerGroups/delete | Egy adott tárolócsoport törlése. |
> | Műveletek | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read | A tárolócsoport diagnosztikai beállításának beolvasása. |
> | Műveletek | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write | A tárolócsoport diagnosztikai beállításának létrehozása vagy frissítése. |
> | Műveletek | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/metricDefinitions/read | A tárolócsoport elérhető metrikáinak beolvasása. |
> | Műveletek | Microsoft.ContainerInstance/containerGroups/read | Az összes tárolócsoport beolvasása. |
> | Műveletek | Microsoft.ContainerInstance/containerGroups/write | Egy adott tárolócsoport létrehozása vagy frissítése. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ContainerRegistry/checkNameAvailability/read | Ellenőrzi, hogy a tároló neve használható. |
> | Műveletek | Microsoft.ContainerRegistry/locations/operationResults/read | Lekérdezi egy aszinkron művelet eredménye |
> | Műveletek | Microsoft.ContainerRegistry/operations/read | Felsorolja az összes Azure tároló beállításjegyzék REST API műveletek |
> | Műveletek | Microsoft.ContainerRegistry/register/action | A tároló beállításjegyzék erőforrás-szolgáltató előfizetésének regisztrálása, és lehetővé teszi a tároló nyilvántartó létrehozását. |
> | Műveletek | Microsoft.ContainerRegistry/registries/delete | Egy tároló beállításjegyzék törli. |
> | Műveletek | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | Egy esemény rács szűrő töröl egy tároló beállításjegyzék. |
> | Műveletek | Microsoft.ContainerRegistry/registries/eventGridFilters/read | A megadott rács szűrő tulajdonságait olvassa be, vagy a megadott tároló beállításjegyzék összes esemény rács szűrők listája. |
> | Műveletek | Microsoft.ContainerRegistry/registries/eventGridFilters/write | Létrehozza vagy frissíti a tároló beállításjegyzék rács esemény szűrőjének a megadott paraméterekkel. |
> | Műveletek | Microsoft.ContainerRegistry/registries/importImage/action | A megadott paraméterekkel tároló beállításjegyzék-lemezkép importálása. |
> | Műveletek | Microsoft.ContainerRegistry/registries/listCredentials/action | Megjeleníti a megadott tároló beállításjegyzék bejelentkezési hitelesítő adataihoz. |
> | Műveletek | Microsoft.ContainerRegistry/registries/listUsages/read | Megjeleníti a megadott tároló beállításjegyzék a a kvóta módjait. |
> | Műveletek | Microsoft.ContainerRegistry/registries/operationStatuses/read | A beállításjegyzék aszinkron műveleti állapotának beolvasása |
> | Műveletek | Microsoft.ContainerRegistry/registries/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.ContainerRegistry/registries/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.ContainerRegistry/registries/providers/Microsoft.Insights/metricDefinitions/read | A Microsoft ContainerRegistry elérhető metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.ContainerRegistry/registries/read | A megadott tároló beállításjegyzék tulajdonságait olvassa be, vagy a megadott erőforráscsoport vagy előfizetés összes tároló nyilvántartó sorolja fel. |
> | Műveletek | Microsoft.ContainerRegistry/registries/regenerateCredential/action | A bejelentkezési hitelesítő adatait a megadott tároló beállításjegyzék egyik újragenerálása. |
> | Műveletek | Microsoft.ContainerRegistry/registries/replications/delete | Egy tároló beállításjegyzék törli a replikációt. |
> | Műveletek | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | A replikációs aszinkron műveleti állapotának beolvasása |
> | Műveletek | Microsoft.ContainerRegistry/registries/replications/read | A megadott replikációs tulajdonságait olvassa be, vagy a megadott tároló rendszerleíró összes replikációk listáját. |
> | Műveletek | Microsoft.ContainerRegistry/registries/replications/write | Létrehozza vagy frissíti a tároló beállításjegyzékbeli replikálása a megadott paraméterekkel. |
> | Műveletek | Microsoft.ContainerRegistry/registries/webhooks/delete | A webhook törlése egy tároló beállításjegyzékből. |
> | Műveletek | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | A konfigurációs szolgáltatás URI és az egyéni fejlécet lekérdezi a webhook. |
> | Műveletek | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | A megadott webhook megőrző legutóbbi események listája. |
> | Műveletek | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | A webhook aszinkron műveleti állapotának beolvasása |
> | Műveletek | Microsoft.ContainerRegistry/registries/webhooks/ping/action | A webhook küldendő egy ping esemény váltja ki. |
> | Műveletek | Microsoft.ContainerRegistry/registries/webhooks/read | A megadott webhook tulajdonságait olvassa be, vagy a megadott tároló rendszerleíró összes webhookok listáját. |
> | Műveletek | Microsoft.ContainerRegistry/registries/webhooks/write | Létrehozza vagy frissíti a webhook a tároló beállításjegyzékbeli a megadott paraméterekkel. |
> | Műveletek | Microsoft.ContainerRegistry/registries/write | Létrehozza vagy frissíti a tároló beállításjegyzékbeli a megadott paraméterekkel. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ContainerService/containerServices/delete | A tárolószolgáltatás törlése |
> | Műveletek | Microsoft.ContainerService/containerServices/read | A tárolószolgáltatás beolvasása |
> | Műveletek | Microsoft.ContainerService/containerServices/write | Létrehoz egy új tároló szolgáltatást, vagy frissít egy meglévő |
> | Műveletek | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Egy felügyelt fürt hozzáférési profil szolgáltatáshitelesítést szerepkörnév lista hitelesítő adat segítségével |
> | Műveletek | Microsoft.ContainerService/managedClusters/accessProfiles/read | Egy felügyelt fürt hozzáférési profil szolgáltatáshitelesítést szerepkörnév |
> | Műveletek | Microsoft.ContainerService/managedClusters/delete | Egy felügyelt fürt törlése |
> | Műveletek | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/diagnosticSettings/read | Egy felügyelt fürt erőforrás diagnosztikai beállításának beolvasása |
> | Műveletek | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti egy felügyelt fürt erőforrás diagnosztikai beállításának |
> | Műveletek | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/metricDefinitions/read | Felügyelt fürt elérhető metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.ContainerService/managedClusters/read | Egy felügyelt fürt beolvasása |
> | Műveletek | Microsoft.ContainerService/managedClusters/write | Egy új felügyelt fürtöt hoz létre vagy frissít egy meglévő |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ContentModerator/applications/delete | Törlési művelet |
> | Műveletek | Microsoft.ContentModerator/applications/listSecrets/action | Titkos kulcsok listázása |
> | Műveletek | Microsoft.ContentModerator/applications/listSingleSignOnToken/action | Olvassa el a jogkivonatok az egyszeri bejelentkezés |
> | Műveletek | Microsoft.ContentModerator/applications/read | Olvasási művelet |
> | Műveletek | Microsoft.ContentModerator/applications/write | Írási művelet |
> | Műveletek | Microsoft.ContentModerator/applications/write | Írási művelet |
> | Műveletek | Microsoft.ContentModerator/listCommunicationPreference/action | Lista kommunikációs beállításokat |
> | Műveletek | Microsoft.ContentModerator/operations/read | olvasási műveletek |
> | Műveletek | Microsoft.ContentModerator/updateCommunicationPreference/action | Update kommunikációs beállítás |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.CustomerInsights/hubs/adobemetadata/action | Létrehozása vagy frissítése bármely Azure felhasználói Insights Adobe metaadatok |
> | Műveletek | Microsoft.CustomerInsights/hubs/adobemetadata/read | Minden Azure felhasználói Insights Adobe metaadatot olvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/authorizationPolicies/delete | Bármely Azure felhasználói Insights megosztott hozzáférési aláírást házirend törlése |
> | Műveletek | Microsoft.CustomerInsights/hubs/authorizationPolicies/read | Azure felhasználói elemzéseket megosztott hozzáférési aláírást házirend olvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/authorizationPolicies/regeneratePrimaryKey/action | Azure felhasználói Insights megosztott hozzáférési aláírást házirend elsődleges kulcs újragenerálása |
> | Műveletek | Microsoft.CustomerInsights/hubs/authorizationPolicies/regenerateSecondaryKey/action | Azure felhasználói Insights megosztott hozzáférési aláírást házirend másodlagos kulcs újragenerálása |
> | Műveletek | Microsoft.CustomerInsights/hubs/authorizationPolicies/write | Hozzon létre vagy bármely Azure felhasználói Insights megosztott hozzáférési aláírást házirend frissítése |
> | Műveletek | Microsoft.CustomerInsights/hubs/connectors/activate/action | Bármely Azure felhasználói Insights-összekötő aktiválása |
> | Műveletek | Microsoft.CustomerInsights/hubs/connectors/activate/action | Bármely Azure felhasználói Insights-összekötő aktiválása |
> | Műveletek | Microsoft.CustomerInsights/hubs/connectors/delete | Bármely Azure felhasználói Insights-összekötő törlése |
> | Műveletek | Microsoft.CustomerInsights/hubs/connectors/getruntimestatus/action | Bármely Azure felhasználói Insights-összekötő futási állapotának beolvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/connectors/mappings/activate/action | Bármely Azure felhasználói Insights összekötő hozzárendelés aktiválásához |
> | Műveletek | Microsoft.CustomerInsights/hubs/connectors/mappings/delete | Bármely Azure felhasználói Insights összekötő leképezés törlése |
> | Műveletek | Microsoft.CustomerInsights/hubs/connectors/mappings/operations/read | Bármely Azure felhasználói Insights összekötő leképezési műveleti eredmény olvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/connectors/mappings/read | Olvassa el a bármely Azure felhasználói Insights összekötő leképezése |
> | Műveletek | Microsoft.CustomerInsights/hubs/connectors/mappings/write | Hozzon létre vagy frissítési bármely Azure felhasználói Insights összekötő leképezés |
> | Műveletek | Microsoft.CustomerInsights/hubs/connectors/operations/read | Bármely Azure felhasználói Insights-összekötő műveleti eredmény olvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/connectors/read | Olvassa el a bármely Azure felhasználói Insights-összekötő |
> | Műveletek | Microsoft.CustomerInsights/hubs/connectors/saveauthinfo/action | Létrehozása vagy frissítése bármely Azure felhasználói Insights összekötő hitelesítési átjárókapcsolat adatai |
> | Műveletek | Microsoft.CustomerInsights/hubs/connectors/update/action | Bármely Azure felhasználói Insights-összekötő frissítése |
> | Műveletek | Microsoft.CustomerInsights/hubs/connectors/write | Létrehozása vagy frissítése bármely Azure felhasználói Insights-összekötő |
> | Műveletek | Microsoft.CustomerInsights/hubs/crmmetadata/action | Létrehozni vagy frissíteni az Azure felhasználói Insights Crm-metaadatok |
> | Műveletek | Microsoft.CustomerInsights/hubs/crmmetadata/read | Olvassa el az Azure felhasználói Insights Crm-metaadatok |
> | Műveletek | Microsoft.CustomerInsights/hubs/delete | Bármely Azure felhasználói Insights Hub törlése |
> | Műveletek | Microsoft.CustomerInsights/hubs/gdpr/delete | Bármely Azure felhasználói Insights Gdpr törlése |
> | Műveletek | Microsoft.CustomerInsights/hubs/gdpr/read | Bármely Azure felhasználói Insights Gdpr olvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/gdpr/write | Hozzon létre vagy bármely Azure felhasználói Insights Gdpr frissítése |
> | Műveletek | Microsoft.CustomerInsights/hubs/getbillingcredits/read | Az Azure felhasználói Insights Hub számlázási jóváírásokat kap |
> | Műveletek | Microsoft.CustomerInsights/hubs/getbillinghistory/read | Az Azure felhasználói Insights Hub számlázási előzmények beolvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/images/delete | Bármely Azure felhasználói Insights lemezkép törlése |
> | Műveletek | Microsoft.CustomerInsights/hubs/images/read | Olvassa el az Azure felhasználói Insights képet |
> | Műveletek | Microsoft.CustomerInsights/hubs/images/write | Létrehozni vagy frissíteni az Azure felhasználói Insights képet |
> | Műveletek | Microsoft.CustomerInsights/hubs/interactions/delete | Bármely azure Insights ügyfélkapcsolati törlése |
> | Műveletek | Microsoft.CustomerInsights/hubs/interactions/operations/read | Bármely Azure Insights kapcsolat az ügyféllel műveleti eredmény olvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/interactions/read | Olvassa el az Azure felhasználói Insights beavatkozás |
> | Műveletek | Microsoft.CustomerInsights/hubs/interactions/suggestrelationshiplinks/action | Bármely Azure Insights ügyfélkapcsolati javasolni kapcsolat hivatkozások |
> | Műveletek | Microsoft.CustomerInsights/hubs/interactions/write | Létrehozása vagy frissítése Insights Azure felhasználói beavatkozás |
> | Műveletek | Microsoft.CustomerInsights/hubs/kpi/delete | Bármely Azure felhasználói Insights fő teljesítménymutató törlése |
> | Műveletek | Microsoft.CustomerInsights/hubs/kpi/operations/read | Bármely Azure felhasználói Insights a fő teljesítménymutatók műveleti eredmény olvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/kpi/read | Bármely Azure felhasználói Insights fő teljesítménymutató olvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/kpi/reprocess/action | Bármely Azure felhasználói Insights fő teljesítménymutatók újból feldolgozza |
> | Műveletek | Microsoft.CustomerInsights/hubs/kpi/write | Létrehozása vagy frissítése bármely Azure felhasználói Insights fő teljesítménymutató |
> | Műveletek | Microsoft.CustomerInsights/hubs/links/delete | Bármely Azure felhasználói Insights hivatkozások törlése |
> | Műveletek | Microsoft.CustomerInsights/hubs/links/operations/read | Bármely Azure felhasználói Insights hivatkozások műveleti eredmény olvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/links/read | Olvassa el az Azure felhasználói Insights hivatkozások |
> | Műveletek | Microsoft.CustomerInsights/hubs/links/write | Hozzon létre vagy a frissítés az Azure-ügyfél |
> | Műveletek | Microsoft.CustomerInsights/hubs/msemetadata/action | Létrehozása vagy frissítése bármely Azure felhasználói Insights Mse metaadatok |
> | Műveletek | Microsoft.CustomerInsights/hubs/msemetadata/read | Minden Azure felhasználói Insights Mse metaadatot olvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/operationresults/read | Az Azure felhasználói Insights Hub művelet eredmények |
> | Műveletek | Microsoft.CustomerInsights/hubs/predictions/delete | Bármely Azure felhasználói Insights előrejelzéseket törlése |
> | Műveletek | Microsoft.CustomerInsights/hubs/predictions/operations/read | Bármely Azure felhasználói Insights előrejelzéseket műveleti eredmény olvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/predictions/read | Bármely Azure felhasználói Insights előrejelzéseket olvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/predictions/write | Létrehozása vagy frissítése bármely Azure felhasználói előrejelzés |
> | Műveletek | Microsoft.CustomerInsights/hubs/predictivematchpolicies/delete | Bármely Azure felhasználói Insights prediktív egyezés házirendek törlése |
> | Műveletek | Microsoft.CustomerInsights/hubs/predictivematchpolicies/operations/read | Bármely Azure felhasználói Insights prediktív egyezés házirendek műveleti eredmény olvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/predictivematchpolicies/read | Minden Azure felhasználói Insights prediktív egyezés házirendet olvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/predictivematchpolicies/write | Bármely Azure felhasználói Insights prediktív egyezés irányelveinek létrehozása vagy frissítése |
> | Műveletek | Microsoft.CustomerInsights/hubs/profiles/delete | Bármely Azure felhasználói Insights profil törlése |
> | Műveletek | Microsoft.CustomerInsights/hubs/profiles/operations/read | Bármely Azure felhasználói Insights profil műveleti eredmény olvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/profiles/read | Bármely Azure felhasználói Insights profil olvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/profiles/write | Minden Azure felhasználói Insights profil írása |
> | Műveletek | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/logDefinitions/read | Naplók erőforrás beolvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/metricDefinitions/read | Az erőforrás elérhető metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/read | Bármely Azure felhasználói Insights Hub olvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/relationshiplinks/delete | Bármely Azure felhasználói Insights kapcsolat hivatkozások törlése |
> | Műveletek | Microsoft.CustomerInsights/hubs/relationshiplinks/operations/read | Bármely Azure felhasználói Insights kapcsolat hivatkozások műveleti eredmény olvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/relationshiplinks/read | Olvassa el az Azure felhasználói Insights kapcsolat hivatkozások |
> | Műveletek | Microsoft.CustomerInsights/hubs/relationshiplinks/write | Hozzon létre vagy a frissítés az Azure felhasználói Insights kapcsolat |
> | Műveletek | Microsoft.CustomerInsights/hubs/relationships/delete | Minden Azure felhasználói Insights kapcsolat törlése |
> | Műveletek | Microsoft.CustomerInsights/hubs/relationships/operations/read | Bármely Azure Insights ügyfélkapcsolatok műveleti eredmény olvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/relationships/read | Bármely Azure Insights ügyfélkapcsolatok olvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/relationships/write | Létrehozása vagy frissítése bármely Azure Insights ügyfélkapcsolatok |
> | Műveletek | Microsoft.CustomerInsights/hubs/roleAssignments/delete | Bármely Azure felhasználói Insights Rbac-hozzárendelés törlése |
> | Műveletek | Microsoft.CustomerInsights/hubs/roleAssignments/operations/read | Bármely Azure felhasználói Insights Rbac-hozzárendelés műveleti eredmény olvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/roleAssignments/read | Olvassa el a bármely Azure felhasználói Insights Rbac-hozzárendelés |
> | Műveletek | Microsoft.CustomerInsights/hubs/roleAssignments/write | Hozzon létre, vagy bármely Azure felhasználói Insights Rbac-hozzárendelés módosítása |
> | Műveletek | Microsoft.CustomerInsights/hubs/roles/read | Olvassa el a bármely Azure felhasználói Insights Rbac-szerepkörök |
> | Műveletek | Microsoft.CustomerInsights/hubs/salesforcemetadata/action | Létrehozása vagy frissítése bármely Azure felhasználói Insights SalesForce metaadatok |
> | Műveletek | Microsoft.CustomerInsights/hubs/salesforcemetadata/read | Minden Azure felhasználói Insights SalesForce metaadatot olvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/segments/delete | Bármely Azure Insights ügyfélszegmensek törlése |
> | Műveletek | Microsoft.CustomerInsights/hubs/segments/dynamic/action | Felügyeleti bármely Azure felhasználói Insight dinamikus szegmensek |
> | Műveletek | Microsoft.CustomerInsights/hubs/segments/read | Bármely Azure Insights ügyfélszegmensek olvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/segments/static/action | Felügyeleti bármely Azure felhasználói Insight statikus szegmensek |
> | Műveletek | Microsoft.CustomerInsights/hubs/segments/write | Bármely Azure felhasználói Insights szegmensek létrehozása és módosítása |
> | Műveletek | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/delete | Bármely Azure felhasználói Insights SqlConnectionStrings törlése |
> | Műveletek | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/read | Bármely Azure felhasználói Insights SqlConnectionStrings olvasása |
> | Műveletek | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/write | Hozzon létre vagy bármely Azure felhasználói Insights SqlConnectionStrings frissítése |
> | Műveletek | Microsoft.CustomerInsights/hubs/suggesttypeschema/action | Hozzon létre típus séma felajánlja a mintaadatokat |
> | Műveletek | Microsoft.CustomerInsights/hubs/tenantmanagement/read | Bármely Azure felhasználói Insights hub beállításainak kezelése |
> | Műveletek | Microsoft.CustomerInsights/hubs/views/delete | Bármely Azure felhasználói Insights alkalmazás nézet törlése |
> | Műveletek | Microsoft.CustomerInsights/hubs/views/read | Olvassa el a bármely Azure felhasználói Insights alkalmazás megtekintése |
> | Műveletek | Microsoft.CustomerInsights/hubs/views/write | Hozzon létre vagy bármely Azure felhasználói Insights alkalmazás nézet frissítése |
> | Műveletek | Microsoft.CustomerInsights/hubs/widgettypes/read | Olvassa el a bármely Azure felhasználói Insights App Widget típusa |
> | Műveletek | Microsoft.CustomerInsights/hubs/write | Hozzon létre vagy bármely Azure felhasználói Insights központ frissítése |
> | Műveletek | Microsoft.CustomerInsights/operations/read | Olvassa el az Azure felhasználói Insights Api Metadatas |
> | Műveletek | Microsoft.CustomerInsights/register/action | Az ügyfél Insights erőforrás-szolgáltató előfizetésének regisztrálása és létrehozását lehetővé tevő ügyfél Insights-erőforrások |
> | Műveletek | Microsoft.CustomerInsights/unregister/action | Az ügyfél Insights erőforrás-szolgáltató az előfizetés regisztrációjának törlése |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Databricks/workspaces/delete | Eltávolítja az munkaterülethez. |
> | Műveletek | Microsoft.Databricks/workspaces/read | A munkaterületek listájának beolvasása. |
> | Műveletek | Microsoft.Databricks/workspaces/write | Egy munkaterület létrehozása |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.DataCatalog/catalogs/delete | Törli a katalógusban. |
> | Műveletek | Microsoft.DataCatalog/catalogs/read | Tulajdonságok beolvasása katalógus vagy a katalógusban az előfizetéshez vagy erőforráscsoporthoz. |
> | Műveletek | Microsoft.DataCatalog/catalogs/write | Katalógus létrehozza vagy frissíti, a címkéket és a katalógus tulajdonságait. |
> | Műveletek | Microsoft.DataCatalog/checkNameAvailability/action | Bérlő katalógus neve rendelkezésre állását ellenőrzi. |
> | Műveletek | Microsoft.DataCatalog/operations/read | Microsoft.DataCatalog erőforrás-szolgáltató elérhető műveleteinek listázása. |
> | Műveletek | Microsoft.DataCatalog/register/action | Az előfizetés regisztrálása a Microsoft.DataCatalog erőforrás-szolgáltató. |
> | Műveletek | Microsoft.DataCatalog/unregister/action | Előfizetés Microsoft.DataCatalog erőforrás-szolgáltató regisztrációjának törlése. |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.DataFactory/datafactories/activitywindows/read | Olvassa be a tevékenység Windows adat-előállító megadott paraméterekkel. |
> | Műveletek | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | Tevékenység Windows beolvasása az adatcsatorna tevékenységhez megadott paraméterekkel. |
> | Műveletek | Microsoft.DataFactory/datafactories/datapipelines/activitywindows/read | Tevékenység Windows beolvassa a folyamat a megadott paraméterekkel. |
> | Műveletek | Microsoft.DataFactory/datafactories/datapipelines/delete | A feldolgozási sor törlése. |
> | Műveletek | Microsoft.DataFactory/datafactories/datapipelines/pause/action | Minden folyamatkezelési felfüggesztése. |
> | Műveletek | Microsoft.DataFactory/datafactories/datapipelines/read | Minden folyamatkezelési beolvasása. |
> | Műveletek | Microsoft.DataFactory/datafactories/datapipelines/resume/action | A folyamat folytatódik. |
> | Műveletek | Microsoft.DataFactory/datafactories/datapipelines/update/action | A folyamat frissíti. |
> | Műveletek | Microsoft.DataFactory/datafactories/datapipelines/write | Létrehozza vagy frissíti a folyamat. |
> | Műveletek | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | Tevékenység Windows beolvassa az adatkészlet megadott paraméterekkel. |
> | Műveletek | Microsoft.DataFactory/datafactories/datasets/delete | A Dataset törli. |
> | Műveletek | Microsoft.DataFactory/datafactories/datasets/read | A Dataset beolvasása. |
> | Műveletek | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | Beolvassa a szelet-futtassa a megadott kezdési időponttal adott adatkészlet. |
> | Műveletek | Microsoft.DataFactory/datafactories/datasets/slices/read | Lekérdezi a Adatszeletek az adott időszakban. |
> | Műveletek | Microsoft.DataFactory/datafactories/datasets/slices/write | Az Adatszelet állapotának frissítése. |
> | Műveletek | Microsoft.DataFactory/datafactories/datasets/write | Létrehozza vagy frissíti a Dataset. |
> | Műveletek | Microsoft.DataFactory/datafactories/delete | A Data Factory törlése. |
> | Műveletek | Microsoft.DataFactory/datafactories/gateways/connectioninfo/action | Minden átjáró olvassa be a kapcsolat adatai. |
> | Műveletek | Microsoft.DataFactory/datafactories/gateways/delete | Törli az összes átjáró. |
> | Műveletek | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | Minden átjáró jeleníti meg a hitelesítési kulcsokat. |
> | Műveletek | Microsoft.DataFactory/datafactories/gateways/read | Minden átjáró beolvasása. |
> | Műveletek | Microsoft.DataFactory/datafactories/gateways/regenerateauthkey/action | Minden átjáró hitelesítési kulcsainak újragenerálása. |
> | Műveletek | Microsoft.DataFactory/datafactories/gateways/write | Létrehozza vagy frissíti az összes átjáró. |
> | Műveletek | Microsoft.DataFactory/datafactories/linkedServices/delete | Törli a társított szolgáltatás. |
> | Műveletek | Microsoft.DataFactory/datafactories/linkedServices/read | Beolvassa a társított szolgáltatás. |
> | Műveletek | Microsoft.DataFactory/datafactories/linkedServices/write | Létrehozza vagy frissíti a társított szolgáltatás. |
> | Műveletek | Microsoft.DataFactory/datafactories/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.DataFactory/datafactories/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.DataFactory/datafactories/providers/Microsoft.Insights/metricDefinitions/read | A datafactories elérhető metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.DataFactory/datafactories/read | Beolvassa az adat-Előállítóban. |
> | Műveletek | Microsoft.DataFactory/datafactories/runs/loginfo/read | Beolvassa a naplókat tartalmazó blob tárolóra SAS URI. |
> | Műveletek | Microsoft.DataFactory/datafactories/tables/delete | A Dataset törli. |
> | Műveletek | Microsoft.DataFactory/datafactories/tables/read | A Dataset beolvasása. |
> | Műveletek | Microsoft.DataFactory/datafactories/tables/write | Létrehozza vagy frissíti a Dataset. |
> | Műveletek | Microsoft.DataFactory/datafactories/write | Létrehozza vagy frissíti az adat-Előállítóban. |
> | Műveletek | Microsoft.DataFactory/factories/cancelpipelinerun/action | Futtassa a futtatási azonosító által a folyamat megszakítása |
> | Műveletek | Microsoft.DataFactory/factories/datasets/delete | A Dataset törli. |
> | Műveletek | Microsoft.DataFactory/factories/datasets/read | A Dataset beolvasása. |
> | Műveletek | Microsoft.DataFactory/factories/datasets/write | Létrehozza vagy frissíti a Dataset. |
> | Műveletek | Microsoft.DataFactory/factories/delete | Törli az adat-Előállítóban. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/delete | Törli az összes integrációs futásidejű. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/read | Integrációs futásidejű Kapcsolatinformáció beolvasása. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | Integráció futási állapotának beolvasása. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | Minden integrációs futásidejű jeleníti meg a hitelesítési kulcsokat. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/monitoringdata/read | Minden integrációs futásidejű lekéri a figyelési adatokat. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | A csomópont törlése a megadott integrációs futásidejű. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | A megadott csomópont integrációs futásidejű az IP-címet adja vissza. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | Egy önálló üzemeltetett integrációs futásidejű csomópont frissíti. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/read | Minden integrációs futásidejű beolvasása. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | A megadott integrációs futási időben hitelesítési kulcsainak újragenerálása. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/start/action | Minden integrációs futásidejű kezdődik. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/stop/action | Minden integrációs futásidejű leáll. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | A hitelesítő adatok szinkronizálásának a megadott integrációs futási időben. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/upgrade/action | A megadott integrációs futásidejű frissíti. |
> | Műveletek | Microsoft.DataFactory/factories/integrationruntimes/write | Létrehozza vagy frissíti az összes integrációs futásidejű. |
> | Műveletek | Microsoft.DataFactory/factories/linkedServices/delete | Törli a társított szolgáltatás. |
> | Műveletek | Microsoft.DataFactory/factories/linkedServices/read | Olvasási társított szolgáltatás. |
> | Műveletek | Microsoft.DataFactory/factories/linkedServices/write | Létrehozni vagy frissíteni a társított szolgáltatás |
> | Műveletek | Microsoft.DataFactory/factories/pipelineruns/activityruns/read | Beolvassa a tevékenység futtatása a megadott feldolgozási folyamat futtatásához azonosítóját. |
> | Műveletek | Microsoft.DataFactory/factories/pipelineruns/read | A folyamat fut beolvasása. |
> | Műveletek | Microsoft.DataFactory/factories/pipelines/createrun/action | A tölcsér hoz létre a kísérletek. |
> | Műveletek | Microsoft.DataFactory/factories/pipelines/delete | Feldolgozási sor törlése. |
> | Műveletek | Microsoft.DataFactory/factories/pipelines/read | Olvassa be a folyamatot. |
> | Műveletek | Microsoft.DataFactory/factories/pipelines/write | Létrehozni vagy frissíteni a feldolgozási sor |
> | Műveletek | Microsoft.DataFactory/factories/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.DataFactory/factories/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.DataFactory/factories/providers/Microsoft.Insights/logDefinitions/read | Előállítók naplók beolvasása. |
> | Műveletek | Microsoft.DataFactory/factories/providers/Microsoft.Insights/metricDefinitions/read | Az előállítók elérhető metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.DataFactory/factories/read | Olvassa be az adat-Előállítóban. |
> | Műveletek | Microsoft.DataFactory/factories/triggers/delete | Törli az összes eseményindító. |
> | Műveletek | Microsoft.DataFactory/factories/triggers/read | Bármely eseményindító beolvasása. |
> | Műveletek | Microsoft.DataFactory/factories/triggers/start/action | Bármely eseményindító kezdődik. |
> | Műveletek | Microsoft.DataFactory/factories/triggers/stop/action | Leállítja az összes eseményindító. |
> | Műveletek | Microsoft.DataFactory/factories/triggers/triggerruns/read | Az eseményindító fut beolvasása. |
> | Műveletek | Microsoft.DataFactory/factories/triggers/write | Létrehozza vagy frissíti az összes eseményindító. |
> | Műveletek | Microsoft.DataFactory/factories/write | Hozzon létre vagy adat-előállító frissítése |
> | Műveletek | Microsoft.DataFactory/register/action | A Data Factory erőforrás-szolgáltató előfizetésének regisztrálása. |
> | Műveletek | Microsoft.DataFactory/unregister/action | A Data Factory erőforrás-szolgáltató az előfizetés regisztrációjának törlése. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | Számítás-házirend törlése. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | A számítási házirenddel kapcsolatos információk. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/computePolicies/write | Hozzon létre, vagy egy számítási házirend frissítése. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | Megszünteti egy DataLakeStore fiók DataLakeAnalytics-fiókból. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | A csatolt DataLakeStore fiók DataLakeAnalytics fiók adatainak beolvasása. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | Fiók létrehozása vagy frissítése egy csatolt DataLakeStore DataLakeAnalytics fiók. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/delete | DataLakeAnalytics fiók törlése. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | Tűzfalszabály törlése. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | Egy tűzfalszabály adatainak beolvasása. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | Hozzon létre, vagy egy tűzfalszabály módosítása. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/operationResults/read | Első DataLakeAnalytics fiók művelet eredménye. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/diagnosticSettings/read | A DataLakeAnalytics fiók diagnosztikai beállításainak beolvasása. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/diagnosticSettings/write | Hozzon létre, vagy frissítse a diagnosztikai beállításokat a DataLakeAnalytics fiók. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/logDefinitions/read | A naplók lekérése az DataLakeAnalytics fiók. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/metricDefinitions/read | Az elérhető mérőszámok lekérni a DataLakeAnalytics fiókhoz. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/read | Meglévő DataLakeAnalytics fiók adatainak beolvasása. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | A tároló DataLakeAnalytics fiók csatolt tárfiók SAS-tokenje listában. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | A csatolt tárfiók DataLakeAnalytics fiók tárolók beolvasása. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/storageAccounts/delete | A Storage-fiók egy DataLakeAnalytics fiókból választható. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | A csatolt tárfiók DataLakeAnalytics fiók adatainak beolvasása. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | Hozzon létre, vagy egy kapcsolódó tárfiók DataLakeAnalytics fiók módosítása. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Más felhasználók által benyújtott szakítsa engedélyeket. |
> | Műveletek | Microsoft.DataLakeAnalytics/accounts/write | Fiók létrehozása vagy frissítése egy DataLakeAnalytics. |
> | Műveletek | Microsoft.DataLakeAnalytics/locations/capability/read | Előfizetés funkció adatainak DataLakeAnalytics használatával kapcsolatban. |
> | Műveletek | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | Egy DataLakeAnalytics fióknevet elérhetőségének ellenőrzése. |
> | Műveletek | Microsoft.DataLakeAnalytics/locations/operationResults/read | Első DataLakeAnalytics fiók művelet eredménye. |
> | Műveletek | Microsoft.DataLakeAnalytics/operations/read | A DataLakeAnalytics elérhető műveleteinek beolvasása. |
> | Műveletek | Microsoft.DataLakeAnalytics/register/action | Előfizetés a DataLakeAnalytics regisztrálja. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.DataLakeStore/accounts/delete | DataLakeStore fiók törlése. |
> | Műveletek | Microsoft.DataLakeStore/accounts/enableKeyVault/action | Engedélyezze a KeyVault DataLakeStore fiók. |
> | Műveletek | Microsoft.DataLakeStore/accounts/firewallRules/delete | Tűzfalszabály törlése. |
> | Műveletek | Microsoft.DataLakeStore/accounts/firewallRules/read | Egy tűzfalszabály adatainak beolvasása. |
> | Műveletek | Microsoft.DataLakeStore/accounts/firewallRules/write | Hozzon létre, vagy egy tűzfalszabály módosítása. |
> | Műveletek | Microsoft.DataLakeStore/accounts/operationResults/read | Első DataLakeStore fiók művelet eredménye. |
> | Műveletek | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/diagnosticSettings/read | A DataLakeStore fiók diagnosztikai beállításainak beolvasása. |
> | Műveletek | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/diagnosticSettings/write | Hozzon létre, vagy frissítse a diagnosztikai beállításokat a DataLakeStore fiók. |
> | Műveletek | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/logDefinitions/read | A naplók lekérése az DataLakeStore fiók. |
> | Műveletek | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/metricDefinitions/read | Az elérhető mérőszámok lekérni a DataLakeStore fiókhoz. |
> | Műveletek | Microsoft.DataLakeStore/accounts/read | Meglévő DataLakeStore fiók adatainak beolvasása. |
> | Műveletek | Microsoft.DataLakeStore/accounts/Superuser/action | Adja meg a Data Lake Store Microsoft.Authorization/roleAssignments/write megadását felügyelő. |
> | Műveletek | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | Egy megbízható identitásszolgáltató törlése. |
> | Műveletek | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | Egy megbízható identitásszolgáltató adatainak beolvasása. |
> | Műveletek | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | Hozzon létre, vagy egy megbízható identitásszolgáltató frissítése. |
> | Műveletek | Microsoft.DataLakeStore/accounts/write | Fiók létrehozása vagy frissítése egy DataLakeStore. |
> | Műveletek | Microsoft.DataLakeStore/locations/capability/read | Előfizetés funkció adatainak DataLakeStore használatával kapcsolatban. |
> | Műveletek | Microsoft.DataLakeStore/locations/checkNameAvailability/action | Egy DataLakeStore fióknevet elérhetőségének ellenőrzése. |
> | Műveletek | Microsoft.DataLakeStore/locations/operationResults/read | Első DataLakeStore fiók művelet eredménye. |
> | Műveletek | Microsoft.DataLakeStore/operations/read | A DataLakeStore elérhető műveleteinek beolvasása. |
> | Műveletek | Microsoft.DataLakeStore/register/action | Előfizetés a DataLakeStore regisztrálja. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.DBforMySQL/locations/performanceTiers/read | A teljesítmény rétegek rendelkezésre álló listát ad vissza. |
> | Műveletek | Microsoft.DBforMySQL/performanceTiers/read | A teljesítmény rétegek rendelkezésre álló listát ad vissza. |
> | Műveletek | Microsoft.DBforMySQL/servers/delete | Egy meglévő kiszolgáló törlése. |
> | Műveletek | Microsoft.DBforMySQL/servers/firewallRules/delete | Meglévő tűzfalszabály törlése. |
> | Műveletek | Microsoft.DBforMySQL/servers/firewallRules/read | Tűzfal listájának kiszolgáló szabályok vagy a megadott tűzfalszabály tulajdonságainak beolvasása vissza. |
> | Műveletek | Microsoft.DBforMySQL/servers/firewallRules/write | A megadott paraméterek vagy a meglévő szabály módosítása egy olyan tűzfalszabályt hoz létre. |
> | Műveletek | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Lekérdezi az erőforráshoz tartozó disagnostic beállításai |
> | Műveletek | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Térjen vissza a típusú ítélt adatbázisokhoz |
> | Műveletek | Microsoft.DBforMySQL/servers/read | A kiszolgálók vagy lekérdezi a megadott kiszolgálóhoz tartozó tulajdonságok listáját adja vissza. |
> | Műveletek | Microsoft.DBforMySQL/servers/recoverableServers/read | A helyreállítható MySQL kiszolgáló adatait adja vissza |
> | Műveletek | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | Egy meglévő virtuális hálózat szabály törlése |
> | Műveletek | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | Térjen vissza a virtuális hálózat szabályok vagy a megadott virtuális hálózati szabály tulajdonságainak beolvasása. |
> | Műveletek | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | A virtuális hálózati szabályt hoz létre a megadott paraméterekkel, vagy a Tulajdonságok vagy a megadott virtuális hálózati szabály címkék frissítése. |
> | Műveletek | Microsoft.DBforMySQL/servers/write | Kiszolgáló létrehozása a megadott paraméterekkel, vagy frissítse a Tulajdonságok vagy a címkék a megadott kiszolgáló. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | A teljesítmény rétegek rendelkezésre álló listát ad vissza. |
> | Műveletek | Microsoft.DBforPostgreSQL/performanceTiers/read | A teljesítmény rétegek rendelkezésre álló listát ad vissza. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/delete | Egy meglévő kiszolgáló törlése. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | Meglévő tűzfalszabály törlése. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/firewallRules/read | Tűzfal listájának kiszolgáló szabályok vagy a megadott tűzfalszabály tulajdonságainak beolvasása vissza. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/firewallRules/write | A megadott paraméterek vagy a meglévő szabály módosítása egy olyan tűzfalszabályt hoz létre. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Lekérdezi az erőforráshoz tartozó disagnostic beállításai |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | Postgres kiszolgálók naplók beolvasása. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Térjen vissza a típusú ítélt adatbázisokhoz |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/read | A kiszolgálók vagy lekérdezi a megadott kiszolgálóhoz tartozó tulajdonságok listáját adja vissza. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | A helyreállítható PostgreSQL-kiszolgáló adatait adja vissza |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | A megadott kiszolgálón konfigurált kiszolgáló fenyegetés észlelési házirend részleteinek beolvasása |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | A kiszolgáló fenyegetés szabályzat egy adott kiszolgáló módosítása |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | Egy meglévő virtuális hálózat szabály törlése |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | Térjen vissza a virtuális hálózat szabályok vagy a megadott virtuális hálózati szabály tulajdonságainak beolvasása. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | A virtuális hálózati szabályt hoz létre a megadott paraméterekkel, vagy a Tulajdonságok vagy a megadott virtuális hálózati szabály címkék frissítése. |
> | Műveletek | Microsoft.DBforPostgreSQL/servers/write | Kiszolgáló létrehozása a megadott paraméterekkel, vagy frissítse a Tulajdonságok vagy a címkék a megadott kiszolgáló. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Devices/checkNameAvailability/Action | Ellenőrizze a rendelkezésre álló Ha IOT hubbal név |
> | Műveletek | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Ellenőrizze a rendelkezésre álló Ha IOT hubbal név |
> | Műveletek | Microsoft.Devices/ElasticPools/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Devices/ElasticPools/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/Delete | Az IOT hubbal bérlő erőforrás törlése |
> | Műveletek | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | Az EventHub felhasználói csoport törlése |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | Az EventHub fogyasztói csoportot beolvasása |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | Az EventHub felhasználói csoport létrehozása |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | Eszközök exportálása |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | Az IOT hubbal bérlői statisztikák erőforrás beolvasása |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | Eszközök importálása |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | Az IOT hubbal bérlői kulcs beolvasása |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/jobs/Read | Ezzel a feladat a megadott IOT hubbal beküldött adatokat |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | Lekérdezi a bérlőkulcsok IOT hubbal |
> | Műveletek | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | Lekérdezi az IOT hubbal szolgáltatáshoz elérhető naplófájl-definíciói |
> | Műveletek | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | Az IOT hubbal szolgáltatáshoz elérhető metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | Kvóta metrikák beolvasása |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/Read | Az IOT hubbal bérlő erőforrás beolvasása |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | A tesztüzenet szemben az összes meglévő útvonal |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | A tesztüzenet szemben a megadott útvonal tesztelése |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | Az IOT hubbal az összes útválasztási végpontok állapotának beolvasása |
> | Műveletek | Microsoft.Devices/elasticPools/iotHubTenants/Write | Létrehozni vagy frissíteni az IOT hubbal bérlő erőforrás |
> | Műveletek | Microsoft.Devices/ElasticPools/metricDefinitions/read | Az IOT hubbal szolgáltatáshoz elérhető metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.Devices/iotHubs/certificates/Delete | Tanúsítvány törlése |
> | Műveletek | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | Ellenőrző kód készítése |
> | Műveletek | Microsoft.Devices/iotHubs/certificates/Read | A tanúsítvány beolvasása |
> | Műveletek | Microsoft.Devices/iotHubs/certificates/verify/Action | Ellenőrizze az erőforrás-tanúsítvány |
> | Műveletek | Microsoft.Devices/iotHubs/certificates/Write | Létrehozni vagy frissíteni a tanúsítvány |
> | Műveletek | Microsoft.Devices/iotHubs/Delete | IOT hubbal erőforrás törlése |
> | Műveletek | Microsoft.Devices/IotHubs/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Devices/IotHubs/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.Devices/iotHubs/eventGridFilters/Delete | Az esemény rács szűrő törlése |
> | Műveletek | Microsoft.Devices/iotHubs/eventGridFilters/Read | Lekérdezi a esemény rács szűrő |
> | Műveletek | Microsoft.Devices/iotHubs/eventGridFilters/Write | Új vagy meglévő esemény rács szűrő frissítése |
> | Műveletek | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | Az EventHub felhasználói csoport törlése |
> | Műveletek | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | Az EventHub fogyasztói csoportot beolvasása |
> | Műveletek | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | Az EventHub felhasználói csoport létrehozása |
> | Műveletek | Microsoft.Devices/iotHubs/exportDevices/Action | Eszközök exportálása |
> | Műveletek | Microsoft.Devices/iotHubs/importDevices/Action | Eszközök importálása |
> | Műveletek | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | A megadott névvel az IOT hubbal kulcs beszerzése |
> | Műveletek | Microsoft.Devices/iotHubs/iotHubStats/Read | Megtekintheti a statisztikákat IOT hubbal |
> | Műveletek | Microsoft.Devices/iotHubs/jobs/Read | Ezzel a feladat a megadott IOT hubbal beküldött adatokat |
> | Műveletek | Microsoft.Devices/iotHubs/listkeys/Action | Minden IOT hubbal kulcs beszerzése |
> | Műveletek | Microsoft.Devices/IotHubs/logDefinitions/read | Lekérdezi az IOT hubbal szolgáltatáshoz elérhető naplófájl-definíciói |
> | Műveletek | Microsoft.Devices/IotHubs/metricDefinitions/read | Az IOT hubbal szolgáltatáshoz elérhető metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.Devices/iotHubs/quotaMetrics/Read | Kvóta metrikák beolvasása |
> | Műveletek | Microsoft.Devices/iotHubs/Read | Lekérdezi az IOT hubbal (oka) t |
> | Műveletek | Microsoft.Devices/iotHubs/routing/$testall/Action | A tesztüzenet szemben az összes meglévő útvonal |
> | Műveletek | Microsoft.Devices/iotHubs/routing/$testnew/Action | A tesztüzenet szemben a megadott útvonal tesztelése |
> | Műveletek | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | Az IOT hubbal az összes útválasztási végpontok állapotának beolvasása |
> | Műveletek | Microsoft.Devices/iotHubs/skus/Read | Érvényes IOT hubbal termékváltozatok beolvasása |
> | Műveletek | Microsoft.Devices/iotHubs/Write | Létrehozni vagy frissíteni az IOT hubbal erőforrás |
> | Műveletek | Microsoft.Devices/operations/Read | Minden ResourceProvider műveletek beolvasása |
> | Műveletek | Microsoft.Devices/provisioningServices/certificates/Delete | Tanúsítvány törlése |
> | Műveletek | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | Ellenőrző kód készítése |
> | Műveletek | Microsoft.Devices/provisioningServices/certificates/Read | A tanúsítvány beolvasása |
> | Műveletek | Microsoft.Devices/provisioningServices/certificates/verify/Action | Ellenőrizze az erőforrás-tanúsítvány |
> | Műveletek | Microsoft.Devices/provisioningServices/certificates/Write | Létrehozni vagy frissíteni a tanúsítvány |
> | Műveletek | Microsoft.Devices/provisioningServices/Delete | IotDps erőforrás törlése |
> | Műveletek | Microsoft.Devices/provisioningServices/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Devices/provisioningServices/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.Devices/provisioningServices/listkeys/Action | Minden IotDps kulcs beszerzése |
> | Műveletek | Microsoft.Devices/provisioningServices/logDefinitions/read | Lekérdezi a létesítési szolgáltatás elérhető naplófájl-definíciói |
> | Műveletek | Microsoft.Devices/provisioningServices/metricDefinitions/read | A létesítési szolgáltatás elérhető metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.Devices/provisioningServices/ProvisioningServiceKeys/listkeys/Action | Kulcsnév IotDps kulcsok beszerzése |
> | Műveletek | Microsoft.Devices/provisioningServices/Read | IotDps erőforráskészlet |
> | Műveletek | Microsoft.Devices/provisioningServices/skus/Read | Érvényes IotDps termékváltozatok beolvasása |
> | Műveletek | Microsoft.Devices/provisioningServices/Write | IotDps erőforrás létrehozása |
> | Műveletek | Microsoft.Devices/register/action | Az IOT hubbal erőforrás-szolgáltató előfizetésének regisztrálása és létrehozását lehetővé tevő erőforrások IOT hubbal |
> | Műveletek | Microsoft.Devices/register/action | Az IOT hubbal erőforrás-szolgáltató előfizetésének regisztrálása és létrehozását lehetővé tevő erőforrások IOT hubbal |
> | Műveletek | Microsoft.Devices/usages/Read | Ezzel előfizetés használati adatokat a szolgáltató. |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.DevTestLab/labCenters/delete | Labor erőforrások törlése. |
> | Műveletek | Microsoft.DevTestLab/labCenters/read | Olvassa el a labor adatközpontokban. |
> | Műveletek | Microsoft.DevTestLab/labCenters/write | Hozzáadása vagy módosítása a labor adatközpontokban. |
> | Műveletek | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | Olvassa el az azure resource manager-sablonok. |
> | Műveletek | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | Állít elő, az adott összetevő egy ARM-sablon, feltölti a szükséges fájlok egy tárfiókot, és érvényesíti a generált összetevő. |
> | Műveletek | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | Olvassa el az összetevők. |
> | Műveletek | Microsoft.DevTestLab/labs/artifactSources/delete | Összetevő források törlése. |
> | Műveletek | Microsoft.DevTestLab/labs/artifactSources/read | Olvassa el a összetevő források. |
> | Műveletek | Microsoft.DevTestLab/labs/artifactSources/write | Hozzáadása vagy módosítása összetevő források. |
> | Műveletek | Microsoft.DevTestLab/labs/ClaimAnyVm/action | Jogcím egy véletlenszerű claimable virtuális gép a tesztkörnyezetben. |
> | Műveletek | Microsoft.DevTestLab/labs/costs/read | Olvassa el a költségeket. |
> | Műveletek | Microsoft.DevTestLab/labs/costs/write | Adja hozzá, és módosíthatja az. |
> | Műveletek | Microsoft.DevTestLab/labs/CreateEnvironment/action | Virtuális gépek létrehozása egy tesztkörnyezetben. |
> | Műveletek | Microsoft.DevTestLab/labs/customImages/delete | Törölje az egyéni lemezképet. |
> | Műveletek | Microsoft.DevTestLab/labs/customImages/read | Olvassa el az egyéni lemezképek. |
> | Műveletek | Microsoft.DevTestLab/labs/customImages/write | Hozzáadása vagy módosítása az egyéni lemezképek. |
> | Műveletek | Microsoft.DevTestLab/labs/delete | Labs törlése. |
> | Műveletek | Microsoft.DevTestLab/labs/ExportResourceUsage/action | A tesztlabor-erőforrások használata exportálja a storage-fiók |
> | Műveletek | Microsoft.DevTestLab/labs/formulas/delete | Képletek törlése. |
> | Műveletek | Microsoft.DevTestLab/labs/formulas/read | Olvassa el a formulákat. |
> | Műveletek | Microsoft.DevTestLab/labs/formulas/write | Hozzáadása vagy módosítása formulákat. |
> | Műveletek | Microsoft.DevTestLab/labs/galleryImages/read | Olvassa el a gyűjtemény lemezképei. |
> | Műveletek | Microsoft.DevTestLab/labs/GenerateUploadUri/action | URI generálása az egyéni lemezképek feltöltése a labor. |
> | Műveletek | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | A virtuális gép egy másik, amikor importálja. |
> | Műveletek | Microsoft.DevTestLab/labs/ListVhds/action | Listázza az elérhető egyéni lemezkép létrehozásához szükséges lemezképeket. |
> | Műveletek | Microsoft.DevTestLab/labs/notificationChannels/delete | Notificationchannels törlése. |
> | Műveletek | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | Értesítést küld a megadott csatornán. |
> | Műveletek | Microsoft.DevTestLab/labs/notificationChannels/read | Olvassa el a notificationchannels. |
> | Műveletek | Microsoft.DevTestLab/labs/notificationChannels/write | Hozzáadása vagy módosítása notificationchannels. |
> | Műveletek | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | Labor házirend kiértékelése. |
> | Műveletek | Microsoft.DevTestLab/labs/policySets/policies/delete | Törölje a házirendeket. |
> | Műveletek | Microsoft.DevTestLab/labs/policySets/policies/read | Olvassa el a házirendeket. |
> | Műveletek | Microsoft.DevTestLab/labs/policySets/policies/write | Adja hozzá vagy módosíthat házirendeket. |
> | Műveletek | Microsoft.DevTestLab/labs/read | Olvassa el a labs. |
> | Műveletek | Microsoft.DevTestLab/labs/schedules/delete | Törölni az ütemezéseket. |
> | Műveletek | Microsoft.DevTestLab/labs/schedules/Execute/action | Ütemezés hajtható végre. |
> | Műveletek | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | Felsorolja az összes megfelelő ütemezést |
> | Műveletek | Microsoft.DevTestLab/labs/schedules/read | Olvassa el az ütemezések. |
> | Műveletek | Microsoft.DevTestLab/labs/schedules/write | Hozzáadása vagy módosítása ütemezések. |
> | Műveletek | Microsoft.DevTestLab/labs/serviceRunners/delete | Szolgáltatás indák törlése. |
> | Műveletek | Microsoft.DevTestLab/labs/serviceRunners/read | Olvasási szolgáltatás indák. |
> | Műveletek | Microsoft.DevTestLab/labs/serviceRunners/write | Adja hozzá, vagy módosítsa a szolgáltatás indák. |
> | Műveletek | Microsoft.DevTestLab/labs/users/delete | Törölje a felhasználói profilokat. |
> | Műveletek | Microsoft.DevTestLab/labs/users/disks/Attach/action | Csatolja, és a címbérlet, a lemez a virtuális gép létrehozása. |
> | Műveletek | Microsoft.DevTestLab/labs/users/disks/delete | Törölje a lemezt. |
> | Műveletek | Microsoft.DevTestLab/labs/users/disks/Detach/action | Válassza le, és a címbérlet, a lemez a virtuális géphez csatlakoztatott break. |
> | Műveletek | Microsoft.DevTestLab/labs/users/disks/read | Lemezek olvasása. |
> | Műveletek | Microsoft.DevTestLab/labs/users/disks/write | Hozzáadása vagy módosítása a lemezeket. |
> | Műveletek | Microsoft.DevTestLab/labs/users/environments/delete | Törölje a környezetben. |
> | Műveletek | Microsoft.DevTestLab/labs/users/environments/read | Olvassa el a környezetben. |
> | Műveletek | Microsoft.DevTestLab/labs/users/environments/write | Hozzáadása vagy módosítása a környezetben. |
> | Műveletek | Microsoft.DevTestLab/labs/users/read | Olvassa a felhasználói profilokat. |
> | Műveletek | Microsoft.DevTestLab/labs/users/secrets/delete | Törölje a titkos kulcsok. |
> | Műveletek | Microsoft.DevTestLab/labs/users/secrets/read | Olvassa el a titkos kulcsok. |
> | Műveletek | Microsoft.DevTestLab/labs/users/secrets/write | Hozzáadása vagy módosítása a titkos kulcsok. |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | Törölje a szolgáltatás hálóhoz. |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | Felsorolja az összes megfelelő ütemezést |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/read | Olvasási szolgáltatás hálóhoz. |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | Törölni az ütemezéseket. |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | Ütemezés hajtható végre. |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | Olvassa el az ütemezések. |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | Hozzáadása vagy módosítása ütemezések. |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | Indítsa el a service fabric. |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | Állítsa le a service fabric |
> | Műveletek | Microsoft.DevTestLab/labs/users/serviceFabrics/write | Adja hozzá, vagy módosítsa a szolgáltatás hálóhoz. |
> | Műveletek | Microsoft.DevTestLab/labs/users/write | Hozzáadása vagy módosítása a felhasználói profilok. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | Új vagy meglévő adatlemezt csatolni a virtuális géphez. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | Az összetevők vonatkozik a virtuális gép. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | Saját tulajdonba vétele meglévő virtuális gépből |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/delete | Törölje a virtuális gépeket. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | Válassza le a megadott lemezt a virtuális gép. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | Felsorolja az összes megfelelő ütemezést |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/read | Olvassa el a virtuális gépek. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | A virtuális gép újbóli üzembe helyezése |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | Átméretezni a virtuális gépet. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | A virtuális gép újraindításához. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | Törölni az ütemezéseket. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | Ütemezés hajtható végre. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | Olvassa el az ütemezések. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | Hozzáadása vagy módosítása ütemezések. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/Start/action | Indítsa el a virtuális gépet. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | A virtuális gép leállítása |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | Saját kezűleg adatlemezek virtuális gép tulajdonjogának átruházása |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | Egy meglévő virtuális gép tulajdonjogának kiadás |
> | Műveletek | Microsoft.DevTestLab/labs/virtualMachines/write | Virtuális gépek hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualNetworks/delete | Törölje a virtuális hálózatok. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualNetworks/read | Olvassa el a virtuális hálózatok. |
> | Műveletek | Microsoft.DevTestLab/labs/virtualNetworks/write | Adja hozzá, vagy módosítsa a virtuális hálózatok. |
> | Műveletek | Microsoft.DevTestLab/labs/vmPools/delete | Virtuális gép készleteket. |
> | Műveletek | Microsoft.DevTestLab/labs/vmPools/read | Olvassa el a virtuális gép készletek. |
> | Műveletek | Microsoft.DevTestLab/labs/vmPools/write | Adja hozzá, vagy módosítsa a virtuális gép készletek. |
> | Műveletek | Microsoft.DevTestLab/labs/write | Hozzáadása vagy módosítása labs. |
> | Műveletek | Microsoft.DevTestLab/locations/operations/read | Az olvasási műveletek. |
> | Műveletek | Microsoft.DevTestLab/register/action | Az előfizetés regisztrálása |
> | Műveletek | Microsoft.DevTestLab/schedules/delete | Törölni az ütemezéseket. |
> | Műveletek | Microsoft.DevTestLab/schedules/Execute/action | Ütemezés hajtható végre. |
> | Műveletek | Microsoft.DevTestLab/schedules/read | Olvassa el az ütemezések. |
> | Műveletek | Microsoft.DevTestLab/schedules/Retarget/action | Frissíti a ütemezés célerőforrás azonosítója. |
> | Műveletek | Microsoft.DevTestLab/schedules/write | Hozzáadása vagy módosítása ütemezések. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.DocumentDB/databaseAccountNames/read | A név foglaltságának ellenőrzése. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | Módosítsa az erőforráscsoport egy olyan adatbázis-fiók |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | A gyűjtemény metrikai meghatározásainak beolvasása. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | A gyűjtemény metrikák beolvasása. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | Olvassa el az adatbázis fiók partíció szintű metrikáit |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | Olvassa el az adatbázis fiók partíció szintű metrikák |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | Olvassa el az adatbázis fiók partíciók egy gyűjteményen belül |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | Olvassa el az adatbázis-fiók partíció szintű módjait |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | Beolvassa a gyűjtemény is érvényesek. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | Az adatbázis metrikai meghatározásainak beolvasása |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | Az adatbázis metrikák beolvasása. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | Olvassa be az adatbázis is érvényesek. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/delete | Az adatbázis-fiók törlése. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/failoverPriorityChange/action | Az adatbázisfiók régiók feladatátvételi prioritás módosítása. Ez használatos kézi feladatátvételi művelet végrehajtásához |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | A kapcsolati karakterláncok beolvasása az adatbázisfiók |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/listKeys/action | Egy adatbázis-fiók listázása |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | Beolvassa az adatbázist fiók metrikák definíciókat. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/metrics/read | Az adatbázis-fiók metrikák beolvasása. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/offlineRegion/action | Kapcsolat nélküli adatbázis fiók egy régiót.  |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/onlineRegion/action | Online adatbázis fiók egy régiót. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/operationResults/read | Olvassa el az aszinkron művelet állapotát |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | Olvassa el a késés metrikák |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/percentile/read | Olvassa el a replikációs késések fordulnak elő a százalékos érték |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | Késés olvasni a megadott forrás- és célparaméterei régió |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | Késés olvasni a célcsoportok régió |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/logDefinitions/read | A rendelkezésre álló napló catageries az adatbázis-fiók beolvasása |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/metricDefinitions/read | Az adatbázis fiók elérhető metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/read | Az adatbázisfiók beolvasása. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Olvassa be az adatbázis csak olvasható kulcsait. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | Olvassa be az adatbázis csak olvasható kulcsait. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/regenerateKey/action | Az adatbázisfiók kulcsokkal elforgatása |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/metrics/read | A regionális gyűjtemény metrikák beolvasása. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | Olvassa el a regionális adatbázis fiók partíció szintű metrikáit |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/metrics/read | Olvassa el a regionális adatbázis fiók partíció szintű metrikák |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/read | Olvassa el a regionális adatbázis fiók partíciók egy gyűjteményen belül |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/region/metrics/read | A régió és az adatbázis fiók metrikák beolvasása. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/usages/read | Beolvassa az adatbázist fiók módjait. |
> | Műveletek | Microsoft.DocumentDB/databaseAccounts/write | Egy adatbázis-fiók frissítéséhez. |
> | Műveletek | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | Microsoft.DocumentDB értesíti, hogy VirtualNetwork vagy az alhálózat törlése folyamatban van |
> | Műveletek | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | Olvassa el a deleteVirtualNetworkOrSubnets állapotának aszinkron művelet |
> | Műveletek | Microsoft.DocumentDB/operationResults/read | Olvassa el az aszinkron művelet állapotát |
> | Műveletek | Microsoft.DocumentDB/operations/read | A Microsoft documentdb elérhető olvasási műveletek  |
> | Műveletek | Microsoft.DocumentDB/register/action |  Az előfizetés a Microsoft a DocumentDB erőforrás-szolgáltató regisztrálása |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.DomainRegistration/checkDomainAvailability/Action | Ellenőrizze, hogy a tartomány megvásárolható |
> | Műveletek | Microsoft.DomainRegistration/domains/Delete | Törölje a meglévő tartományban. |
> | Műveletek | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Delete | Törölje a tulajdonjog azonosítója |
> | Műveletek | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Lista tulajdonjoga azonosítói |
> | Műveletek | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Tulajdonosi azonosító lekérése |
> | Műveletek | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Write | Létrehozása vagy frissítése azonosítója |
> | Műveletek | Microsoft.DomainRegistration/domains/operationresults/Read | A tartomány művelet |
> | Műveletek | Microsoft.DomainRegistration/domains/Read | A tartományok listájának lekérdezése |
> | Műveletek | Microsoft.DomainRegistration/domains/Read | Get domain |
> | Műveletek | Microsoft.DomainRegistration/domains/renew/Action | Újítsa meg egy meglévő tartományhoz. |
> | Műveletek | Microsoft.DomainRegistration/domains/Write | Új tartomány hozzáadása vagy egy meglévő frissítése |
> | Műveletek | Microsoft.DomainRegistration/generateSsoRequest/Action | Létre a tartomány vezérlőközpont bejelentkezni. |
> | Műveletek | Microsoft.DomainRegistration/listDomainRecommendations/Action | A lista tartomány meg ajánlásainkat kulcsszavak beolvasása |
> | Műveletek | Microsoft.DomainRegistration/operations/Read | Minden műveleteket az app service tartományregisztrációs felsorolása |
> | Műveletek | Microsoft.DomainRegistration/register/action | Az előfizetés a Microsoft Domains erőforrás-szolgáltató regisztrálása |
> | Műveletek | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | A szerződés művelet |
> | Műveletek | Microsoft.DomainRegistration/topLevelDomains/Read | A toplevel tartományok beolvasása |
> | Műveletek | Microsoft.DomainRegistration/topLevelDomains/Read | A toplevel lekérése |
> | Műveletek | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | Ellenőrizze a tartomány beszerzési objektum lefolytatása nélkül |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.DynamicsLcs/lcsprojects/clouddeployments/read | Megjeleníti a Microsoft Dynamics AX 2012 R3 értékelési központi telepítések a felhasználóhoz tartozó Microsoft Dynamics életciklus szolgáltatások projektben |
> | Műveletek | Microsoft.DynamicsLcs/lcsprojects/clouddeployments/write | A felhasználóhoz tartozó Microsoft Dynamics életciklus szolgáltatások projektben létrehozása a Microsoft Dynamics AX 2012 R3 értékelési telepítést. Központi telepítések is kezelhető az Azure felügyeleti portálon |
> | Műveletek | Microsoft.DynamicsLcs/lcsprojects/connectors/read | Olvassa el a Microsoft Dynamics életciklus szolgáltatások projekthez tartozó összekötők |
> | Műveletek | Microsoft.DynamicsLcs/lcsprojects/connectors/write | Létrehozása és frissítése a Microsoft Dynamics életciklus szolgáltatások projekthez tartozó összekötők |
> | Műveletek | Microsoft.DynamicsLcs/lcsprojects/delete | A felhasználóhoz tartozó Microsoft Dynamics életciklus-Services-projektek törlése |
> | Műveletek | Microsoft.DynamicsLcs/lcsprojects/read | A felhasználóhoz tartozó Microsoft Dynamics életciklus szolgáltatások projektek megjelenítéséhez |
> | Műveletek | Microsoft.DynamicsLcs/lcsprojects/write | Hozzon létre, és a felhasználóhoz tartozó Microsoft Dynamics életciklus szolgáltatások projektek frissítése. Csak a nevet és leírást tulajdonságait is lehet frissíteni. Az egyes előfizetésekhez és helyekhez társított a projekt létrehozása után nem frissíthető |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.EventGrid/eventSubscriptions/delete | Egy eventSubscription törlése |
> | Műveletek | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | Az esemény-előfizetés teljes URL-cím lekérése |
> | Műveletek | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read | Az esemény előfizetések diagnosztikai beállításának beolvasása. |
> | Műveletek | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti a diagnosztikai esemény-előfizetések beállítása |
> | Műveletek | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read | A eventSubscriptions elérhető metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.EventGrid/eventSubscriptions/read | Olvassa el a eventSubscription |
> | Műveletek | Microsoft.EventGrid/eventSubscriptions/write | Hozható létre vagy frissíthető egy eventSubscription |
> | Műveletek | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | Témakörök diagnosztikai beállításának beolvasása. |
> | Műveletek | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti a diagnosztikai beállításának kapcsolatos témakörök |
> | Műveletek | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | Elérhető metrikai meghatározások beolvasása kapcsolatos témakörök |
> | Műveletek | Microsoft.EventGrid/register/action | Regisztrálja a eventSubscription a EventGrid erőforrás-szolgáltató, és lehetővé teszi, hogy a rács esemény előfizetések létrehozása. |
> | Műveletek | Microsoft.EventGrid/topics/delete | Egy témakör törlése |
> | Műveletek | Microsoft.EventGrid/topics/listKeys/action | A témakör a listában kulcsok |
> | Műveletek | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/read | Témakörök diagnosztikai beállításának beolvasása. |
> | Műveletek | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti a diagnosztikai beállításának kapcsolatos témakörök |
> | Műveletek | Microsoft.EventGrid/topics/providers/Microsoft.Insights/metricDefinitions/read | Elérhető metrikai meghatározások beolvasása kapcsolatos témakörök |
> | Műveletek | Microsoft.EventGrid/topics/read | Olvassa el a témakör |
> | Műveletek | Microsoft.EventGrid/topics/regenerateKey/action | A témakör kulcs újragenerálása |
> | Műveletek | Microsoft.EventGrid/topics/write | Létrehozni vagy frissíteni a témakör |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.EventHub/checkNameAvailability/action | A névtér adott előfizetésben való elérhetőségének ellenőrzése. |
> | Műveletek | Microsoft.EventHub/checkNamespaceAvailability/action | A névtér adott előfizetésben való elérhetőségének ellenőrzése. Ez az API elavult. használja helyette a CheckNameAvailabiltiy. |
> | Műveletek | Microsoft.EventHub/clusters/providers/Microsoft.Insights/metricDefinitions/read | Fürt metrikáinak erőforrás leírása listájának beolvasása |
> | Műveletek | Microsoft.EventHub/clusters/read | A fürterőforrás leírásának beolvasása |
> | Műveletek | Microsoft.EventHub/clusters/write | A fürterőforrás leírásának beolvasása |
> | Műveletek | Microsoft.EventHub/namespaces/authorizationRules/action | Frissítések Namespace engedélyezési szabályt. Ez az API depricated. A Namespace engedélyezési szabály sorkészletének frissítéséhez használja a PUT hívás... Ez a művelet nem támogatott az API verzió 2017-04-01. |
> | Műveletek | Microsoft.EventHub/namespaces/authorizationRules/delete | Namespace engedélyezési szabály törlése. Az alapértelmezett Namespace engedélyezési szabály nem törölhető.  |
> | Műveletek | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | A névtérhez tartozó kapcsolati karakterlánc beolvasása |
> | Műveletek | Microsoft.EventHub/namespaces/authorizationRules/read | A névtér-engedélyezési szabályok leírásai listájának beolvasása. |
> | Műveletek | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása |
> | Műveletek | Microsoft.EventHub/namespaces/authorizationRules/write | A Namespace szintű engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és másodlagos kulcsok lehet frissíteni. |
> | Műveletek | Microsoft.EventHub/namespaces/Delete | Egy névtérerőforrás törlése |
> | Műveletek | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | A vészhelyreállítás elsődleges névterére vonatkozó engedélyezési szabályok kulcsainak beolvasása |
> | Műveletek | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | Vészhelyreállítás elsődleges névterére vonatkozó engedélyezési szabályok beolvasása |
> | Műveletek | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | A vészhelyreállítás letiltása és a módosítások elsődleges névtérből másodlagos névtérbe történő replikálásának leállítása. |
> | Műveletek | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | A névtéralias adott előfizetésben való elérhetőségének ellenőrzése. |
> | Műveletek | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | A névtér társított vész-helyreállítási konfiguráció törlése. Ezt a műveletet csak az elsődleges névtér keresztül kell meghívni. |
> | Műveletek | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | GEO DR feladatátvételének meghívása és a névtéralias beállítása úgy, hogy a másodlagos névtérre mutasson. |
> | Műveletek | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | A névtérhez hozzárendelt vész-helyreállítási konfiguráció beolvasása. |
> | Műveletek | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | A névtérhez hozzárendelt vész-helyreállítási konfiguráció létrehozása vagy frissítése. |
> | Műveletek | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/action | Az EventHub frissítési művelete. Ez a művelet nem támogatott az API verzió 2017-04-01. Az engedélyezési szabályok. Egy PUT hívás segítségével engedélyezési szabály frissítése. |
> | Műveletek | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/delete | A művelet törli az EventHub-engedélyezési szabályok |
> | Műveletek | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/listkeys/action | Az EventHub kapcsolati karakterláncot az beszerzése |
> | Műveletek | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/read |  Az EventHub-az engedélyezési szabályok listájának lekérdezése |
> | Műveletek | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/action | Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása |
> | Műveletek | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/write | Az EventHub-engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Az engedélyezési szabályok hozzáférési jogok lehet frissíteni. |
> | Műveletek | Microsoft.EventHub/namespaces/eventHubs/consumergroups/Delete | A művelet ConsumerGroup erőforrás törlése |
> | Műveletek | Microsoft.EventHub/namespaces/eventHubs/consumergroups/read | Listájának ConsumerGroup erőforrás leírása |
> | Műveletek | Microsoft.EventHub/namespaces/eventHubs/consumergroups/write | Hozzon létre vagy frissítés ConsumerGroup tulajdonságai. |
> | Műveletek | Microsoft.EventHub/namespaces/eventhubs/Delete | A művelet az EventHub erőforrás törlése |
> | Műveletek | Microsoft.EventHub/namespaces/eventhubs/read | Az EventHub erőforrás leírások listáját |
> | Műveletek | Microsoft.EventHub/namespaces/eventhubs/write | Hozzon létre vagy frissítés EventHub tulajdonságai. |
> | Műveletek | Microsoft.EventHub/namespaces/messagingPlan/read | Lekérdezi az névtér az üzenetküldési megtervezése.<br>Ez az API elavult.<br>A MessagingPlan erőforrás keresztül elérhetővé tett tulajdonságok (szülő) Namespace erőforrás későbbi API-verziók kerülnek...<br>Ez a művelet nem támogatott az API verzió 2017-04-01. |
> | Műveletek | Microsoft.EventHub/namespaces/messagingPlan/write | Frissíti az üzenetkezelési tervezése névtér.<br>Ez az API elavult.<br>A MessagingPlan erőforrás keresztül elérhetővé tett tulajdonságok (szülő) Namespace erőforrás későbbi API-verziók kerülnek...<br>Ez a művelet nem támogatott az API verzió 2017-04-01. |
> | Műveletek | Microsoft.EventHub/namespaces/operationresults/read | Névtérművelet állapotának beolvasása |
> | Műveletek | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Namespace diagnosztikai beállítások erőforrás leírása listájának beolvasása |
> | Műveletek | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Namespace diagnosztikai beállítások erőforrás leírása listájának beolvasása |
> | Műveletek | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/logDefinitions/read | Listájának Namespace naplók erőforrás leírása |
> | Műveletek | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Namespace metrikák erőforrás leírása listájának beolvasása |
> | Műveletek | Microsoft.EventHub/namespaces/read | A névtérerőforrások leírásai listájának beolvasása |
> | Műveletek | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | ACS-névtér eltávolítása |
> | Műveletek | Microsoft.EventHub/namespaces/write | Namespace erőforrás létrehozása és frissítése a tulajdonságait. Címkék és a kapacitás, a Namespace nem frissíthető tulajdonságait. |
> | Műveletek | Microsoft.EventHub/operations/read | Műveletek beolvasása |
> | Műveletek | Microsoft.EventHub/register/action | Az EventHub erőforrás-szolgáltatóhoz tartozó előfizetés regisztrálása és az EventHub-erőforrások létrehozásának engedélyezése |
> | Műveletek | Microsoft.EventHub/sku/read | Termékváltozat erőforrás leírása listájának beolvasása |
> | Műveletek | Microsoft.EventHub/sku/regions/read | Listájának SkuRegions erőforrás leírása |
> | Műveletek | Microsoft.EventHub/unregister/action | Az EventHub erőforrás-szolgáltató regisztrálása |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Features/features/read | Beolvassa az előfizetéshez tartozó szolgáltatásokat. |
> | Műveletek | Microsoft.Features/operations/read | A műveletek listájának beolvasása. |
> | Műveletek | Microsoft.Features/providers/features/read | Beolvassa az előfizetés szolgáltatásait az adott erőforrás-szolgáltatón. |
> | Műveletek | Microsoft.Features/providers/features/register/action | Regisztrálja az előfizetéshez tartozó szolgáltatást az adott erőforrás-szolgáltatón. |
> | Műveletek | Microsoft.Features/providers/features/unregister/action | Az előfizetéshez tartozó szolgáltatás regisztrációjának törlése az adott erőforrás-szolgáltatón. |
> | Műveletek | Microsoft.Features/register/action | Az előfizetésben elérhető funkció regisztrálása. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.HDInsight/clusters/changerdpsetting/action | HDInsight-fürt RDP beállításának módosítása |
> | Műveletek | Microsoft.HDInsight/clusters/configurations/action | HDInsight-fürt konfigurációjának frissítése |
> | Műveletek | Microsoft.HDInsight/clusters/configurations/read | HDInsight-fürt konfigurációjának beolvasása |
> | Műveletek | Microsoft.HDInsight/clusters/delete | A HDInsight-fürt törlése |
> | Műveletek | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/read | A HDInsight-fürt erőforrás diagnosztikai beállításának beolvasása. |
> | Műveletek | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti a HDInsight-fürt erőforrás diagnosztikai beállításának |
> | Műveletek | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/metricDefinitions/read | HDInsight-fürthöz elérhető metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.HDInsight/clusters/read | Részletes információkat szolgáltatva HDInsight-fürt |
> | Műveletek | Microsoft.HDInsight/clusters/roles/resize/action | A HDInsight-fürt méretezése |
> | Műveletek | Microsoft.HDInsight/clusters/write | Létrehozni vagy frissíteni a HDInsight-fürt |
> | Műveletek | Microsoft.HDInsight/locations/capabilities/read | Az előfizetési lehetőségek elérése |
> | Műveletek | Microsoft.HDInsight/locations/checkNameAvailability/read | Név foglaltságának ellenőrzése |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ImportExport/jobs/delete | Egy meglévő feladat törlése. |
> | Műveletek | Microsoft.ImportExport/jobs/listBitLockerKeys/action | Lekérdezi a megadott feladathoz tartozó BitLocker-kulcsok. |
> | Műveletek | Microsoft.ImportExport/jobs/read | Tulajdonságait olvassa be a megadott feladathoz tartozó, illetve a feladatok beolvasása. |
> | Műveletek | Microsoft.ImportExport/jobs/write | Létrehoz egy feladatot a megadott paraméterekkel, vagy a Tulajdonságok vagy a megadott feladathoz tartozó címkék frissítése. |
> | Műveletek | Microsoft.ImportExport/locations/read | A megadott hely tulajdonságainak beolvasása, vagy a helyek listáját adja vissza. |
> | Műveletek | Microsoft.ImportExport/register/action | Az importálási/exportálási erőforrás-szolgáltató előfizetésének regisztrálása, és lehetővé teszi, hogy az importálási/exportálási feladatok létrehozása. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Insights/ActionGroups/Delete | Műveletcsoport törlése |
> | Műveletek | Microsoft.Insights/ActionGroups/Read | Műveletcsoport olvasása |
> | Műveletek | Microsoft.Insights/ActionGroups/Write | Műveletcsoport írása |
> | Műveletek | Microsoft.Insights/ActivityLogAlerts/Activated/Action | A rendszer elindította a tevékenységnapló-riasztást |
> | Műveletek | Microsoft.Insights/ActivityLogAlerts/Delete | Tevékenységnapló-riasztás törlése |
> | Műveletek | Microsoft.Insights/ActivityLogAlerts/Read | Tevékenységnapló-riasztás olvasása |
> | Műveletek | Microsoft.Insights/ActivityLogAlerts/Write | Tevékenységnapló-riasztás olvasása |
> | Műveletek | Microsoft.Insights/AlertRules/Activated/Action | Riasztási szabály aktiválva |
> | Műveletek | Microsoft.Insights/AlertRules/Delete | Riasztási szabály konfigurációjának törlése |
> | Műveletek | Microsoft.Insights/AlertRules/Incidents/Read | Riasztási szabály incidenskonfigurációjának beolvasása |
> | Műveletek | Microsoft.Insights/AlertRules/Read | Riasztási szabály konfigurációjának beolvasása |
> | Műveletek | Microsoft.Insights/AlertRules/Resolved/Action | Riasztási szabály feloldva |
> | Műveletek | Microsoft.Insights/AlertRules/Throttled/Action | A riiasztási szabály elindítva |
> | Műveletek | Microsoft.Insights/AlertRules/Write | Riasztási szabály konfigurációjának írása |
> | Műveletek | Microsoft.Insights/AutoscaleSettings/Delete | Automatikus skálázási beállítás konfigurációjának törlése |
> | Műveletek | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | A metrikadefiníciók olvasása |
> | Műveletek | Microsoft.Insights/AutoscaleSettings/Read | Automatikus skálázási beállítás konfigurációjának beolvasása |
> | Műveletek | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | Automatikus vertikális leskálázási művelet |
> | Műveletek | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | Automatikus vertikális felskálázási művelet |
> | Műveletek | Microsoft.Insights/AutoscaleSettings/Write | Automatikus skálázási beállítás konfigurációjának írása |
> | Műveletek | Microsoft.Insights/Components/AnalyticsItems/Delete | Egy Application Insights-elemzési elem törlése |
> | Műveletek | Microsoft.Insights/Components/AnalyticsItems/Read | Egy Application Insights-elemzési elem olvasása |
> | Műveletek | Microsoft.Insights/Components/AnalyticsItems/Write | Egy Application Insights-elemzési elem írása |
> | Műveletek | Microsoft.Insights/Components/AnalyticsTables/Action | Az Application Insights-elemzési táblájának művelete |
> | Műveletek | Microsoft.Insights/Components/AnalyticsTables/Delete | Egy Application Insights-elemzési tábla sémájának törlése |
> | Műveletek | Microsoft.Insights/Components/AnalyticsTables/Read | Egy Application Insights-elemzési tábla sémájának olvasása |
> | Műveletek | Microsoft.Insights/Components/AnalyticsTables/Write | Egy Application Insights-elemzési tábla sémájának írása |
> | Műveletek | Microsoft.Insights/Components/Annotations/Delete | Egy Application Insights-jegyzet törlése |
> | Műveletek | Microsoft.Insights/Components/Annotations/Read | Egy Application Insights-jegyzet olvasása |
> | Műveletek | Microsoft.Insights/Components/Annotations/Write | Egy Application Insights-jegyzet írása |
> | Műveletek | Microsoft.Insights/Components/Api/Read | Application Insights-összetevőadatok API-jának olvasása |
> | Műveletek | Microsoft.Insights/Components/ApiKeys/Action | Egy Application Insights-beli API-kulcs létrehozása |
> | Műveletek | Microsoft.Insights/Components/ApiKeys/Delete | Egy Application Insights-beli API-kulcs törlése |
> | Műveletek | Microsoft.Insights/Components/ApiKeys/Read | Egy Application Insights-beli API-kulcs olvasása |
> | Műveletek | Microsoft.Insights/Components/BillingPlanForComponent/Read | Egy Application Insights-összetevő számlázási csomagjának olvasása |
> | Műveletek | Microsoft.Insights/Components/CurrentBillingFeatures/Read | Application Insights-összetevő aktuális számlázási funkcióinak olvasása |
> | Műveletek | Microsoft.Insights/Components/CurrentBillingFeatures/Write | Application Insights-összetevő aktuális számlázási funkcióinak írása |
> | Műveletek | Microsoft.Insights/Components/DefaultWorkItemConfig/Read | Az Application Insights egy alapértelmezett ALM-integrációja konfigurációjának olvasása |
> | Műveletek | Microsoft.Insights/Components/Delete | Application Insights-összetevő konfigurációjának törlése |
> | Műveletek | Microsoft.Insights/Components/ExportConfiguration/Action | Az Application Insights exportálási beállításainak művelete |
> | Műveletek | Microsoft.Insights/Components/ExportConfiguration/Delete | Az Application Insights exportálási beállításainak törlése |
> | Műveletek | Microsoft.Insights/Components/ExportConfiguration/Read | Az Application Insights exportálási beállításainak olvasása |
> | Műveletek | Microsoft.Insights/Components/ExportConfiguration/Write | Az Application Insights exportálási beállításainak írása |
> | Műveletek | Microsoft.Insights/Components/ExtendQueries/Read | Application Insights-összetevő bővített lekérdezési eredményeinek olvasása |
> | Műveletek | Microsoft.Insights/Components/Favorites/Delete | Egy Application Insights-kedvenc törlése |
> | Műveletek | Microsoft.Insights/Components/Favorites/Read | Egy Application Insights-kedvenc olvasása |
> | Műveletek | Microsoft.Insights/Components/Favorites/Write | Egy Application Insights-kedvenc írása |
> | Műveletek | Microsoft.Insights/Components/FeatureCapabilities/Read | Application Insights-összetevő funkciói lehetőségeinek olvasása |
> | Műveletek | Microsoft.Insights/Components/GetAvailableBillingFeatures/Read | Application Insights-összetevő elérhető számlázási funkcióinak olvasása |
> | Műveletek | Microsoft.Insights/Components/GetToken/Read | Egy Application Insights-összetevő jogkivonatának olvasása |
> | Műveletek | Microsoft.Insights/Components/MetricDefinitions/Read | Application Insights-összetevő metrikadefinícióinak olvasása |
> | Műveletek | Microsoft.Insights/Components/Metrics/Read | Application Insights-összetevő metrikáinak olvasása |
> | Műveletek | Microsoft.Insights/Components/Move/Action | Application Insights-összetevő áthelyezése másik erőforráscsoportba vagy előfizetésbe |
> | Műveletek | Microsoft.Insights/Components/MyAnalyticsItems/Delete | Egy személyes Application Insights-elemzési elem törlése |
> | Műveletek | Microsoft.Insights/Components/MyAnalyticsItems/Read | Egy személyes Application Insights-elemzési elem olvasása |
> | Műveletek | Microsoft.Insights/Components/MyAnalyticsItems/Write | Egy személyes Application Insights-elemzési elem írása |
> | Műveletek | Microsoft.Insights/Components/MyFavorites/Read | Egy személyes Application Insights-kedvenc olvasása |
> | Műveletek | Microsoft.Insights/Components/PricingPlans/Read | Egy Application Insights-összetevő tarifacsomagjának olvasása |
> | Műveletek | Microsoft.Insights/Components/PricingPlans/Write | Egy Application Insights-összetevő tarifacsomagjának írása |
> | Műveletek | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | Az Application Insights Proaktív problémaészlelés konfigurációjának olvasása |
> | Műveletek | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | Az Application Insights Proaktív problémaészlelés konfigurációjának írása |
> | Műveletek | Microsoft.Insights/Components/providers/Microsoft.Insights/MetricDefinitions/Read | A metrikadefiníciók olvasása |
> | Műveletek | Microsoft.Insights/Components/QuotaStatus/Read | Application Insights-összetevő kvótaállapotának olvasása |
> | Műveletek | Microsoft.Insights/Components/Read | Application Insights-összetevő konfigurációjának olvasása |
> | Műveletek | Microsoft.Insights/Components/SyntheticMonitorLocations/Read | Application Insights-webtesztek helyének olvasása |
> | Műveletek | Microsoft.Insights/Components/WorkItemConfigs/Delete | Az Application Insights egy ALM integrációja konfigurációjának törlése |
> | Műveletek | Microsoft.Insights/Components/WorkItemConfigs/Read | Az Application Insights egy ALM-integrációja konfigurációjának olvasása |
> | Műveletek | Microsoft.Insights/Components/WorkItemConfigs/Write | Az Application Insights egy ALM-integrációja konfigurációjának írása |
> | Műveletek | Microsoft.Insights/Components/Write | Application Insights-összetevő konfigurációjának írása |
> | Műveletek | Microsoft.Insights/DiagnosticSettings/Delete | Diagnosztikai beállítások konfigurációjának törlése |
> | Műveletek | Microsoft.Insights/DiagnosticSettings/Read | Diagnosztikai beállítások konfigurációjának olvasása |
> | Műveletek | Microsoft.Insights/DiagnosticSettings/Write | Diagnosztikai beállítások konfigurációjának írása |
> | Műveletek | Microsoft.Insights/EventCategories/Read | Eseménykategória olvasása |
> | Műveletek | Microsoft.Insights/eventtypes/digestevents/Read | A felügyeleti eseménytípus kivonatának olvasása |
> | Műveletek | Microsoft.Insights/eventtypes/values/Read | A felügyeleti eseménytípus értékeinek olvasása |
> | Műveletek | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | Bővített diagnosztikai beállítások konfigurációjának törlése |
> | Műveletek | Microsoft.Insights/ExtendedDiagnosticSettings/Read | Bővített diagnosztikai beállítások konfigurációjának olvasása |
> | Műveletek | Microsoft.Insights/ExtendedDiagnosticSettings/Write | Bővített diagnosztikai beállítások konfigurációjának írása |
> | Műveletek | Microsoft.Insights/ListMigrationDate/Action | Előfizetés migrálási dátumának visszaolvasása |
> | Műveletek | Microsoft.Insights/ListMigrationDate/Read | Előfizetés migrálási dátumának visszaolvasása |
> | Műveletek | Microsoft.Insights/LogDefinitions/Read | A naplódefiníciók olvasása |
> | Műveletek | Microsoft.Insights/LogProfiles/Delete | Naplóprofilok konfigurációjának törlése |
> | Műveletek | Microsoft.Insights/LogProfiles/Read | Naplóprofilok olvasása |
> | Műveletek | Microsoft.Insights/LogProfiles/Write | Naplóprofil-konfigurációba való írás |
> | Műveletek | Microsoft.Insights/MetricAlerts/Delete | Metrikariasztás törlése |
> | Műveletek | Microsoft.Insights/MetricAlerts/Read | Metrikariasztás olvasása |
> | Műveletek | Microsoft.Insights/MetricAlerts/Status/Read | Metrikaalapú riasztás állapotának olvasása |
> | Műveletek | Microsoft.Insights/MetricAlerts/Write | Metrikariasztás írása |
> | Műveletek | Microsoft.Insights/MetricDefinitions/Microsoft.Insights/Read | A metrikadefiníciók olvasása |
> | Műveletek | Microsoft.Insights/MetricDefinitions/providers/Microsoft.Insights/Read | A metrikadefiníciók olvasása |
> | Műveletek | Microsoft.Insights/MetricDefinitions/Read | A metrikadefiníciók olvasása |
> | Műveletek | Microsoft.Insights/Metrics/providers/Metrics/Read | Metrikák olvasása |
> | Műveletek | Microsoft.Insights/Metrics/Read | Metrikák olvasása |
> | Műveletek | Microsoft.Insights/Metrics/Write | Metrikák írása |
> | Műveletek | Microsoft.Insights/MigrateToNewpricingModel/Action | Előfizetés migrálása új díjszabási modellbe |
> | Műveletek | Microsoft.Insights/Operations/Read | Olvasási műveletek |
> | Műveletek | Microsoft.Insights/Register/Action | A Microsoft Insights-szolgáltató regisztrálása |
> | Műveletek | Microsoft.Insights/RollbackToLegacyPricingModel/Action | Előfizetés visszaállítása a régi díjszabási modellre |
> | Műveletek | Microsoft.Insights/ScheduledQueryRules/Delete | Ütemezett lekérdezés szabály törlése |
> | Műveletek | Microsoft.Insights/ScheduledQueryRules/Read | Egy ütemezett lekérdezési szabály olvasása |
> | Műveletek | Microsoft.Insights/ScheduledQueryRules/Write | Ütemezett lekérdezési szabály írása |
> | Műveletek | Microsoft.Insights/Tenants/Register/Action | A Microsoft Insights-szolgáltató inicializálása |
> | Műveletek | Microsoft.Insights/Unregister/Action | A Microsoft Insights-szolgáltató regisztrálása |
> | Műveletek | Microsoft.Insights/Webtests/Delete | Webteszt-konfiguráció törlése |
> | Műveletek | Microsoft.Insights/Webtests/GetToken/Read | Egy webteszt jogkivonatának olvasása |
> | Műveletek | Microsoft.Insights/Webtests/MetricDefinitions/Read | Egy webteszt metrikadefinícióinak olvasása |
> | Műveletek | Microsoft.Insights/Webtests/Metrics/Read | Egy webteszt metrikáinak olvasása |
> | Műveletek | Microsoft.Insights/Webtests/Read | Webteszt-konfiguráció olvasása |
> | Műveletek | Microsoft.Insights/Webtests/Write | Webteszt-konfiguráció írása |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.KeyVault/checkNameAvailability/read | Annak ellenőrzése, hogy érvényes-e a kulcstartónév, és nincs-e használatban |
> | Műveletek | Microsoft.KeyVault/deletedVaults/read | Helyreállíthatóan törölt kulcstartók tulajdonságainak megtekintése |
> | Műveletek | Microsoft.KeyVault/hsmPools/delete | HSM-készlet törlése |
> | Műveletek | Microsoft.KeyVault/hsmPools/joinVault/action | Kulcstartó HSM-készlethez való csatlakoztatása |
> | Műveletek | Microsoft.KeyVault/hsmPools/read | A HSM-készlet tulajdonságainak megtekintése |
> | Műveletek | Microsoft.KeyVault/hsmPools/write | Új HSM-készlet létrehozása vagy egy meglévő HSM-készlet tulajdonságainak frissítése |
> | Műveletek | Microsoft.KeyVault/locations/deletedVaults/purge/action | Helyreállíthatóan törölt kulcstartó végleges törlése |
> | Műveletek | Microsoft.KeyVault/locations/deletedVaults/read | Helyreállíthatóan törölt kulcstartó tulajdonságainak megtekintése |
> | Műveletek | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | A Microsoft.KeyVault értesítése arról, hogy virtuális hálózat vagy alhálózat törlése folyamatban van |
> | Műveletek | Microsoft.KeyVault/locations/operationResults/read | Hosszú futtatású művelet eredményének ellenőrzése |
> | Műveletek | Microsoft.KeyVault/operations/read | A Microsoft.KeyVault erőforrás-szolgáltató elérhető műveleteinek listázása |
> | Műveletek | Microsoft.KeyVault/register/action | Az előfizetés regisztrálása |
> | Műveletek | Microsoft.KeyVault/unregister/action | Előfizetés regisztrációjának törlése |
> | Műveletek | Microsoft.KeyVault/vaults/accessPolicies/write | Egyesítéssel vagy cserével frissíthet egy meglévő hozzáférési szabályzatot, vagy létrehozhat egy új hozzáférési szabályzatot a tárolóban. |
> | Műveletek | Microsoft.KeyVault/vaults/delete | Kulcstároló törlése |
> | Műveletek | Microsoft.KeyVault/vaults/deploy/action | A kulcstartóban található titkos kódokhoz való hozzáférés engedélyezése Azure-erőforrások üzembe helyezésekor |
> | Műveletek | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/diagnosticSettings/Read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/diagnosticSettings/Write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/logDefinitions/read | Beolvassa a kulcstartóhoz elérhető naplókat |
> | Műveletek | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/metricDefinitions/read | Beolvassa a kulcstartóhoz elérhető metrikákat |
> | Műveletek | Microsoft.KeyVault/vaults/read | A kulcstartó tulajdonságainak megtekintése |
> | Műveletek | Microsoft.KeyVault/vaults/secrets/read | A titkos kód tulajdonságainak megtekintése, kivéve a kód értékét |
> | Műveletek | Microsoft.KeyVault/vaults/secrets/write | Új titkos kód létrehozása, vagy meglévő titkos kód értékének módosítása |
> | Műveletek | Microsoft.KeyVault/vaults/write | Új kulcstartó létrehozása, vagy meglévő kulcstartó tulajdonságainak módosítása |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.LabServices/labAccounts/CreateLab/action | Labor létrehozása a tesztkörnyezeti fiók. |
> | Műveletek | Microsoft.LabServices/labAccounts/delete | Tesztkörnyezet törlését. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/delete | Labs törlése. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/ClaimAny/action | Jogcím-egy véletlenszerű környezetben egy felhasználó egy környezet beállításaiban |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | Törölje a környezeti beállítás. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Claim/action | A környezet jogcímekből, és hozzárendeli a felhasználót |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | Törölje a környezetben. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | Olvassa el a környezetben. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | Elindítja a környezet összes erőforráshoz, a környezet elindításával. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | A környezet összes erőforráshoz leállításával leállítja a környezet |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/write | Hozzáadása vagy módosítása a környezetben. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | Rendelkezések/deprovisions szükséges erőforrások beállítása környezet aktuális állapotát, a laborkörnyezet/beállítás alapján. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | Olvassa el a környezeti beállítás. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | Elindítja a sablon összes erőforráshoz, a sablon elindításával. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | Elindítja a sablon összes erőforráshoz, a sablon elindításával. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | Hozzáadása vagy módosítása a környezeti beállítás. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/read | Olvassa el a labs. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/Register/action | Regisztrálja a felügyelt laborkörnyezetben. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/users/delete | Felhasználók törlése. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/users/read | Olvassa el a felhasználók. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/users/write | Hozzáadása vagy módosítása a felhasználók. |
> | Műveletek | Microsoft.LabServices/labAccounts/labs/write | Hozzáadása vagy módosítása labs. |
> | Műveletek | Microsoft.LabServices/labAccounts/read | Olvassa el a labor fiókok. |
> | Műveletek | Microsoft.LabServices/labAccounts/sizes/read | Olvassa el a méretét. |
> | Műveletek | Microsoft.LabServices/labAccounts/write | Hozzáadása vagy módosítása a labor fiókok. |
> | Műveletek | Microsoft.LabServices/locations/operations/read | Az olvasási műveletek. |
> | Műveletek | Microsoft.LabServices/register/action | Az előfizetés regisztrálása |
> | Műveletek | Microsoft.LabServices/users/GetEnvironment/action | Lekérdezi a virtuális gép részletei |
> | Műveletek | Microsoft.LabServices/users/ListEnvironments/action | A felhasználó lista környezetekben |
> | Műveletek | Microsoft.LabServices/users/ListLabs/action | A felhasználó labs listája. |
> | Műveletek | Microsoft.LabServices/users/Register/action | Egy felügyelt, amikor a felhasználó regisztrálása |
> | Műveletek | Microsoft.LabServices/users/StartEnvironment/action | Elindítja a környezet összes erőforráshoz, a környezet elindításával. |
> | Műveletek | Microsoft.LabServices/users/StopEnvironment/action | A környezet összes erőforráshoz leállításával leállítja a környezet |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.LocationBasedServices/accounts/delete | (Elavult: használja a /providers/Microsoft.Maps) Egy hely alapú törlése szolgáltatásfiók. |
> | Műveletek | Microsoft.LocationBasedServices/accounts/listKeys/action | (Elavult: használja a /providers/Microsoft.Maps) Hely alapú Services-fiók listázása |
> | Műveletek | Microsoft.LocationBasedServices/accounts/providers/Microsoft.Insights/diagnosticSettings/read | (Elavult: használja a /providers/Microsoft.Maps) Az erőforrás diagnosztikai beállításának beolvasása. |
> | Műveletek | Microsoft.LocationBasedServices/accounts/providers/Microsoft.Insights/diagnosticSettings/write | (Elavult: használja a /providers/Microsoft.Maps) Létrehozza vagy frissíti az erőforrás diagnosztikai beállításának |
> | Műveletek | Microsoft.LocationBasedServices/accounts/providers/Microsoft.Insights/metricDefinitions/read | (Elavult: használja a /providers/Microsoft.Maps) A hely alapú szolgáltatások fiókok elérhető metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.LocationBasedServices/accounts/read | (Elavult: használja a /providers/Microsoft.Maps) Egy alapú helyét szolgáltatásfiók. |
> | Műveletek | Microsoft.LocationBasedServices/accounts/regenerateKey/action | (Elavult: használja a /providers/Microsoft.Maps) Hozzon létre új hely alapú Services-fiók elsődleges vagy másodlagos kulcsot |
> | Műveletek | Microsoft.LocationBasedServices/accounts/write | (Elavult: használja a /providers/Microsoft.Maps) Fiók létrehozása vagy frissítése egy hely alapú szolgáltatások. |
> | Műveletek | Microsoft.LocationBasedServices/register/action | (Elavult: használja a /providers/Microsoft.Maps) Regisztrálja a szolgáltatót |

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | DataAction | Microsoft.LogAnalytics/logs/Alert/read | A riasztási táblából adatok olvasása |
> | DataAction | Microsoft.LogAnalytics/logs/AzureActivity/read | Adatok olvasása a AzureActivity táblából |
> | DataAction | Microsoft.LogAnalytics/logs/AzureMetrics/read | Adatok olvasása a AzureMetrics táblából |
> | DataAction | Microsoft.LogAnalytics/logs/CommonSecurityLog/read | Adatok olvasása a CommonSecurityLog táblából |
> | DataAction | Microsoft.LogAnalytics/logs/ComputerGroup/read | ComputerGroup táblázat adatok olvasása |
> | DataAction | Microsoft.LogAnalytics/logs/CustomLogs/read | Adatok beolvasása a * _CL tábla |
> | DataAction | Microsoft.LogAnalytics/logs/ETWEvent/read | Adatok olvasása a ETWEvent táblából |
> | DataAction | Microsoft.LogAnalytics/logs/Event/read | Az esemény táblából adatok olvasása |
> | DataAction | Microsoft.LogAnalytics/logs/Heartbeat/read | A szívverés táblából adatok olvasása |
> | DataAction | Microsoft.LogAnalytics/logs/LinuxAuditLog/read | Adatok olvasása a LinuxAuditLog táblából |
> | DataAction | Microsoft.LogAnalytics/logs/Operation/read | A művelet táblából adatok olvasása |
> | DataAction | Microsoft.LogAnalytics/logs/Perf/read | A teljesítményadat-táblából adatok olvasása |
> | DataAction | Microsoft.LogAnalytics/logs/ProtectionStatus/read | Adatok olvasása a ProtectionStatus táblából |
> | DataAction | Microsoft.LogAnalytics/logs/read | A naplók adatok olvasása |
> | DataAction | Microsoft.LogAnalytics/logs/ReservedAzureCommonFields/read | Adatok olvasása a ReservedAzureCommonFields táblából |
> | DataAction | Microsoft.LogAnalytics/logs/ReservedCommonFields/read | Adatok olvasása a ReservedCommonFields táblából |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityAlert/read | Adatok olvasása a SecurityAlert táblából |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityBaseline/read | Adatok olvasása a SecurityBaseline táblából |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityBaselineSummary/read | Adatok olvasása a SecurityBaselineSummary táblából |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityDetection/read | Adatok olvasása a SecurityDetection táblából |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityEvent/read | Adatok olvasása a SecurityEvent táblából |
> | DataAction | Microsoft.LogAnalytics/logs/ServiceFabricOperationalEvent/read | Adatok olvasása a ServiceFabricOperationalEvent táblából |
> | DataAction | Microsoft.LogAnalytics/logs/ServiceFabricReliableActorEvent/read | Adatok olvasása a ServiceFabricReliableActorEvent táblából |
> | DataAction | Microsoft.LogAnalytics/logs/ServiceFabricReliableServiceEvent/read | Adatok olvasása a ServiceFabricReliableServiceEvent táblából |
> | DataAction | Microsoft.LogAnalytics/logs/Syslog/read | A Syslog-táblából adatok olvasása |
> | DataAction | Microsoft.LogAnalytics/logs/SysmonEvent/read | Adatok olvasása a SysmonEvent táblából |
> | DataAction | Microsoft.LogAnalytics/logs/Update/read | A frissítés táblából adatok olvasása |
> | DataAction | Microsoft.LogAnalytics/logs/UpdateSummary/read | Adatok olvasása a UpdateSummary táblából |
> | DataAction | Microsoft.LogAnalytics/logs/Usage/read | A használati táblából adatok olvasása |
> | DataAction | Microsoft.LogAnalytics/logs/W3CIISLog/read | Adatok olvasása a W3CIISLog táblából |
> | DataAction | Microsoft.LogAnalytics/logs/WindowsFirewall/read | Adatok olvasása a WindowsFirewall táblából |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Logic/integrationAccounts/agreements/delete | Egy integrációs fiókban lévő megállapodás törlése. |
> | Műveletek | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | Egy integrációs fiókban lévő megállapodástartalom visszahívási URL-címének beolvasása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/agreements/read | Egy integrációs fiókban lévő megállapodás beolvasása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/agreements/write | A megállapodás létrehozása vagy frissítése egy integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/assemblies/delete | Egy integrációs fiókban lévő szerelvény törlése. |
> | Műveletek | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | Egy integrációs fiókban lévő szerelvénytartalom visszahívási URL-címének beolvasása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/assemblies/read | Egy integrációs fiókban lévő szerelvény beolvasása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/assemblies/write | A szerelvény létrehozása vagy frissítése egy integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | Egy integrációs fiókban lévő kötegkonfiguráció törlése. |
> | Műveletek | Microsoft.Logic/integrationAccounts/batchConfigurations/read | Egy integrációs fiókban lévő kötegkonfiguráció beolvasása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/batchConfigurations/write | A kötegkonfiguráció létrehozása vagy frissítése egy integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/certificates/delete | Egy integrációs fiókban lévő tanúsítvány törlése. |
> | Műveletek | Microsoft.Logic/integrationAccounts/certificates/read | Egy integrációs fiókban lévő tanúsítvány beolvasása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/certificates/write | A tanúsítvány létrehozása vagy frissítése egy integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/delete | Egy integrációs fiók törlése. |
> | Műveletek | Microsoft.Logic/integrationAccounts/join/action | Az integrációs fiók csatlakoztatása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | Egy integrációs fiók visszahívási URL-címének beolvasása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | Egy kulcstartóban lévő kulcsok beolvasása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | Egy integrációs fiókban bekövetkező követési események naplózása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/maps/delete | Egy integrációs fiókban lévő leképezés törlése. |
> | Műveletek | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | Egy integrációs fiókban lévő hozzárendelési tartalom visszahívási URL-címének beolvasása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/maps/read | Egy integrációs fiókban lévő hozzárendelés beolvasása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/maps/write | A hozzárendelés létrehozása vagy frissítése egy integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/partners/delete | Egy integrációs fiókban lévő partner törlése. |
> | Műveletek | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | Egy integrációs fiókban lévő partnertartalom visszahívási URL-címének beolvasása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/partners/read | Egy integrációs fiókban lévő partner beolvasása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/partners/write | A partner létrehozása vagy frissítése egy integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | Az integrációs fiók naplódefinícióinak olvasása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/read | Egy integrációs fiók beolvasása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | Újra létrehozza a hívóbetű titkos kulcsait. |
> | Műveletek | Microsoft.Logic/integrationAccounts/schemas/delete | Egy integrációs fiókban lévő séma törlése. |
> | Műveletek | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | Egy integrációs fiókban lévő sématartalom visszahívási URL-címének beolvasása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/schemas/read | Egy integrációs fiókban lévő séma beolvasása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/schemas/write | A séma létrehozása vagy frissítése egy integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/sessions/delete | Egy integrációs fiókban lévő munkamenet törlése. |
> | Műveletek | Microsoft.Logic/integrationAccounts/sessions/read | Egy integrációs fiókban lévő kötegkonfiguráció beolvasása. |
> | Műveletek | Microsoft.Logic/integrationAccounts/sessions/write | A munkamenet létrehozása vagy frissítése egy integrációs fiókban. |
> | Műveletek | Microsoft.Logic/integrationAccounts/write | Egy integrációs fiók létrehozása vagy frissítése. |
> | Műveletek | Microsoft.Logic/locations/workflows/validate/action | Érvényesíti a munkafolyamatot. |
> | Műveletek | Microsoft.Logic/operations/read | Egy művelet beolvasása. |
> | Műveletek | Microsoft.Logic/register/action | A Microsoft.Logic erőforrás-szolgáltató regisztrálása egy adott előfizetéshez. |
> | Műveletek | Microsoft.Logic/workflows/accessKeys/delete | Törli a hívóbetűt. |
> | Műveletek | Microsoft.Logic/workflows/accessKeys/list/action | Listázza a hívóbetű titkos kulcsait. |
> | Műveletek | Microsoft.Logic/workflows/accessKeys/read | Beolvassa a hívóbetűt. |
> | Műveletek | Microsoft.Logic/workflows/accessKeys/regenerate/action | Újra létrehozza a hívóbetű titkos kulcsait. |
> | Műveletek | Microsoft.Logic/workflows/accessKeys/write | Létrehozza vagy frissíti a hívóbetűt. |
> | Műveletek | Microsoft.Logic/workflows/delete | Törli a munkafolyamatot. |
> | Műveletek | Microsoft.Logic/workflows/disable/action | Letiltja a munkafolyamatot. |
> | Műveletek | Microsoft.Logic/workflows/enable/action | Engedélyezi a munkafolyamatot. |
> | Műveletek | Microsoft.Logic/workflows/listCallbackUrl/action | Egy munkafolyamat visszahívási URL-címének beolvasása. |
> | Műveletek | Microsoft.Logic/workflows/listSwagger/action | A munkafolyamat Swagger-definícióinak beolvasása. |
> | Műveletek | Microsoft.Logic/workflows/move/action | A munkafolyamatot a meglévő előfizetés-azonosítójáról, erőforráscsoportjából és/vagy nevéről egy másik előfizetés-azonosítóra, erőforráscsoportba és/vagy névre helyezi át. |
> | Műveletek | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/read | Beolvassa a munkafolyamat diagnosztikai beállításait. |
> | Műveletek | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti a munkafolyamat diagnosztikai beállításait. |
> | Műveletek | Microsoft.Logic/workflows/providers/Microsoft.Insights/logDefinitions/read | Beolvassa a munkafolyamat naplómeghatározásait. |
> | Műveletek | Microsoft.Logic/workflows/providers/Microsoft.Insights/metricDefinitions/read | Beolvassa a munkafolyamat metrikai meghatározásait. |
> | Műveletek | Microsoft.Logic/workflows/read | Beolvassa a munkafolyamatot. |
> | Műveletek | Microsoft.Logic/workflows/regenerateAccessKey/action | Újra létrehozza a hívóbetű titkos kulcsait. |
> | Műveletek | Microsoft.Logic/workflows/run/action | Elindítja egy munkafolyamat futtatását. |
> | Műveletek | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | A munkafolyamat-futtatási műveleti kifejezés követési adatainak beolvasása. |
> | Műveletek | Microsoft.Logic/workflows/runs/actions/read | Beolvassa a munkafolyamat-futtatási műveletet. |
> | Műveletek | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | Egy munkafolyamat-futtatási művelet ismétlődési kifejezéskövetési adatainak beolvasása. |
> | Műveletek | Microsoft.Logic/workflows/runs/actions/repetitions/read | Egy munkafolyamat-futtatási művelet ismétlődésének beolvasása. |
> | Műveletek | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | Egy munkafolyamat-futtatási művelet hatóköre ismétlődésének beolvasása. |
> | Műveletek | Microsoft.Logic/workflows/runs/cancel/action | Megszakítja egy munkafolyamat futtatását. |
> | Műveletek | Microsoft.Logic/workflows/runs/operations/read | Beolvassa a munkafolyamat-futtatás műveleti állapotát. |
> | Műveletek | Microsoft.Logic/workflows/runs/read | Beolvassa a munkafolyamat-futtatást. |
> | Műveletek | Microsoft.Logic/workflows/suspend/action | Egy munkafolyamat felfüggesztése. |
> | Műveletek | Microsoft.Logic/workflows/triggers/histories/read | Beolvassa a triggerelőzményeket. |
> | Műveletek | Microsoft.Logic/workflows/triggers/histories/resubmit/action | A munkafolyamat-trigger újraküldése. |
> | Műveletek | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | Egy trigger visszahívási URL-címének beolvasása. |
> | Műveletek | Microsoft.Logic/workflows/triggers/read | Beolvassa a triggert. |
> | Műveletek | Microsoft.Logic/workflows/triggers/reset/action | A trigger alaphelyzetbe állítása. |
> | Műveletek | Microsoft.Logic/workflows/triggers/run/action | Végrehajtja a triggert. |
> | Műveletek | Microsoft.Logic/workflows/triggers/setState/action | A trigger állapotának beállítása. |
> | Műveletek | Microsoft.Logic/workflows/validate/action | Érvényesíti a munkafolyamatot. |
> | Műveletek | Microsoft.Logic/workflows/versions/read | Beolvassa a munkafolyamat verzióját. |
> | Műveletek | Microsoft.Logic/workflows/versions/triggers/listCallbackUrl/action | Egy trigger visszahívási URL-címének beolvasása. |
> | Műveletek | Microsoft.Logic/workflows/write | Létrehozza vagy frissíti a munkafolyamatot. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | Helyezze át a gépi tanulási kötelezettségvállalás terv társítása |
> | Műveletek | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | Olvassa el a gépi tanulási kötelezettségvállalás terv társítása |
> | Műveletek | Microsoft.MachineLearning/commitmentPlans/delete | Gépi tanulás kötelezettségvállalás terv törlése |
> | Műveletek | Microsoft.MachineLearning/commitmentPlans/join/action | Csatlakozás a gépi tanulási kötelezettségvállalás terv |
> | Műveletek | Microsoft.MachineLearning/commitmentPlans/read | Olvassa el a gépi tanulási kötelezettségvállalás terv |
> | Műveletek | Microsoft.MachineLearning/commitmentPlans/write | Hozzon létre vagy bármely Machine Learning előfizetési csomag frissítése |
> | Műveletek | Microsoft.MachineLearning/locations/operationresults/read | A Machine Learning művelet eredményének beolvasása |
> | Műveletek | Microsoft.MachineLearning/locations/operationsstatus/read | A Machine Learning futó művelet állapotának beolvasása |
> | Műveletek | Microsoft.MachineLearning/operations/read | Gépi tanulási műveletek beolvasása |
> | Műveletek | Microsoft.MachineLearning/register/action | A machine learning web service erőforrás-szolgáltató előfizetésének regisztrálása, és lehetővé teszi a webszolgáltatások létrehozását. |
> | Műveletek | Microsoft.MachineLearning/skus/read | Gépi tanulási kötelezettségvállalás terv termékváltozatok beolvasása |
> | Műveletek | Microsoft.MachineLearning/webServices/action | A támogatott régiók regionális webes szolgáltatás tulajdonságok létrehozása |
> | Műveletek | Microsoft.MachineLearning/webServices/delete | A Machine Learning webszolgáltatás törlése |
> | Műveletek | Microsoft.MachineLearning/webServices/listkeys/read | A Machine Learning webszolgáltatás-kulcsok beszerzése |
> | Műveletek | Microsoft.MachineLearning/webServices/read | Olvassa el a Machine Learning webszolgáltatás |
> | Műveletek | Microsoft.MachineLearning/webServices/write | Létrehozni vagy frissíteni a Machine Learning webszolgáltatás |
> | Műveletek | Microsoft.MachineLearning/Workspaces/delete | Gépi tanulás munkaterület törlése |
> | Műveletek | Microsoft.MachineLearning/Workspaces/listworkspacekeys/action | A Machine Learning-munkaterület listában kulcsok |
> | Műveletek | Microsoft.MachineLearning/Workspaces/read | Olvassa el a gépi tanulási munkaterület |
> | Műveletek | Microsoft.MachineLearning/Workspaces/resyncstoragekeys/action | A Machine Learning-munkaterület beállítása tárfiók kulcsait újraszinkronizálásra |
> | Műveletek | Microsoft.MachineLearning/Workspaces/write | Létrehozni vagy frissíteni a gépi tanulási munkaterület |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.MachineLearningCompute/operationalizationClusters/checkUpdate/action | Ha frissítések érhetők el a operationalization fürt-szolgáltatások ellenőrzése |
> | Műveletek | Microsoft.MachineLearningCompute/operationalizationClusters/delete | Bármely üzemeltetési fiók törlése |
> | Műveletek | Microsoft.MachineLearningCompute/operationalizationClusters/listKeys/action | Operationalization-fürthöz tartozó kulcsainak listázása |
> | Műveletek | Microsoft.MachineLearningCompute/operationalizationClusters/read | Bármely olyan üzemeltetési fiók olvasása |
> | Műveletek | Microsoft.MachineLearningCompute/operationalizationClusters/updateSystem/action | A rendszerszolgáltatások operationalization fürt frissítése |
> | Műveletek | Microsoft.MachineLearningCompute/operationalizationClusters/write | Bármely üzemeltetési fiók létrehozása vagy frissítése |
> | Műveletek | Microsoft.MachineLearningCompute/register/action | Az erőforrás-szolgáltató az előfizetés-azonosító regisztrálja, és lehetővé teszi a machine learning-számítási erőforrásokat létrehozását |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.MachineLearningModelManagement/accounts/delete | Bármely üzemeltetési fiók törlése |
> | Műveletek | Microsoft.MachineLearningModelManagement/accounts/read | Bármely olyan üzemeltetési fiók olvasása |
> | Műveletek | Microsoft.MachineLearningModelManagement/accounts/write | Bármely üzemeltetési fiók létrehozása vagy frissítése |
> | Műveletek | Microsoft.MachineLearningModelManagement/register/action | Az erőforrás-szolgáltató az előfizetés-azonosító regisztrálja, és lehetővé teszi egy üzemeltetési fiók létrehozását |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.MachineLearningServices/register/action | A Machine Learning szolgáltatások erőforrás-szolgáltató előfizetésének regisztrálása |
> | Műveletek | Microsoft.MachineLearningServices/workspaces/computes/delete | A Machine Learning szolgáltatás munkaterületek számítási erőforrások törlése |
> | Műveletek | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | Titkos kulcsainak listázása a számítási erőforrásokat Machine Learning szolgáltatások munkaterület |
> | Műveletek | Microsoft.MachineLearningServices/workspaces/computes/read | A számítási erőforrásokat lekérdezi a Machine Learning szolgáltatás munkaterületek |
> | Műveletek | Microsoft.MachineLearningServices/workspaces/computes/write | Létrehozza vagy frissíti a számítási erőforrásokat a Machine Learning szolgáltatás munkaterületek |
> | Műveletek | Microsoft.MachineLearningServices/workspaces/delete | Törli a gépi tanulási szolgáltatások munkaterületek |
> | Műveletek | Microsoft.MachineLearningServices/workspaces/listKeys/action | A Machine Learning szolgáltatások munkaterület titkos kulcsainak listázása |
> | Műveletek | Microsoft.MachineLearningServices/workspaces/read | Lekérdezi a gépi tanulási szolgáltatások munkaterületek |
> | Műveletek | Microsoft.MachineLearningServices/workspaces/write | Létrehozza vagy frissíti a Machine Learning szolgáltatás munkaterületek |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | Szerepalapú művelet egy meglévő felhasználó hozzárendelése egy erőforráshoz hozzárendelt identitás |
> | Műveletek | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Egy meglévő felhasználó lehet hozzárendelve identitás törlése |
> | Műveletek | Microsoft.ManagedIdentity/userAssignedIdentities/read | Lekérdezi egy meglévő felhasználó lehet hozzárendelve identitás |
> | Műveletek | Microsoft.ManagedIdentity/userAssignedIdentities/write | Egy új felhasználó lehet hozzárendelve identitás létrehoz vagy frissít egy meglévő felhasználó lehet hozzárendelve identitás társított címkék |

## <a name="microsoftmanagedlab"></a>Microsoft.ManagedLab

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ManagedLab/labAccounts/CreateLab/action | Labor létrehozása a tesztkörnyezeti fiók. |
> | Műveletek | Microsoft.ManagedLab/labAccounts/delete | Tesztkörnyezet törlését. |
> | Műveletek | Microsoft.ManagedLab/labAccounts/labs/delete | Labs törlése. |
> | Műveletek | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/delete | Törölje a környezeti beállítás. |
> | Műveletek | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/delete | Törölje a környezetben. |
> | Műveletek | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/read | Olvassa el a környezetben. |
> | Műveletek | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/write | Hozzáadása vagy módosítása a környezetben. |
> | Műveletek | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/read | Olvassa el a környezeti beállítás. |
> | Műveletek | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/write | Hozzáadása vagy módosítása a környezeti beállítás. |
> | Műveletek | Microsoft.ManagedLab/labAccounts/labs/labVms/delete | Törölje a labor virtuális gépeket. |
> | Műveletek | Microsoft.ManagedLab/labAccounts/labs/labVms/read | Olvassa el a labor virtuális gépeken. |
> | Műveletek | Microsoft.ManagedLab/labAccounts/labs/labVms/write | Hozzáadása vagy módosítása a labor virtuális gépeken. |
> | Műveletek | Microsoft.ManagedLab/labAccounts/labs/read | Olvassa el a labs. |
> | Műveletek | Microsoft.ManagedLab/labAccounts/labs/write | Hozzáadása vagy módosítása labs. |
> | Műveletek | Microsoft.ManagedLab/labAccounts/read | Olvassa el a labor fiókok. |
> | Műveletek | Microsoft.ManagedLab/labAccounts/write | Hozzáadása vagy módosítása a labor fiókok. |
> | Műveletek | Microsoft.ManagedLab/locations/operations/read | Az olvasási műveletek. |
> | Műveletek | Microsoft.ManagedLab/register/action | Az előfizetés regisztrálása |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Management/checkNameAvailability/action | Ellenőrzi, hogy a megadott felügyeleti csoport neve érvényes és egyedi. |
> | Műveletek | Microsoft.Management/getEntities/action | Összes entitás (felügyeleti csoportok, előfizetések stb.) a hitelesített felhasználók listázása. |
> | Műveletek | Microsoft.Management/managementGroups/delete | Felügyeleti csoport törlése. |
> | Műveletek | Microsoft.Management/managementGroups/read | A hitelesített felhasználók listáját a felügyeleti csoportok. |
> | Műveletek | Microsoft.Management/managementGroups/subscriptions/delete | Előfizetés a felügyeleti csoportból való társítja. |
> | Műveletek | Microsoft.Management/managementGroups/subscriptions/write | Meglévő felügyeleti csoporttal előfizetés társult. |
> | Műveletek | Microsoft.Management/managementGroups/write | Hozzon létre, vagy egy felügyeleti csoport frissítése. |
> | Műveletek | Microsoft.Management/register/action | A megadott előfizetés regisztrálása Microsoft.Management |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Maps/accounts/delete | A Maps-fiók törlése. |
> | Műveletek | Microsoft.Maps/accounts/listKeys/action | A Maps-fiók listázása |
> | Műveletek | Microsoft.Maps/accounts/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Maps/accounts/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.Maps/accounts/providers/Microsoft.Insights/metricDefinitions/read | A Maps fiókok elérhető metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.Maps/accounts/read | Hozzon létre egy Maps-fiókot. |
> | Műveletek | Microsoft.Maps/accounts/regenerateKey/action | Hozzon létre új Maps fiók elsődleges vagy másodlagos kulcsot |
> | Műveletek | Microsoft.Maps/accounts/write | Fiók létrehozása vagy frissítése a Maps. |
> | Műveletek | Microsoft.Maps/register/action | Regisztrálja a szolgáltatót |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | Szerződés adja vissza. |
> | Műveletek | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | Egy aláírt megállapodás fogad el. |
> | Műveletek | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | A végfelhasználó ACR lemezkép importál. |
> | Műveletek | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | A config adja vissza. |
> | Műveletek | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | Menti a config. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.MarketplaceApps/ClassicDevServices/delete | Klasszikus fejlesztői szolgáltatás erőforrás törlési műveletet végez. |
> | Műveletek | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | Lekérdezi a klasszikus fejlesztői erőforrás felügyeleti kulcsai. |
> | Műveletek | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | Az egyszeri bejelentkezési URL-cím lekérése egy klasszikus fejlesztői szolgáltatáshoz. |
> | Műveletek | Microsoft.MarketplaceApps/ClassicDevServices/read | Klasszikus fejlesztői szolgáltatás a GET műveletet végez. |
> | Műveletek | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | A klasszikus fejlesztői erőforrás felügyeleti kulcsai állít elő. |
> | Műveletek | Microsoft.MarketplaceApps/Operations/read | Olvassa el a műveleteket, az összes erőforrástípus. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.MarketplaceOrdering/agreements/offers/plans/cancel/action | Szakítsa meg a szerződés egy adott piactér elem |
> | Műveletek | Microsoft.MarketplaceOrdering/agreements/offers/plans/read | Térjen vissza az adott piactér elem szerződés |
> | Műveletek | Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action | Egy adott piactér elem létrejött |
> | Műveletek | Microsoft.MarketplaceOrdering/agreements/read | Térjen vissza a minden szerződés megadott előfizetés |
> | Műveletek | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | Egy adott piactér virtuális gép elem szerződés beolvasása |
> | Műveletek | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | Bejelentkezés, vagy szakítsa meg a szerződés egy adott piactér virtuális gép elem |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Media/checknameavailability/action | Ellenőrzi, hogy a Media Services-fiók nevének érhető el |
> | Műveletek | Microsoft.Media/locations/checkNameAvailability/action | Ellenőrzi, hogy a Media Services-fiók nevének érhető el |
> | Műveletek | Microsoft.Media/mediaservices/assets/delete | Minden eszköz törlése |
> | Műveletek | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | Eszköz titkosítási kulcs beszerzése |
> | Műveletek | Microsoft.Media/mediaservices/assets/listContainerSas/action | Lista eszköz tároló SAS URL-címek |
> | Műveletek | Microsoft.Media/mediaservices/assets/read | Minden eszköz beolvasása |
> | Műveletek | Microsoft.Media/mediaservices/assets/write | Minden eszköz létrehozása vagy frissítése |
> | Műveletek | Microsoft.Media/mediaservices/contentKeyPolicies/delete | A tartalom-házirend törlése |
> | Műveletek | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | A titkos kulcsok házirend tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Media/mediaservices/contentKeyPolicies/read | A tartalom-házirend olvasása |
> | Műveletek | Microsoft.Media/mediaservices/contentKeyPolicies/write | Hozzon létre vagy bármely tartalom-házirend frissítése |
> | Műveletek | Microsoft.Media/mediaservices/delete | A Media Services-fiók törlése |
> | Műveletek | Microsoft.Media/mediaservices/eventGridFilters/delete | A rács szűrő törlése |
> | Műveletek | Microsoft.Media/mediaservices/eventGridFilters/read | Olvassa el a rács szűrő |
> | Műveletek | Microsoft.Media/mediaservices/eventGridFilters/write | Létrehozni vagy frissíteni a rács szűrő |
> | Műveletek | Microsoft.Media/mediaservices/liveEventOperations/read | Olvassa el az összes élő esemény műveletet |
> | Műveletek | Microsoft.Media/mediaservices/liveEvents/delete | Bármely élő esemény törlése |
> | Műveletek | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | Élő kimenetet törlése |
> | Műveletek | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | Élő kimenetet olvasása |
> | Műveletek | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | Hozzon létre vagy élő kimenetet frissítése |
> | Műveletek | Microsoft.Media/mediaservices/liveEvents/read | Olvassa el az összes élő esemény |
> | Műveletek | Microsoft.Media/mediaservices/liveEvents/reset/action | Bármely élő esemény művelet alaphelyzetbe állítása |
> | Műveletek | Microsoft.Media/mediaservices/liveEvents/start/action | Bármely élő esemény indítása |
> | Műveletek | Microsoft.Media/mediaservices/liveEvents/stop/action | Bármely élő esemény művelet leállítása |
> | Műveletek | Microsoft.Media/mediaservices/liveEvents/write | Létrehozása vagy frissítése bármely élő esemény |
> | Műveletek | Microsoft.Media/mediaservices/liveOutputOperations/read | Olvassa el az összes élő kimeneti műveletet |
> | Műveletek | Microsoft.Media/mediaservices/read | Olvassa el a Media Services-fiók |
> | Műveletek | Microsoft.Media/mediaservices/streamingEndpointOperations/read | Olvassa el a folyamatos átviteli végpont művelet |
> | Műveletek | Microsoft.Media/mediaservices/streamingEndpoints/delete | Bármely adatfolyam-továbbítási végpontjának törlése |
> | Műveletek | Microsoft.Media/mediaservices/streamingEndpoints/read | A Streamvégpont olvasása |
> | Műveletek | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | Minden olyan adatfolyam-továbbítási végpont művelet méretezése |
> | Műveletek | Microsoft.Media/mediaservices/streamingEndpoints/start/action | A folyamatos átviteli végpont indítása |
> | Műveletek | Microsoft.Media/mediaservices/streamingEndpoints/stop/action | A folyamatos átviteli végpont művelet leállítása |
> | Műveletek | Microsoft.Media/mediaservices/streamingEndpoints/write | Hozzon létre vagy bármely adatfolyam-továbbítási végpontjának frissítése |
> | Műveletek | Microsoft.Media/mediaservices/streamingLocators/delete | A Streamelési Locator törlése |
> | Műveletek | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | Tartalom listázása |
> | Műveletek | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | Útvonalak listája |
> | Műveletek | Microsoft.Media/mediaservices/streamingLocators/read | Olvassa el a Streamelési lokátor |
> | Műveletek | Microsoft.Media/mediaservices/streamingLocators/write | Létrehozni vagy frissíteni a Streamelési lokátor |
> | Műveletek | Microsoft.Media/mediaservices/streamingPolicies/delete | Bármely adatfolyam-házirend törlése |
> | Műveletek | Microsoft.Media/mediaservices/streamingPolicies/read | Bármely adatfolyam-házirend olvasása |
> | Műveletek | Microsoft.Media/mediaservices/streamingPolicies/write | Létrehozni vagy frissíteni a folyamatos átviteli házirendet |
> | Műveletek | Microsoft.Media/mediaservices/syncStorageKeys/action | A tárolási kulcsokat csatolt Azure Storage-fiók szinkronizálása |
> | Műveletek | Microsoft.Media/mediaservices/transforms/delete | Bármely átalakító törlése |
> | Műveletek | Microsoft.Media/mediaservices/transforms/jobs/cancelJob/action | Megszakítása |
> | Műveletek | Microsoft.Media/mediaservices/transforms/jobs/delete | Törli a feladatot |
> | Műveletek | Microsoft.Media/mediaservices/transforms/jobs/read | A feladat beolvasása |
> | Műveletek | Microsoft.Media/mediaservices/transforms/jobs/write | Létrehozni vagy frissíteni a feladat |
> | Műveletek | Microsoft.Media/mediaservices/transforms/read | Bármely átalakító olvasása |
> | Műveletek | Microsoft.Media/mediaservices/transforms/write | Létrehozni vagy frissíteni a átalakítás |
> | Műveletek | Microsoft.Media/mediaservices/write | Bármely Media Services-fiók létrehozása vagy frissítése |
> | Műveletek | Microsoft.Media/operations/read | Rendelkezésre álló műveletek beolvasása |
> | Műveletek | Microsoft.Media/register/action | A Media Services erőforrás-szolgáltató előfizetésének regisztrálása, és lehetővé teszi a Media Services-fiókok létrehozását |
> | Műveletek | Microsoft.Media/unregister/action | A Media Services erőforrás-szolgáltató az előfizetés regisztrációjának törlése |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Migrate/Operations/read | Az elérhető műveletek beolvasása |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | Alkalmazás átjáró Ssl előre definiált házirend |
> | Műveletek | Microsoft.Network/applicationGatewayAvailableSslOptions/read | Alkalmazás átjáró elérhető Ssl-beállítások |
> | Műveletek | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | Rendelkezésre álló Waf beállítására Alkalmazásátjáró beolvasása |
> | Műveletek | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Egy alkalmazás Alkalmazásátjáró háttércímkészletének illesztése |
> | Műveletek | Microsoft.Network/applicationGateways/backendhealth/action | Az átjáró háttér állapotának beolvasása |
> | Műveletek | Microsoft.Network/applicationGateways/delete | Alkalmazásátjáró törlése |
> | Műveletek | Microsoft.Network/applicationGateways/effectiveNetworkSecurityGroups/action | Alkalmazásátjáró konfigurált útválasztási táblázatot beolvasása |
> | Műveletek | Microsoft.Network/applicationGateways/effectiveRouteTable/action | Alkalmazásátjáró konfigurált útválasztási táblázatot beolvasása |
> | Műveletek | Microsoft.Network/applicationGateways/providers/Microsoft.Insights/logDefinitions/read | Az Alkalmazásátjáró eseményeinek beolvasása |
> | Műveletek | Microsoft.Network/applicationGateways/providers/Microsoft.Insights/metricDefinitions/read | Az Alkalmazásátjáró elérhető metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.Network/applicationGateways/read | Alkalmazásátjáró beolvasása |
> | Műveletek | Microsoft.Network/applicationGateways/setSecurityCenterConfiguration/action | Konfigurációs készletek alkalmazás átjáró biztonsági központ |
> | Műveletek | Microsoft.Network/applicationGateways/start/action | Alkalmazásátjáró kezdődik |
> | Műveletek | Microsoft.Network/applicationGateways/stop/action | Leállítja az Alkalmazásátjáró |
> | Műveletek | Microsoft.Network/applicationGateways/write | Alkalmazásátjáró létrehozása vagy meglévő Alkalmazásátjáró frissítése |
> | Műveletek | Microsoft.Network/applicationSecurityGroups/delete | Az alkalmazás biztonsági csoport törlése |
> | Műveletek | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | Biztonsági csoportok csatlakoztatja IP-konfigurációt. |
> | Műveletek | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Csatlakozik a szabály az alkalmazás biztonsági csoportokat. |
> | Műveletek | Microsoft.Network/applicationSecurityGroups/read | Lekérdezi egy alkalmazás biztonsági csoport. |
> | Műveletek | Microsoft.Network/applicationSecurityGroups/write | Egy alkalmazás biztonsági csoportot hoz létre, vagy frissíti a meglévő alkalmazás biztonsági csoporthoz. |
> | Műveletek | Microsoft.Network/bgpServiceCommunities/read | Bgp-szolgáltatás Közösségek beolvasása |
> | Műveletek | Microsoft.Network/checkTrafficManagerNameAvailability/action | A Traffic Manager relatív DNS-név elérhetőségének ellenőrzése. |
> | Műveletek | Microsoft.Network/connections/delete | Deletes VirtualNetworkGatewayConnection |
> | Műveletek | Microsoft.Network/connections/read | Gets VirtualNetworkGatewayConnection |
> | Műveletek | Microsoft.Network/connections/sharedkey/action | ConnectionType(hypernet/routebased) SharedKey beolvasása |
> | Műveletek | Microsoft.Network/connections/sharedKey/read | Gets VirtualNetworkGatewayConnection SharedKey |
> | Műveletek | Microsoft.Network/connections/sharedKey/write | Létrehoz vagy frissít egy meglévő ConnectionType(hypernet/routebased) SharedKey |
> | Műveletek | Microsoft.Network/connections/vpndeviceconfigurationscript/action | Lekérdezi a ConnectionType(hypernet/routebased) VPN-eszköz konfigurációja |
> | Műveletek | Microsoft.Network/connections/write | Létrehoz vagy frissít egy meglévő ConnectionType(hypernet/routebased) |
> | Műveletek | Microsoft.Network/ddosProtectionPlans/ddosProtectionPlanProxies/delete | DDoS védelem terv Proxy törlése |
> | Műveletek | Microsoft.Network/ddosProtectionPlans/ddosProtectionPlanProxies/read | DDoS védelem megtervezése Proxy definíciójának beolvasása |
> | Műveletek | Microsoft.Network/ddosProtectionPlans/ddosProtectionPlanProxies/write | Létrehoz egy DDoS védelem megtervezése Proxy vagy frissítések- és meglévő DDoS védelem megtervezése |
> | Műveletek | Microsoft.Network/ddosProtectionPlans/delete | DDoS védelem terv törlése |
> | Műveletek | Microsoft.Network/ddosProtectionPlans/join/action | DDoS védelmi tervet illesztése |
> | Műveletek | Microsoft.Network/ddosProtectionPlans/read | Lekérdezi a DDoS védelem terv |
> | Műveletek | Microsoft.Network/ddosProtectionPlans/write | DDoS védelem terv létrehozása vagy frissítése. a DDoS védelem megtervezése  |
> | Műveletek | Microsoft.Network/dnsoperationresults/read | A DNS-művelet eredményének beolvasása |
> | Műveletek | Microsoft.Network/dnsoperationstatuses/read | A DNS-művelet állapotának beolvasása  |
> | Műveletek | Microsoft.Network/dnszones/A/delete | Eltávolítható az adott nevű és írja be a "A" a DNS-zónából. |
> | Műveletek | Microsoft.Network/dnszones/A/read | A "A" típusú rekordkészlet beolvasása JSON formátumban. A rekordhalmaz rögzíti, valamint a TTL-t, a címkék és a etag listáját tartalmazza. |
> | Műveletek | Microsoft.Network/dnszones/A/write | Hozzon létre vagy frissíthető a DNS-zónában "A" típusú. A megadott rekordok a rendszer lecseréli a rekordhalmaz aktuális rekordját. |
> | Műveletek | Microsoft.Network/dnszones/AAAA/delete | Eltávolítható az adott nevű és írja be a "AAAA" a DNS-zónából. |
> | Műveletek | Microsoft.Network/dnszones/AAAA/read | "AAAA", az a típusú rekordkészlet beolvasása JSON formátumban. A rekordhalmaz rögzíti, valamint a TTL-t, a címkék és a etag listáját tartalmazza. |
> | Műveletek | Microsoft.Network/dnszones/AAAA/write | Hozzon létre vagy frissíthető a DNS-zónában "AAAA" típusú. A megadott rekordok a rendszer lecseréli a rekordhalmaz aktuális rekordját. |
> | Műveletek | Microsoft.Network/dnszones/all/read | Lekérdezi a DNS-rekordhalmazok típusok között |
> | Műveletek | Microsoft.Network/dnszones/CAA/delete | Eltávolítható az adott nevű és írja be a "CAA" a DNS-zónából. |
> | Műveletek | Microsoft.Network/dnszones/CAA/read | "CAA", az a típusú rekordkészlet beolvasása JSON formátumban. A rekordkészlet tartalmazza a TTL-t, a címkék és az etag. |
> | Műveletek | Microsoft.Network/dnszones/CAA/write | Hozzon létre vagy frissíthető a DNS-zónában "CAA" típusú. A megadott rekordok a rendszer lecseréli a rekordhalmaz aktuális rekordját. |
> | Műveletek | Microsoft.Network/dnszones/CNAME/delete | Eltávolítható az adott nevű és írja be a "CNAME" a DNS-zónából. |
> | Műveletek | Microsoft.Network/dnszones/CNAME/read | "CNAME", az a típusú rekordkészlet beolvasása JSON formátumban. A rekordkészlet tartalmazza a TTL-t, a címkék és az etag. |
> | Műveletek | Microsoft.Network/dnszones/CNAME/write | Hozzon létre vagy frissíthető a DNS-zónában "CNAME" típusú. A megadott rekordok a rendszer lecseréli a rekordhalmaz aktuális rekordját. |
> | Műveletek | Microsoft.Network/dnszones/delete | Törli a DNS-zóna JSON formátumban. A zóna tulajdonságai tartalmaznak címkéket, etag, numberOfRecordSets és maxnumberofrecordsets tulajdonságokat. |
> | Műveletek | Microsoft.Network/dnszones/MX/delete | Eltávolítható az adott nevű és írja be az "MX" a DNS-zónából. |
> | Műveletek | Microsoft.Network/dnszones/MX/read | "MX", az a típusú rekordkészlet beolvasása JSON formátumban. A rekordhalmaz rögzíti, valamint a TTL-t, a címkék és a etag listáját tartalmazza. |
> | Műveletek | Microsoft.Network/dnszones/MX/write | Hozzon létre vagy frissíthető a DNS-zónában "MX" típusú. A megadott rekordok a rendszer lecseréli a rekordhalmaz aktuális rekordját. |
> | Műveletek | Microsoft.Network/dnszones/NS/delete | Törli a DNS-rekordhalmaz NS típusú |
> | Műveletek | Microsoft.Network/dnszones/NS/read | Gets DNS record set of type NS |
> | Műveletek | Microsoft.Network/dnszones/NS/write | Létrehozza vagy frissíti a DNS NS típusú rekordhalmaz |
> | Műveletek | Microsoft.Network/dnszones/providers/Microsoft.Insights/diagnosticSettings/read | A DNS-zóna diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Network/dnszones/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti a DNS-zóna diagnosztikai beállítások |
> | Műveletek | Microsoft.Network/dnszones/providers/Microsoft.Insights/metricDefinitions/read | A DNS-zóna metrikai meghatározásainak beolvasása |
> | Műveletek | Microsoft.Network/dnszones/PTR/delete | Eltávolítható az adott nevű és írja be a "PTR" a DNS-zónából. |
> | Műveletek | Microsoft.Network/dnszones/PTR/read | "PTR", az a típusú rekordkészlet beolvasása JSON formátumban. A rekordhalmaz rögzíti, valamint a TTL-t, a címkék és a etag listáját tartalmazza. |
> | Műveletek | Microsoft.Network/dnszones/PTR/write | Hozzon létre vagy frissíthető a DNS-zónában "PTR" típusú. A megadott rekordok a rendszer lecseréli a rekordhalmaz aktuális rekordját. |
> | Műveletek | Microsoft.Network/dnszones/read | A DNS-zóna JSON formátumban beolvasása. A zóna tulajdonságai tartalmaznak címkéket, etag, numberOfRecordSets és maxnumberofrecordsets tulajdonságokat. Vegye figyelembe, hogy ez a parancs nem kéri le a rekordkészleteket, a zóna tartalmaz. |
> | Műveletek | Microsoft.Network/dnszones/recordsets/read | Lekérdezi a DNS-rekordhalmazok típusok között |
> | Műveletek | Microsoft.Network/dnszones/SOA/read | Gets DNS record set of type SOA |
> | Műveletek | Microsoft.Network/dnszones/SOA/write | Létrehozza vagy frissíti a DNS-rekordhalmaz SOA típusú |
> | Műveletek | Microsoft.Network/dnszones/SRV/delete | Eltávolítható az adott nevű és írja be a "SRV" a DNS-zónából. |
> | Műveletek | Microsoft.Network/dnszones/SRV/read | "SRV", az a típusú rekordkészlet beolvasása JSON formátumban. A rekordhalmaz rögzíti, valamint a TTL-t, a címkék és a etag listáját tartalmazza. |
> | Műveletek | Microsoft.Network/dnszones/SRV/write | SRV típusú rekordot csoport létrehozása vagy frissítése |
> | Műveletek | Microsoft.Network/dnszones/TXT/delete | Eltávolítható az adott nevű és írja be a "TXT" a DNS-zónából. |
> | Műveletek | Microsoft.Network/dnszones/TXT/read | "TXT", az a típusú rekordkészlet beolvasása JSON formátumban. A rekordhalmaz rögzíti, valamint a TTL-t, a címkék és a etag listáját tartalmazza. |
> | Műveletek | Microsoft.Network/dnszones/TXT/write | Hozzon létre vagy frissíthető a DNS-zónában "TXT" típusú. A megadott rekordok a rendszer lecseréli a rekordhalmaz aktuális rekordját. |
> | Műveletek | Microsoft.Network/dnszones/write | Hozzon létre, vagy frissítse a DNS-zónák erőforráscsoporton belül.  A címke van megadva a DNS-zóna erőforrás frissítésére szolgáló. Vegye figyelembe, hogy ez a parancs nem használható létrehozására vagy frissítésére a zónán belül rekordhalmazok. |
> | Műveletek | Microsoft.Network/expressRouteCircuits/authorizations/delete | Törli az Express route-körnek engedélyezési |
> | Műveletek | Microsoft.Network/expressRouteCircuits/authorizations/read | Lekérdezi az Express route-körnek engedély |
> | Műveletek | Microsoft.Network/expressRouteCircuits/authorizations/write | Létrehozza vagy frissíti a meglévő Express route-körnek engedély |
> | Műveletek | Microsoft.Network/expressRouteCircuits/delete | Az Express route-körnek törlése |
> | Műveletek | Microsoft.Network/expressRouteCircuits/join/action | Az Express Route-körhöz illesztése |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/arpTables/action | Az Express route-körnek társviszony-létesítés ArpTable beolvasása |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | Az Express route-körnek kapcsolat törlése |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/connections/read | Lekérdezi az Express route-körnek kapcsolat |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/connections/write | Létrehozza vagy frissíti az Express route-körnek meglévő kapcsolat erőforrás |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/delete | Az Express route-körnek Társviszony törlése |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/read | Lekérdezi az Express route-körnek társviszony-létesítés |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/routeTables/action | Az Express route-körnek társviszony-létesítés Migrálták beolvasása |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/routeTablesSummary/action | Az Express route-körnek társviszony-létesítés Migrálták összegzését beolvasása |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/stats/read | Lekérdezi az Express route-körnek társviszony-létesítés statisztika |
> | Műveletek | Microsoft.Network/expressRouteCircuits/peerings/write | Létrehoz vagy frissít egy meglévő Express route-körnek Társviszony |
> | Műveletek | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/read | Az ExpressRoute-Kapcsolatcsoportok diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti az ExpressRoute-Kapcsolatcsoportok tartozó diagnosztikai beállítások |
> | Műveletek | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/logDefinitions/read | Az események lekérése ExpressRoute-Kapcsolatcsoportok |
> | Műveletek | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/metricDefinitions/read | Az ExpressRoute-Kapcsolatcsoportok metrikai meghatározásainak beolvasása |
> | Műveletek | Microsoft.Network/expressRouteCircuits/read | Az Express route-körnek beolvasása |
> | Műveletek | Microsoft.Network/expressRouteCircuits/stats/read | Az Express route-körnek Stat beolvasása |
> | Műveletek | Microsoft.Network/expressRouteCircuits/write | Létrehoz vagy frissít egy meglévő Express route-körnek |
> | Műveletek | Microsoft.Network/expressRouteCrossConnections/delete | Törölje az Express Route telephelyek közötti kapcsolat |
> | Műveletek | Microsoft.Network/expressRouteCrossConnections/join/action | Az Express Route illesztések telephelyek közötti kapcsolat |
> | Műveletek | Microsoft.Network/expressRouteCrossConnections/peerings/arpTables/action | Egy Expressroute közötti kapcsolat társviszony-létesítés Arp-táblázat beolvasása |
> | Műveletek | Microsoft.Network/expressRouteCrossConnections/peerings/delete | Egy Expressroute közötti kapcsolat Társviszony törlése |
> | Műveletek | Microsoft.Network/expressRouteCrossConnections/peerings/read | Egy Expressroute közötti kapcsolat társviszony-létesítés beolvasása |
> | Műveletek | Microsoft.Network/expressRouteCrossConnections/peerings/routeTables/action | Egy Expressroute közötti kapcsolat társviszony-létesítés útvonaltábla beolvasása |
> | Műveletek | Microsoft.Network/expressRouteCrossConnections/peerings/routeTableSummary/action | Egy Expressroute közötti kapcsolat társviszony-létesítés útvonal tábla összegzés beolvasása |
> | Műveletek | Microsoft.Network/expressRouteCrossConnections/peerings/write | Az Express Route telephelyek közötti kapcsolat társviszony-létesítés létrehozása vagy frissítése. egy meglévő Express Route telephelyek közötti kapcsolat társviszony-létesítés |
> | Műveletek | Microsoft.Network/expressRouteCrossConnections/read | Expressroute beolvasása telephelyek közötti kapcsolat |
> | Műveletek | Microsoft.Network/expressRouteCrossConnections/write | Hozzon létre vagy frissíthető Expressroute telephelyek közötti kapcsolat |
> | Műveletek | Microsoft.Network/expressRouteServiceProviders/read | Lekérdezi az Express Route-szolgáltatók |
> | Műveletek | Microsoft.Network/intendedPolicies/delete | Egy adott házirend törlése |
> | Műveletek | Microsoft.Network/intendedPolicies/read | Lekérdezi az alkalmazni kívánt házirend leírása |
> | Műveletek | Microsoft.Network/intendedPolicies/write | Létrehoz egy olyan házirendet, vagy frissíti a meglévő szánt házirend |
> | Műveletek | Microsoft.Network/loadBalancers/backendAddressPools/join/action | A load balancer háttércímkészletének illesztése |
> | Műveletek | Microsoft.Network/loadBalancers/backendAddressPools/read | A load balancer háttércímkészlet definíciójának beolvasása |
> | Műveletek | Microsoft.Network/loadBalancers/delete | Törli a terheléselosztó |
> | Műveletek | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | A terheléselosztó előtérbeli IP konfiguráció definíciójának beolvasása |
> | Műveletek | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Művelettel illeszthető egy terheléselosztó bejövő forgalmat kezelő nat-készlete |
> | Műveletek | Microsoft.Network/loadBalancers/inboundNatPools/read | Terheléselosztó bejövő forgalmat kezelő nat-készlet definíciójának beolvasása |
> | Műveletek | Microsoft.Network/loadBalancers/inboundNatRules/delete | Terheléselosztó bejövő forgalmat kezelő nat-szabályának törlése |
> | Műveletek | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Terheléselosztó bejövő forgalmat kezelő nat-szabályának illesztése |
> | Műveletek | Microsoft.Network/loadBalancers/inboundNatRules/read | Terheléselosztó bejövő forgalmat kezelő nat-szabályát leíró definíció beolvasása |
> | Műveletek | Microsoft.Network/loadBalancers/inboundNatRules/write | Terheléselosztó bejövő forgalmat kezelő nat-szabályának létrehozása vagy frissítése egy meglévő terheléselosztó bejövő nat-szabálya |
> | Műveletek | Microsoft.Network/loadBalancers/loadBalancingRules/read | Terheléselosztó terheléselosztási terheléselosztási szabály definíciójának beolvasása |
> | Műveletek | Microsoft.Network/loadBalancers/networkInterfaces/read | A terheléselosztó minden hálózati interfészen hivatkozások beolvasása |
> | Műveletek | Microsoft.Network/loadBalancers/outboundNatRules/read | Terheléselosztó kimenő forgalmat kezelő nat szabály definíciójának beolvasása |
> | Műveletek | Microsoft.Network/loadBalancers/probes/join/action | Lehetővé teszi, hogy a terheléselosztó mintavételt használatával. Például, az engedély healthProbe tulajdonság a Virtuálisgép-méretezési készlet is hivatkozhatnak a mintavételi. |
> | Műveletek | Microsoft.Network/loadBalancers/probes/read | Terheléselosztói mintavétel beolvasása |
> | Műveletek | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/read | A Load Balancer diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti a terheléselosztási diagnosztikai beállításokat |
> | Műveletek | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/logDefinitions/read | Terheléselosztó eseményeinek beolvasása |
> | Műveletek | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/metricDefinitions/read | A terheléselosztó elérhető metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.Network/loadBalancers/read | Terheléselosztó definíciójának beolvasása |
> | Műveletek | Microsoft.Network/loadBalancers/virtualMachines/read | A terheléselosztó virtuális gépek hivatkozások beolvasása |
> | Műveletek | Microsoft.Network/loadBalancers/write | Terheléselosztó létrehozása vagy meglévő terheléselosztó frissítése |
> | Műveletek | Microsoft.Network/localnetworkgateways/delete | LocalNetworkGateway törlése |
> | Műveletek | Microsoft.Network/localnetworkgateways/read | Lekérdezi a LocalNetworkGateway |
> | Műveletek | Microsoft.Network/localnetworkgateways/write | Létrehoz vagy frissít egy meglévő virtuális |
> | Műveletek | Microsoft.Network/locations/availableDelegations/read | Lekérdezi a rendelkezésre álló delegálásokat |
> | Műveletek | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | Ellenőrzi az elérését gyorsítja fel hálózati támogatás |
> | Műveletek | Microsoft.Network/locations/checkDnsNameAvailability/read | Ellenőrzi, hogy DNS-címke érhető el a megadott helyen |
> | Műveletek | Microsoft.Network/locations/operationResults/read | Aszinkrón POST vagy DELETE művelet eredményének beolvasása |
> | Műveletek | Microsoft.Network/locations/operations/read | Egy aszinkron művelet állapotát jelző művelet erőforrás beolvasása |
> | Műveletek | Microsoft.Network/locations/supportedVirtualMachineSizes/read | Lekérdezi a támogatott virtuális gépek méretét |
> | Műveletek | Microsoft.Network/locations/usages/read | A szoftverhasználati mérési adatok erőforrások lekérése |
> | Műveletek | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | Virtuális hálózati végpont elérhető szolgáltatások listájának lekérése |
> | Műveletek | Microsoft.Network/networkInterfaces/delete | Hálózati kapcsolat törlése |
> | Műveletek | Microsoft.Network/networkInterfaces/diagnosticIdentity/read | Lekérdezi az erőforrás diagnosztikai identitás |
> | Műveletek | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | Hálózati biztonsági csoportok konfigurált a hálózati kapcsolat, a VM-lekérdezés |
> | Műveletek | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | A virtuális gép hálózati illesztőjén konfigurált útválasztási táblázatot beolvasása |
> | Műveletek | Microsoft.Network/networkInterfaces/ipconfigurations/read | A hálózati illesztő ip-konfiguráció definíciójának beolvasása  |
> | Műveletek | Microsoft.Network/networkInterfaces/join/action | A virtuális gép illesztése hálózati illesztőhöz |
> | Műveletek | Microsoft.Network/networkInterfaces/joinViaPrivateIp/action | Egy erőforrás csatlakoztatja egy hálózati adapteren keresztül a szolgáltatás társítása |
> | Műveletek | Microsoft.Network/networkInterfaces/loadBalancers/read | A hálózati illesztő részét képező terheléselosztók beolvasása |
> | Műveletek | Microsoft.Network/networkInterfaces/providers/Microsoft.Insights/metricDefinitions/read | Elérhető lekérdezi a hálózati adapter |
> | Műveletek | Microsoft.Network/networkInterfaces/read | Hálózati illesztő definíciójának beolvasása.  |
> | Műveletek | Microsoft.Network/networkInterfaces/serviceAssociations/delete | A szolgáltatás-hozzárendelés törlése |
> | Műveletek | Microsoft.Network/networkInterfaces/serviceAssociations/read | A szolgáltatás társítás definíciójának beolvasása |
> | Műveletek | Microsoft.Network/networkInterfaces/serviceAssociations/validate/action | Ellenőrzi a szolgáltatás társítása |
> | Műveletek | Microsoft.Network/networkInterfaces/serviceAssociations/write | Társítást hoz létre új szolgáltatás, vagy módosítja a meglévő Service társítás |
> | Műveletek | Microsoft.Network/networkInterfaces/write | Egy adott hálózati csatoló létrehozza vagy frissíti a meglévő hálózati illesztő.  |
> | Műveletek | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | Alapértelmezett biztonsági szabály definíciójának beolvasása |
> | Műveletek | Microsoft.Network/networkSecurityGroups/delete | A hálózati biztonsági csoport törlése |
> | Műveletek | Microsoft.Network/networkSecurityGroups/join/action | Hálózati biztonsági csoport illesztése |
> | Műveletek | Microsoft.Network/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | A hálózati biztonsági csoport diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Network/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | A hálózati biztonsági csoportok diagnosztikai beállításainak létrehozása vagy frissítése. Ezt a műveletet az adatelemzési erőforrás-szolgáltató egészíti ki adatokkal. |
> | Műveletek | Microsoft.Network/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | A hálózati biztonsági csoport lekérése eseményeinek beolvasása |
> | Műveletek | Microsoft.Network/networkSecurityGroups/read | Hálózati biztonsági csoport definíciójának beolvasása |
> | Műveletek | Microsoft.Network/networkSecurityGroups/securityRules/delete | A biztonsági szabály törlése |
> | Műveletek | Microsoft.Network/networkSecurityGroups/securityRules/read | Biztonsági szabály definíciójának beolvasása |
> | Műveletek | Microsoft.Network/networkSecurityGroups/securityRules/write | A szabály létrehozása vagy meglévő biztonsági szabály frissítése |
> | Műveletek | Microsoft.Network/networkSecurityGroups/write | Hálózati biztonsági csoport létrehozása vagy meglévő hálózati biztonsági csoport frissítése |
> | Műveletek | Microsoft.Network/networkWatchers/availableProvidersList/action | Adja vissza az összes rendelkezésre álló internet szolgáltatók a megadott Azure-régió. |
> | Műveletek | Microsoft.Network/networkWatchers/azureReachabilityReport/action | Adja vissza a relatív késés pontszám internetes szolgáltatók megadott helyről az Azure-régiók. |
> | Műveletek | Microsoft.Network/networkWatchers/configureFlowLog/action | Konfigurálja a cél erőforráson folyamat naplózását. |
> | Műveletek | Microsoft.Network/networkWatchers/connectionMonitors/delete | Figyelő kapcsolat törlése |
> | Műveletek | Microsoft.Network/networkWatchers/connectionMonitors/providers/Microsoft.Insights/diagnosticSettings/read | Figyelő kapcsolat diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Network/networkWatchers/connectionMonitors/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti a kapcsolat figyelő diagnosztikai beállításai |
> | Műveletek | Microsoft.Network/networkWatchers/connectionMonitors/providers/Microsoft.Insights/metricDefinitions/read | Figyelő kapcsolat elérhető metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.Network/networkWatchers/connectionMonitors/query/action | A lekérdezés megadott végpontok közötti kapcsolat ellenőrzése |
> | Műveletek | Microsoft.Network/networkWatchers/connectionMonitors/read | Részletek a kapcsolat figyelője |
> | Műveletek | Microsoft.Network/networkWatchers/connectionMonitors/start/action | Indítsa el a megadott végpontok közötti kapcsolat ellenőrzése |
> | Műveletek | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | Állítsa le vagy felfüggesztése megadott végpontok közötti kapcsolat ellenőrzése |
> | Műveletek | Microsoft.Network/networkWatchers/connectionMonitors/write | Egy kapcsolat figyelő létrehozása |
> | Műveletek | Microsoft.Network/networkWatchers/connectivityCheck/action | Ellenőrzi a lehetőségét a közvetlen TCP-kapcsolatot a virtuális gép egy adott végpont, többek között a másik virtuális gép vagy egy tetszőleges távoli kiszolgálón. |
> | Műveletek | Microsoft.Network/networkWatchers/delete | Törli a hálózati figyelőt |
> | Műveletek | Microsoft.Network/networkWatchers/ipFlowVerify/action | Adja vissza, hogy a csomag engedélyezett vagy megtagadott vagy onnan az adott célhelyre. |
> | Műveletek | Microsoft.Network/networkWatchers/lenses/delete | A fókuszok téma törlése |
> | Műveletek | Microsoft.Network/networkWatchers/lenses/query/action | A megadott végpont a hálózati forgalom figyelése lekérdezés |
> | Műveletek | Microsoft.Network/networkWatchers/lenses/read | Részletek a fókuszt |
> | Műveletek | Microsoft.Network/networkWatchers/lenses/start/action | Indítsa el a megadott végpont a hálózati forgalom figyelése |
> | Műveletek | Microsoft.Network/networkWatchers/lenses/stop/action | A megadott végpont a hálózati forgalom figyelése STOP/felfüggesztése |
> | Műveletek | Microsoft.Network/networkWatchers/lenses/write | Létrehoz egy fókuszban |
> | Műveletek | Microsoft.Network/networkWatchers/nextHop/action | A megadott cél és a cél IP-cím térjen vissza a következő ugrás típusa, és ezután az IP-cím legyen. |
> | Műveletek | Microsoft.Network/networkWatchers/packetCaptures/delete | A csomagrögzítéssel törlése |
> | Műveletek | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Lekérdezi a tulajdonságok és a csomag rögzítési erőforrás állapotával kapcsolatos adatokat. |
> | Műveletek | Microsoft.Network/networkWatchers/packetCaptures/read | A csomag rögzítési definíciójának beolvasása |
> | Műveletek | Microsoft.Network/networkWatchers/packetCaptures/stop/action | A futó csomag-rögzítési munkamenet leállítása. |
> | Műveletek | Microsoft.Network/networkWatchers/packetCaptures/write | A csomagrögzítéssel létrehozása |
> | Műveletek | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | Erőforrás-naplózás folyamat állapotának beolvasása. |
> | Műveletek | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | Lekérdezi a korábban futtatott vagy jelenleg hibaelhárítási eredménye hibaelhárítási művelet futtatása. |
> | Műveletek | Microsoft.Network/networkWatchers/read | A hálózati figyelő definíciójának beolvasása |
> | Műveletek | Microsoft.Network/networkWatchers/securityGroupView/action | A konfigurált és hatékony hálózati biztonsági csoport szabálya a virtuális gép megtekintése. |
> | Műveletek | Microsoft.Network/networkWatchers/topology/action | Lekérdezi a hálózati szintű áttekintés a erőforrásokat, és azok erőforráscsoportban. |
> | Műveletek | Microsoft.Network/networkWatchers/troubleshoot/action | Egy hálózati erőforráshoz az Azure-ban végzett hibaelhárítás indítása. |
> | Műveletek | Microsoft.Network/networkWatchers/write | A hálózati figyelőt létrehoz vagy frissít egy meglévő hálózati figyelőt |
> | Műveletek | Microsoft.Network/operations/read | Rendelkezésre álló műveletek beolvasása |
> | Műveletek | Microsoft.Network/publicIPAddresses/delete | Törli a nyilvános IP-cím. |
> | Műveletek | Microsoft.Network/publicIPAddresses/join/action | A nyilvános IP-cím illesztése |
> | Műveletek | Microsoft.Network/publicIPAddresses/loadBalancerPools/delete | A nyilvános IP-címet a terheléselosztó háttérkészletének törlése |
> | Műveletek | Microsoft.Network/publicIPAddresses/loadBalancerPools/join/action | A nyilvános IP-címet a terheléselosztó háttérkészletének illesztése |
> | Műveletek | Microsoft.Network/publicIPAddresses/loadBalancerPools/read | Nyilvános IP-cím terheléselosztó háttér készlet definíciójának beolvasása |
> | Műveletek | Microsoft.Network/publicIPAddresses/loadBalancerPools/write | A nyilvános IP-címet a terheléselosztó háttérkészletének létrehoz vagy frissít egy meglévő nyilvános IP-címet a terheléselosztó háttérkészletének |
> | Műveletek | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/read | A nyilvános IP-cím diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/write | A nyilvános IP-cím diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/logDefinitions/read | A naplófájl-definíciói nyilvános IP-cím beszerzése |
> | Műveletek | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/metricDefinitions/read | A metrikák definíciók nyilvános IP-cím beszerzése |
> | Műveletek | Microsoft.Network/publicIPAddresses/read | A nyilvános IP-cím definíciójának beolvasása |
> | Műveletek | Microsoft.Network/publicIPAddresses/write | A nyilvános IP-cím létrehozza vagy frissíti a meglévő nyilvános IP-címnek.  |
> | Műveletek | Microsoft.Network/register/action | Az előfizetés regisztrálása |
> | Műveletek | Microsoft.Network/routeFilters/delete | Egy útvonal szűrődefinícióban törlése |
> | Műveletek | Microsoft.Network/routeFilters/join/action | Útvonal szűrő illesztése |
> | Műveletek | Microsoft.Network/routeFilters/read | Útvonal szűrő definíciójának beolvasása |
> | Műveletek | Microsoft.Network/routeFilters/routeFilterRules/delete | Törli a útvonal szűrő szabályát leíró definíció beolvasása |
> | Műveletek | Microsoft.Network/routeFilters/routeFilterRules/read | Útvonal szűrési szabály definíciójának beolvasása |
> | Műveletek | Microsoft.Network/routeFilters/routeFilterRules/write | Egy útvonal-szűrő szabályt hoz létre, vagy meglévő útvonal szűrési szabály frissítése |
> | Műveletek | Microsoft.Network/routeFilters/write | Útvonal szűrő létrehoz vagy frissít egy meglévő rotue szűrő |
> | Műveletek | Microsoft.Network/routeTables/delete | Útvonaltábla-definíció törlése |
> | Műveletek | Microsoft.Network/routeTables/join/action | Egy útválasztási táblázatot illesztése |
> | Műveletek | Microsoft.Network/routeTables/read | Útvonaltábla-definíció beolvasása |
> | Műveletek | Microsoft.Network/routeTables/routes/delete | Útvonal-definíció törlése |
> | Műveletek | Microsoft.Network/routeTables/routes/read | Útvonal definíciójának beolvasása |
> | Műveletek | Microsoft.Network/routeTables/routes/write | Új útvonal létrehozása vagy meglévő útvonal frissítése |
> | Műveletek | Microsoft.Network/routeTables/write | Útvonaltábla létrehozása vagy meglévő útvonaltábla frissítése |
> | Műveletek | Microsoft.Network/securegateways/applicationRuleCollections/delete | Az alkalmazás-szabálygyűjtemény egy biztonságos átjáró törlése |
> | Műveletek | Microsoft.Network/securegateways/applicationRuleCollections/read | Az alkalmazás-szabálygyűjtemény egy adott biztonságos átjáró beolvasása |
> | Műveletek | Microsoft.Network/securegateways/applicationRuleCollections/write | Létrehozza vagy frissíti az alkalmazás-szabálygyűjtemény egy biztonságos átjáró |
> | Műveletek | Microsoft.Network/securegateways/delete | Biztonságos átjáró törlése |
> | Műveletek | Microsoft.Network/securegateways/networkRuleCollections/delete | Egy hálózati szabálygyűjteményhez biztonságos átjáró törlése |
> | Műveletek | Microsoft.Network/securegateways/networkRuleCollections/read | Egy adott biztonságos átjáró egy hálózati szabálygyűjteményhez beolvasása |
> | Műveletek | Microsoft.Network/securegateways/networkRuleCollections/write | Létrehozza vagy frissíti egy hálózati szabálygyűjteményhez biztonságos átjáró |
> | Műveletek | Microsoft.Network/securegateways/read | Biztonságos átjáró beolvasása |
> | Műveletek | Microsoft.Network/securegateways/write | Létrehozza vagy frissíti egy biztonságos átjáró |
> | Műveletek | Microsoft.Network/serviceEndpointPolicies/delete | A szolgáltatási végpont házirend törlése |
> | Műveletek | Microsoft.Network/serviceEndpointPolicies/join/action | A szolgáltatási végpont házirend illesztése |
> | Műveletek | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | A szolgáltatási végpont házirendek alhálózat illesztése |
> | Műveletek | Microsoft.Network/serviceEndpointPolicies/read | Lekérdezi a szolgáltatási végpont házirend leírása |
> | Műveletek | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | A szolgáltatási végpont házirend-definíció törlése |
> | Műveletek | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | Lekérdezi a szolgáltatási végpont házirend Definition visszafejtés |
> | Műveletek | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | Szolgáltatási végpont házirend-definíció létrehozása vagy egy meglévő szolgáltatási végpont házirend-definíció frissítése |
> | Műveletek | Microsoft.Network/serviceEndpointPolicies/write | A szolgáltatási végpont házirend létrehozza vagy frissíti a meglévő szolgáltatási végpont házirend |
> | Műveletek | Microsoft.Network/trafficManagerGeographicHierarchies/read | Lekérdezi a régiókban, amelyek együtt a földrajzi forgalom-útválasztási módszert tartalmazó Traffic Manager Geographic hierarchia |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | Azure-végpont törlése egy meglévő Traffic Manager-profil. A TRAFFIC Manager útválasztási forgalmat a törölt Azure-beli végpont le fog állni. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | Lekérdezi egy Azure-végpontot, amely a Traffic Manager-profil, beleértve az adott Azure-beli végpont tulajdonságait. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | Új Azure-végpont hozzáadása a meglévő Traffic Manager-profilt, vagy egy meglévő Azure-beli végpont, hogy a Traffic Manager-profil tulajdonságainak frissítéséhez. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/delete | A Traffic Manager-profil törlése. A Traffic Manager-profil társított összes beállítást elvész, és a profil már nem használható a forgalom irányításához. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | Külső végpont törlése egy meglévő Traffic Manager-profil. A TRAFFIC Manager útválasztási forgalmat a törölt külső végpont le fog állni. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | Lekérdezi a külső végpont, amely a Traffic Manager-profil, beleértve a külső végpont tulajdonságait. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | Új külső végpont hozzáadása a meglévő Traffic Manager-profilt, vagy egy meglévő külső végpont, hogy a Traffic Manager-profil tulajdonságainak frissítéséhez. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/heatMaps/read | A Traffic Manager Hőtérkép lekérdezi a megadott Traffic Manager-profil helyét és a forrás IP-címekként számát és a késés adatait tartalmazó. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | A beágyazott végpont törlése egy meglévő Traffic Manager-profil. A TRAFFIC Manager útválasztási forgalmat a törölt beágyazott végpont le fog állni. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | Lekérdezi egy beágyazott végpontot, amely a Traffic Manager-profil, beleértve, hogy a beágyazott végpont tulajdonságait. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | Adja hozzá új beágyazott végpont egy már meglévő Traffic Manager-profilt, vagy egy meglévő beágyazott végpont a Traffic Manager-profil tulajdonságainak frissítéséhez. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/read | A Traffic Manager diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti a Traffic Manager diagnosztikai beállítások, a művelet nem hozhatók insights erőforrás-szolgáltató. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/logDefinitions/read | A Traffic Manager eseményeinek beolvasása |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/metricDefinitions/read | Elérhető metrikai meghatározások beolvasása a Traffic Manager. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/read | A Traffic Manager-profil konfigurációjának beolvasása. Ez magában foglalja a DNS-beállítások, forgalom útválasztásához tartozó beállításokat, végpontmonitoring beállításai, és továbbítja a Traffic Manager-profil végpontok listáját. |
> | Műveletek | Microsoft.Network/trafficManagerProfiles/write | Traffic Manager-profil létrehozása, vagy egy meglévő Traffic Manager-profil konfigurációjának módosítása.<br>Ez magában foglalja a engedélyezésére, vagy egy profil letiltása folyamatban van, és módosítja a DNS-beállítások, a forgalom útválasztásához tartozó beállításokat vagy a végpontmonitoring beállításai.<br>A Traffic Manager-profil által kezelendő végpontok hozzáadott, eltávolított, engedélyezhető vagy le van tiltva. |
> | Műveletek | Microsoft.Network/trafficManagerUserMetricsKeys/delete | A valós idejű felhasználói metrikák gyűjtemény használt előfizetés szintű kulcs törlése. |
> | Műveletek | Microsoft.Network/trafficManagerUserMetricsKeys/read | A valós idejű felhasználói metrikák gyűjtemény használt előfizetés szintű kulcs lekérése. |
> | Műveletek | Microsoft.Network/trafficManagerUserMetricsKeys/write | Létrehoz egy új előfizetés-szintű kulcs valós idejű felhasználói metrikák gyűjtemény használható. |
> | Műveletek | Microsoft.Network/unregister/action | Az előfizetés regisztrációjának törlése |
> | Műveletek | Microsoft.Network/virtualHubs/delete | Egy virtuális központi törlése |
> | Műveletek | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | Törli a HubVirtualNetworkConnection |
> | Műveletek | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | Egy HubVirtualNetworkConnection beolvasása |
> | Műveletek | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | Hozható létre vagy frissíthető egy HubVirtualNetworkConnection |
> | Műveletek | Microsoft.Network/virtualHubs/read | Egy virtuális központi beolvasása |
> | Műveletek | Microsoft.Network/virtualHubs/write | Létrehozni vagy frissíteni a virtuális Hub |
> | Műveletek | Microsoft.Network/virtualnetworkgateways/Connections/Read | Get VirtualNetworkGatewayConnection |
> | Műveletek | Microsoft.Network/virtualNetworkGateways/delete | Törli az átjárónak |
> | Műveletek | Microsoft.Network/virtualnetworkgateways/generatevpnclientpackage/Action | Az átjárónak VpnClient csomagot |
> | Műveletek | Microsoft.Network/virtualnetworkgateways/generatevpnprofile/Action | Pedig VpnProfile csomag létrehozása |
> | Műveletek | Microsoft.Network/virtualnetworkgateways/getadvertisedroutes/Action | Lekérdezi pedig hirdetett útvonalakat |
> | Műveletek | Microsoft.Network/virtualnetworkgateways/getbgppeerstatus/Action | Lekérdezi a pedig a bgp társ állapota |
> | Műveletek | Microsoft.Network/virtualnetworkgateways/getlearnedroutes/Action | Megtudta, pedig útvonalak beolvasása |
> | Műveletek | Microsoft.Network/virtualnetworkgateways/getvpnclientipsecparameters/Action | Vpnclient Ipsec paramétereinek lekérése pedig P2S-ügyfél. |
> | Műveletek | Microsoft.Network/virtualnetworkgateways/getvpnprofilepackageurl/Action | Lekérdezi egy előre generált VPN-ügyfélcsomag profil URL-címe |
> | Műveletek | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/read | A virtuális hálózati átjáró diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/write | Létrehozza vagy frissíti a virtuális hálózati átjáró diagnosztikai beállítások, a művelet nem hozhatók insights erőforrás-szolgáltató. |
> | Műveletek | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/logDefinitions/read | Virtuális hálózati átjáró eseményeinek beolvasása |
> | Műveletek | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/metricDefinitions/read | A virtuális hálózati átjáró elérhető metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.Network/virtualNetworkGateways/read | Lekérdezi a pedig |
> | Műveletek | Microsoft.Network/virtualnetworkgateways/reset/Action | Alaphelyzetbe állítja a pedig |
> | Műveletek | Microsoft.Network/virtualnetworkgateways/setvpnclientipsecparameters/Action | Vpnclient Ipsec paramétert meg az átjárónak P2S-ügyfél. |
> | Műveletek | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | Listák támogatott VPN-eszközök |
> | Műveletek | Microsoft.Network/virtualNetworkGateways/write | Létrehozza vagy frissíti az átjárónak |
> | Műveletek | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | Ellenőrizze, hogy a megadott virtuális hálózati érhető el-e IP-cím |
> | Műveletek | Microsoft.Network/virtualNetworks/customViews/get/action | A virtuális hálózati egyéni nézet tartalmának lekérdezése |
> | Műveletek | Microsoft.Network/virtualNetworks/customViews/read | Virtuális hálózat egyéni nézet definíciójának lekérdezése |
> | Műveletek | Microsoft.Network/virtualNetworks/delete | Virtuális hálózat törlése |
> | Műveletek | Microsoft.Network/virtualNetworks/peer/action | A virtuális hálózat egy másik virtuális hálózathoz állomásokhoz |
> | Műveletek | Microsoft.Network/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/read | A virtuális hálózat diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Network/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/write | A virtuális hálózat diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.Network/virtualNetworks/providers/Microsoft.Insights/logDefinitions/read | A naplófájl-definíciói virtuális hálózat beolvasása |
> | Műveletek | Microsoft.Network/virtualNetworks/read | A virtuális hálózat definíciójának beolvasása |
> | Műveletek | Microsoft.Network/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | Törli a virtuális hálózati társviszony-létesítési proxy |
> | Műveletek | Microsoft.Network/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | A virtuális hálózati társviszony-létesítés proxydefiníció beolvasása |
> | Műveletek | Microsoft.Network/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | A virtuális hálózati társviszony-létesítési proxy létrehoz vagy frissít egy meglévő virtuális hálózati társviszony-létesítési proxy |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/delete | Virtuális hálózati alhálózat törlése |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/join/action | Egy virtuális hálózathoz csatlakozik |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Alhálózathoz csatlakozik az erőforrás, például a tárfiók, vagy az SQL-adatbázis. |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/read | Virtuális hálózati alhálózat definíciójának beolvasása |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/resourceNavigationLinks/delete | Egy erőforrás-navigációs hivatkozás törlése |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/resourceNavigationLinks/read | Az erőforrás-navigációs hivatkozás definíciójának beolvasása |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/resourceNavigationLinks/write | Egy erőforrás-navigációs hivatkozás létrehoz vagy frissít egy meglévő erőforrás-navigációs hivatkozás |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/delete | A szolgáltatás társítás hivatkozás törlése |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/details/read | A szolgáltatás társítás hivatkozás részletes definíciójának beolvasása |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/read | A szolgáltatás társítás hivatkozás definíciójának beolvasása |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/validate/action | Ellenőrzi a szolgáltatás társítás hivatkozás |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/write | Létrehoz egy szolgáltatás társítás hivatkozást vagy frissíti a meglévő Service társítás hivatkozás |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | Virtuális hálózati alhálózat virtuális gépeire mutató hivatkozások beolvasása |
> | Műveletek | Microsoft.Network/virtualNetworks/subnets/write | Virtuális hálózati alhálózat létrehozása vagy egy meglévő virtuális hálózati alhálózat frissítése |
> | Műveletek | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/delete | Címkézett forgalom ügyféllel törlése |
> | Műveletek | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/read | A forgalom fogyasztói címkézett definíciójának beolvasása |
> | Műveletek | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/validate/action | Ellenőrzi az címkézett forgalom ügyféllel |
> | Műveletek | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/write | Címkézett forgalom ügyféllel létrehoz vagy frissít egy meglévő címkézett forgalom fogyasztói |
> | Műveletek | Microsoft.Network/virtualNetworks/usages/read | Az IP-módjait minden, a virtuális hálózati alhálózat beolvasása |
> | Műveletek | Microsoft.Network/virtualNetworks/virtualMachines/read | Virtuális hálózat virtuális gépeire mutató hivatkozások beolvasása |
> | Műveletek | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Törli a virtuális hálózati társviszony-létesítés |
> | Műveletek | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | Virtuális hálózati társviszony-létesítési definíciójának beolvasása |
> | Műveletek | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Virtuális hálózati társviszony-létesítés létrehozása vagy frissítése. egy meglévő virtuális hálózati társviszony |
> | Műveletek | Microsoft.Network/virtualNetworks/write | Létrehoz egy virtuális hálózatot, vagy meglévő virtuális hálózat frissítése |
> | Műveletek | Microsoft.Network/virtualNetworkTaps/delete | Koppintson a virtuális hálózat törlése |
> | Műveletek | Microsoft.Network/virtualNetworkTaps/join/action | A virtuális hálózati tap illesztése |
> | Műveletek | Microsoft.Network/virtualNetworkTaps/read | Virtuális hálózati Tap beolvasása |
> | Műveletek | Microsoft.Network/virtualNetworkTaps/write | Létrehozni vagy frissíteni a virtuális hálózati Tap |
> | Műveletek | Microsoft.Network/virtualWans/delete | Törli a virtuális Wan |
> | Műveletek | Microsoft.Network/virtualWans/read | Get virtuális a Wan |
> | Műveletek | Microsoft.Network/virtualWans/virtualHubProxies/delete | Egy virtuális központi proxy törlése |
> | Műveletek | Microsoft.Network/virtualWans/virtualHubProxies/read | Egy virtuális központi proxy definíciójának beolvasása |
> | Műveletek | Microsoft.Network/virtualWans/virtualHubProxies/write | Egy virtuális központi proxy létrehoz vagy frissít egy virtuális központi proxy |
> | Műveletek | Microsoft.Network/virtualWans/virtualHubs/read | Minden virtuális hubok hivatkozó virtuális Wan lekérdezi. |
> | Műveletek | Microsoft.Network/virtualwans/vpnconfiguration/action | Lekérdezi a VPN-konfiguráció |
> | Műveletek | Microsoft.Network/virtualWans/vpnSiteProxies/delete | Törli a Vpn-hely proxy |
> | Műveletek | Microsoft.Network/virtualWans/vpnSiteProxies/read | Vpn-hely proxy definíciójának beolvasása |
> | Műveletek | Microsoft.Network/virtualWans/vpnSiteProxies/write | A Vpn-hely proxy létrehozása vagy frissítése egy Vpn-hely proxy |
> | Műveletek | Microsoft.Network/virtualWans/vpnSites/read | Lekérdezi egy virtuális Wan hivatkozó összes VPN-hely. |
> | Műveletek | Microsoft.Network/virtualWans/write | Létrehozni vagy frissíteni a virtuális Wan |
> | Műveletek | Microsoft.Network/vpnGateways/read | Lekérdezi a A(z). |
> | Műveletek | microsoft.network/vpnGateways/vpnConnections/read | Lekérdezi a VPN-kapcsolatot. |
> | Műveletek | microsoft.network/vpnGateways/vpnConnections/write | A VPN-kapcsolat helyezi. |
> | Műveletek | Microsoft.Network/vpnGateways/write | A A(z) helyezi. |
> | Műveletek | Microsoft.Network/vpnsites/delete | Törli a Vpn-hely erőforrást. |
> | Műveletek | Microsoft.Network/vpnsites/read | A Vpn-hely erőforrás lekérése. |
> | Műveletek | Microsoft.Network/vpnsites/write | Létrehozza vagy frissíti a Vpn-hely erőforrás. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | Adott névtér-erőforrásnév elérhetőségének ellenőrzése a Notification Hub szolgáltatásban. |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | A névtér-engedélyezési szabályok leírásai listájának beolvasása. |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | Namespace engedélyezési szabály törlése. Az alapértelmezett Namespace engedélyezési szabály nem törölhető.  |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | A névtérhez tartozó kapcsolati karakterlánc beolvasása |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | A névtér-engedélyezési szabályok leírásai listájának beolvasása. |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | Egy névtér-engedélyezési szabály elsődleges vagy másodlagos kulcsának újragenerálása; meg kell adni az újragenerálni kívánt kulcsot |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | A Namespace szintű engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és másodlagos kulcsok lehet frissíteni. |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | Adott értesítésiközpont-név elérhetőségének ellenőrzése egy névtérben. |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/Delete | Egy névtérerőforrás törlése |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | Az értesítésiközpont-engedélyezési szabályok listájának beolvasása |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Értesítésiközpont-engedélyezési szabályok törlése |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | Az értesítési központhoz tartozó kapcsolati karakterlánc beolvasása |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | Az értesítésiközpont-engedélyezési szabályok listájának beolvasása |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Egy értesítésiközpont-engedélyezési szabály elsődleges vagy másodlagos kulcsának újragenerálása; meg kell adni az újragenerálni kívánt kulcsot |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | Értesítési központ engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és másodlagos kulcsok lehet frissíteni. |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | Leküldéses tesztértesítés küldése. |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | Értesítésiközpont-erőforrás törlése |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | Namespace metrikák erőforrás leírása listájának beolvasása |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Minden értesítési központ PNS hitelesítő adatainak lekérése. Ez magában foglalja a wns-ből, a MPNS, a APNS, a GCM és a Baidu hitelesítő adatokat |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | Az értesítésiközpont-erőforrások leírásai listájának beolvasása |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Hozzon létre egy értesítési központot, és a tulajdonságok frissítése. A Tulajdonságok főként PNS hitelesítő adatok közé tartozik. Az engedélyezési szabályok és a TTL-t |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/read | A névtérerőforrások leírásai listájának beolvasása |
> | Műveletek | Microsoft.NotificationHubs/Namespaces/write | Namespace erőforrás létrehozása és frissítése a tulajdonságait. Címkék és a kapacitás, a Namespace nem frissíthető tulajdonságait. |
> | Műveletek | Microsoft.NotificationHubs/register/action | A Notification Hubs-erőforrásszolgáltatóra való előfizetés regisztrálása, valamint a névterek és a Notification Hubs-példányok létrehozásának lehetővé tétele |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.OperationalInsights/linkTargets/read | Felsorolja a meglévő fiókokat, amelyek nem kapcsolódnak az Azure-előfizetéssel. Az Azure-előfizetés összekapcsolása egy munkaterület, egy felhasználói azonosítót, ez a művelet az ügyfél-azonosító tulajdonsággal, a munkaterület létrehozása művelet által visszaadott használja. |
> | Műveletek | Microsoft.OperationalInsights/register/action | Egy erőforrás-szolgáltató előfizetés regisztrálásához. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/analytics/query/action | A keresés új motor használatával. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | Keresési séma V2 beolvasása. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/api/query/action | A keresés új motor használatával. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/api/query/schema/read | Keresési séma V2 beolvasása. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | A konfigurációs hatókörben törlése |
> | Műveletek | Microsoft.OperationalInsights/workspaces/configurationScopes/read | A konfigurációs hatókörben beolvasása |
> | Műveletek | Microsoft.OperationalInsights/workspaces/configurationScopes/write | Konfigurációs hatókör megadása |
> | Műveletek | Microsoft.OperationalInsights/workspaces/datasources/delete | Törölje az adott munkaterületen adatforrásokhoz. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/datasources/read | Adatforrások beolvasása az adott munkaterületen. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/datasources/write | Az adott munkaterületen Adatforrás létrehozása/frissítése. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/delete | A munkaterület törlése. Ha a munkaterületet lett csatolva egy meglévő munkaterület a létrehozás időpontjában volt csatolva a munkaterület nem törlődik. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/generateregistrationcertificate/action | A munkaterület regisztrációs tanúsítványt hoz létre. Ezzel a tanúsítvánnyal a munkaterületen a Microsoft System Center Operation Manager csatlakozni. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | Egy intelligence Pack csomagot egy adott munkaterület letiltja. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | Lehetővé teszi, hogy egy adott munkaterület egy intelligence Pack csomagot. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | Megjeleníti egy adott worksapce esetében az eszközintelligencia-csomagokat, és is megjeleníti, hogy a csomag engedélyezett vagy letiltott adott munkaterület. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/linkedServices/delete | A szolgáltatás kapcsolódó törlése megadott munkaterületen. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/linkedServices/read | A társított szolgáltatások munkaterület megadott. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/linkedServices/write | Létrehozása/frissítése kapcsolódó szolgáltatások az adott munkaterületen. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/listKeys/action | Lekéri a listában kulcsok a munkaterületen. Ezek a kulcsok segítségével a Microsoft az Operational Insights-ügynököket a munkaterülethez csatlakoztatandó. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/listKeys/read | Lekéri a listában kulcsok a munkaterületen. Ezek a kulcsok segítségével a Microsoft az Operational Insights-ügynököket a munkaterülethez csatlakoztatandó. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/managementGroups/read | System Center Operations Manager felügyeleti csoport csatlakoztatva a munkaterület neve és metaadatok beolvasása. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | A metrika definíciók lekérése a munkaterületen |
> | Műveletek | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | A felhasználói értesítés beállításait a munkaterület törlése. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/notificationSettings/read | A felhasználói értesítés beállításait a munkaterület beolvasása. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/notificationSettings/write | Állítsa be a felhasználói értesítés beállításait a munkaterületen. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/purge/action | Megadott adatok törléséhez a munkaterület |
> | Műveletek | Microsoft.OperationalInsights/workspaces/read | Lekérdezi a meglévő-munkaterülettel |
> | Műveletek | Microsoft.OperationalInsights/workspaces/savedSearches/delete | Törli a mentett keresési lekérdezés |
> | Műveletek | Microsoft.OperationalInsights/workspaces/savedSearches/read | Lekérdezi a mentett keresési lekérdezés |
> | Műveletek | Microsoft.OperationalInsights/workspaces/savedSearches/write | A mentett keresési lekérdezést hoz létre |
> | Műveletek | Microsoft.OperationalInsights/workspaces/schema/read | A keresési séma lekérése a munkaterületen.  Keresési séma kitett mezőket és a típusukat tartalmazza. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/search/action | Keresési lekérdezés végrehajtása |
> | Műveletek | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Lekéri a megosztott kulcsok a munkaterületen. Ezek a kulcsok segítségével a Microsoft az Operational Insights-ügynököket a munkaterülethez csatlakoztatandó. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Lekéri a megosztott kulcsok a munkaterületen. Ezek a kulcsok segítségével a Microsoft az Operational Insights-ügynököket a munkaterülethez csatlakoztatandó. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | A tárolási konfiguráció törlése. Ez a jövőben nem Microsoft Operational Insights adatok beolvasása a tárfiókból. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/read | Lekérdezi a tárolási konfiguráció. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/write | Létrehoz egy új tárolási konfigurációt. Ezek a beállítások segítségével olvasnak be adatokat egy hely a meglévő tárfiókot. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/usages/read | Lekérdezi a használati adatok egy munkaterület, beleértve a munkaterület által beolvasott adatok mennyiségét. |
> | Műveletek | Microsoft.OperationalInsights/workspaces/write | Új munkaterület vagy egy meglévő munkaterület mutató hivatkozások azáltal, hogy az ügyfél-azonosítója a meglévő munkaterületről hozható létre. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.OperationsManagement/managementAssociations/delete | Törölje a meglévő felügyeleti társítása |
> | Műveletek | Microsoft.OperationsManagement/managementAssociations/read | Meglévő felügyeleti társítása beolvasása |
> | Műveletek | Microsoft.OperationsManagement/managementAssociations/write | Hozzon létre egy új felügyeleti társítása |
> | Műveletek | Microsoft.OperationsManagement/managementConfigurations/delete | Törölje a meglévő felügyeleti Configuratin |
> | Műveletek | Microsoft.OperationsManagement/managementConfigurations/read | Meglévő felügyeleti konfiguráció beolvasása |
> | Műveletek | Microsoft.OperationsManagement/managementConfigurations/write | Hozzon létre egy új felügyeleti konfiguráció |
> | Műveletek | Microsoft.OperationsManagement/register/action | Egy erőforrás-szolgáltató előfizetés regisztrálásához. |
> | Műveletek | Microsoft.OperationsManagement/solutions/delete | Törölje a meglévő OMS-megoldás |
> | Műveletek | Microsoft.OperationsManagement/solutions/read | Kilépés az OMS-megoldás beszerzése |
> | Műveletek | Microsoft.OperationsManagement/solutions/write | Új OMS-megoldás létrehozása |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.PolicyInsights/policyEvents/queryResults/action | Házirend-eseményekkel kapcsolatos információk lekérdezése. |
> | Műveletek | Microsoft.PolicyInsights/policyStates/queryResults/action | Házirend állapotok kapcsolatos információk lekérdezése. |
> | Műveletek | Microsoft.PolicyInsights/policyStates/summarize/action | Lekérdezés házirend legújabb állapotok kapcsolatos összegző információkat. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Portal/dashboards/delete | Az irányítópult eltávolítása az előfizetésből. |
> | Műveletek | Microsoft.Portal/dashboards/read | Az előfizetéshez tartozó irányítópultok beolvasása. |
> | Műveletek | Microsoft.Portal/dashboards/write | Irányítópult felvétele vagy átalakítása előfizetéssé. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.PowerBIDedicated/capacities/checkNameAvailability/action | Ellenőrzi, hogy a Power BI dedikált kapacitás a megadott név érvényes, és nincs használatban. |
> | Műveletek | Microsoft.PowerBIDedicated/capacities/delete | Törli a Power BI dedikált kapacitás. |
> | Műveletek | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/logDefinitions/read | A Power BI dedikált kapacitás naplók beolvasása. |
> | Műveletek | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/metricDefinitions/read | A Power BI dedikált kapacitás elérhető metrikai meghatározások beolvasása. |
> | Műveletek | Microsoft.PowerBIDedicated/capacities/read | A megadott Power BI dedikált kapacitás adatainak beolvasása. |
> | Műveletek | Microsoft.PowerBIDedicated/capacities/resume/action | Folytatja a kapacitást. |
> | Műveletek | Microsoft.PowerBIDedicated/capacities/skus/read | A kapacitás elérhető SKU információinak lekérése |
> | Műveletek | Microsoft.PowerBIDedicated/capacities/suspend/action | Felfüggeszti a kapacitást. |
> | Műveletek | Microsoft.PowerBIDedicated/capacities/write | Létrehozza vagy frissíti a megadott Power BI dedikált kapacitásának kihasználásához. |
> | Műveletek | Microsoft.PowerBIDedicated/locations/operationresults/read | A megadott művelet eredményének adatainak beolvasása. |
> | Műveletek | Microsoft.PowerBIDedicated/locations/operationstatuses/read | A megadott műveleti állapotának adatainak beolvasása. |
> | Műveletek | Microsoft.PowerBIDedicated/operations/read | A műveletek adatainak beolvasása |
> | Műveletek | Microsoft.PowerBIDedicated/register/action | A Power BI dedikált erőforrás-szolgáltató regisztrálása. |
> | Műveletek | Microsoft.PowerBIDedicated/skus/read | Az SKU adatainak beolvasása |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.RecoveryServices/locations/allocatedStamp/read | A Lefoglalt bélyegző beolvasása egy belső művelet, melyet a szolgáltatás használ |
> | Műveletek | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp egy szolgáltatás által használt belső művelet |
> | Műveletek | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Műveletek | Microsoft.RecoveryServices/locations/backupStatus/action | Helyreállítási szolgáltatások tárolók a biztonsági mentés állapotának ellenőrzése |
> | Műveletek | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Szolgáltatások ellenőrzése |
> | Műveletek | Microsoft.RecoveryServices/operations/read | Műveletet az erőforrás-szolgáltató műveleteinek listáját adja vissza. |
> | Műveletek | Microsoft.RecoveryServices/register/action | Regiszterekben előfizetést az adott erőforrás-szolgáltató |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupconfig/read | A helyreállításhoz adja vissza konfigurációs szolgáltatások tárolóban. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupconfig/write | Frissítések konfigurációja helyreállítási szolgáltatások tárolóban. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupEngines/read | A tárolóban regisztrált biztonsági másolatokat kezelő kiszolgálókat adja vissza. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | A biztonságimásolat-készítő védelmi leképezés létrehozásához |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | A művelet állapotának beolvasása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Az összes védhető tároló beolvasása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/delete | A regisztrált tárolójának törlése |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Hajtsa végre a lekérdezést a tárolóban futó alkalmazások és szolgáltatások |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | A tárolóban lévő összes beolvasásának |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Beolvassa a védelmi tárolón végrehajtott művelet eredményét. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Biztonsági másolat készítése egy védett elemről. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | Törli a védett elem |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Beolvassa a védett elemeken végrehajtott művelet eredményét. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | A védett elemeken végrehajtott művelet állapotát adja vissza. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | A védett elem kapcsolatobjektum-adatait adja vissza |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Védett elem rendelkezés azonnali elem helyreállítása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Védett elemek helyreállítási pontjainak beolvasása. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Védett elemek helyreállítási pontjainak visszaállítása. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Védett elem azonnali elem helyreállítása visszavonása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Biztonsági mentési védett típusú elem létrehozása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Minden regisztrált tárolókat ad vissza |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Tárolót hoz létre regisztrált |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Frissíti a tároló listáját |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | A feladat megszakítása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | A feladatművelet eredményét adja vissza. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupJobs/read | Minden feladat objektumot ad vissza |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportálási feladat |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Az exportálási feladat művelet eredményét adja vissza. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | A Recovery Services-tároló biztonságimásolat-kezelési metaadatait adja vissza. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | A Recovery Services-tárolóval kapcsolatos biztonsági mentés eredményét adja vissza. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupOperations/read | Visszaadja a biztonsági mentési művelet állapotának helyreállítási szolgáltatások tárolóban. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupPolicies/delete | Védelmi házirend törlése |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | A szabályzatművelet eredményeinek beolvasása. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Házirend művelet állapotának beolvasása. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Visszaadja az összes védelmi házirend |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Védelmi házirend létrehozása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | A védhető elemek listáját adja vissza. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | A védett elemek listájának beolvasása. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Az előfizetéshez tartozó összes tárolókat ad vissza |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/action | Visszaadja a PIN-kód biztonsági tároló szolgáltatásról a helyreállításhoz. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Helyreállítási beolvasása-tárolási konfigurációját szolgáltatások tárolóban. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupstorageconfig/write | Frissítések Tárkonfigurációt helyreállítási szolgáltatások tárolóban. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Recovery Services beolvasása védett elemek és a védett kiszolgálók összesítések. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/certificates/write | A frissítés erőforrás tanúsítvány művelet frissíti az erőforrás/tárolói hitelesítő adatainak tanúsítványa. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/delete | A tároló törlése a művelet törli a megadott Azure-erőforrás "tárolóban" típusú |
> | Műveletek | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Műveletek | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Műveletek | Microsoft.RecoveryServices/Vaults/extendedInformation/write | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Műveletek | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | A riasztások lekéri a Recovery services-tároló. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Feloldja a riasztást. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | A helyreállítási szolgáltatás tároló értesítési beállításainak lekérése. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | Konfigurálja az e-mail értesítések küldéséhez Recovery services-tároló. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/diagnosticSettings/read | Az Azure biztonsági mentési diagnosztika |
> | Műveletek | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/diagnosticSettings/write | Az Azure biztonsági mentési diagnosztika |
> | Műveletek | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/logDefinitions/read | Az Azure biztonsági mentési naplók |
> | Műveletek | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/metricDefinitions/read | Az Azure biztonsági mentési metrikák |
> | Műveletek | Microsoft.RecoveryServices/Vaults/read | A tároló Get művelet lekérdezi az Azure-erőforrás "tárolóban" típusú képviselő objektum |
> | Műveletek | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | A tároló regisztrációját művelet használható a tároló regisztrációját. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Művelet alkalmazható Get művelet eredményeit a műveleti állapotának és az aszinkron módon elküldött művelet eredménye beolvasása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Az beszerzése tárolókat művelet alkalmazható a tárolók regisztrált erőforrás lekérése. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | A szolgáltatás tárolójának regisztrálása műveletet szolgáltatásban való regisztráláshoz tárolója helyreállítási használható. |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Olvassa el a riasztások beállításai |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | Minden riasztás beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationEvents/read | Bármely események olvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Konzisztencia-ellenőrzést futtat a hálón |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | A hálók törlése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | Lemezkép központi telepítése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/read | A hálók olvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Átjáró újbóli társítása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | Távolítsa el a háló |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Háló tanúsítványának megújítása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Olvassa el a hálózatok |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | Hálózati leképezések törlése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Olvassa el a hálózatok leképezését |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | Hozzon létre vagy hálózati leképezések frissítése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | Védhető objektum felderítése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Olvassa el a védelmi tárolókkal |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | Távolítsa el a védelmi tároló |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Bármely védhető elemek olvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Helyreállítási pont alkalmazása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | Minden védett elemek törlése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Feladatátvétel véglegesítésének |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Tervezett feladatátvétel |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Minden védett elemek olvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Olvassa el a replikációs helyreállítási pontot |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | Távolítsa el a védett elem |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Javítás replikáció |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Állítsa a védett elem |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Feladatátvételi teszt |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Teszt feladatátadás kitakarítását |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Feladatátvétel |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Frissítse a mobilitási szolgáltatás |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | Minden védett cikkek létrehozása vagy frissítése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | A védelmi tároló leképezéseket törlése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Olvassa el a védelmi tároló leképezések |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | Védelmitároló-leképezés eltávolítása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | Létrehozni vagy frissíteni a védelmi tároló leképezések |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Kapcsoló védelmi tároló |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | Létrehozni vagy frissíteni a védelmi tárolókkal |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | Bármilyen helyreállítási szolgáltatók törlése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Olvassa el az összes helyreállítási szolgáltatók |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Frissítse a szolgáltatót |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | Recovery Services-szolgáltató eltávolítása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | Létrehozása vagy frissítése a helyreállítási szolgáltatók |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | A Tárhelybesorolások olvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | A tárolási besorolás leképezéseket törlése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Olvassa el a tárolási besorolás leképezések |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | Létrehozni vagy frissíteni a tárolási besorolás leképezések |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | Feladatok törlése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Olvassa el a megfelelő feladat |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | A feladatok létrehozása vagy módosítása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationFabrics/write | Létrehozni vagy frissíteni a háló |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | Megszakítása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationJobs/read | Olvassa el a megfelelő feladat |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | Indítsa újra a feladatot |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | Feladat folytatása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | A szabályzatoknak törlése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Olvassa el az összes házirend |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationPolicies/write | Bármely irányelveinek létrehozása vagy frissítése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | A helyreállítási terv törlése |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Feladatátvétel véglegesítésének helyreállítási terv |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Tervezett feladatátvétel helyreállítási terv |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | A helyreállítási tervek olvasása |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Állítsa a helyreállítási terv |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Teszt feladatátvételi helyreállítási terv |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Teszt feladatátadás kitakarítását helyreállítási terv |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Feladatátvételi helyreállítási terv |
> | Műveletek | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | A helyreállítási tervek létrehozása vagy módosítása |
> | Műveletek | Microsoft.RecoveryServices/Vaults/tokenInfo/read | Recovery Services-tároló információi token értéket ad vissza. |
> | Műveletek | Microsoft.RecoveryServices/vaults/usages/read | Olvassa el a tároló módjait |
> | Műveletek | Microsoft.RecoveryServices/Vaults/usages/read | Egy adott Recovery Services-tároló használati adatait adja vissza. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A tároló Token művelet alkalmazható tároló szintű háttérbeli műveletek tároló jogkivonatának beszerzéséhez. |
> | Műveletek | Microsoft.RecoveryServices/Vaults/write | A tárlétrehozási művelettel vault típusú Azure-erőforrás hozható létre |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Relay/checkNameAvailability/action | A névtér adott előfizetésben való elérhetőségének ellenőrzése. |
> | Műveletek | Microsoft.Relay/checkNamespaceAvailability/action | A névtér adott előfizetésben való elérhetőségének ellenőrzése. Ez az API elavult. használja helyette a CheckNameAvailabiltiy. |
> | Műveletek | Microsoft.Relay/namespaces/authorizationRules/action | Frissítések Namespace engedélyezési szabályt. Ez az API depricated. A Namespace engedélyezési szabály sorkészletének frissítéséhez használja a PUT hívás... Ez a művelet nem támogatott az API verzió 2017-04-01. |
> | Műveletek | Microsoft.Relay/namespaces/authorizationRules/delete | Namespace engedélyezési szabály törlése. Az alapértelmezett Namespace engedélyezési szabály nem törölhető.  |
> | Műveletek | Microsoft.Relay/namespaces/authorizationRules/listkeys/action | A névtérhez tartozó kapcsolati karakterlánc beolvasása |
> | Műveletek | Microsoft.Relay/namespaces/authorizationRules/read | A névtér-engedélyezési szabályok leírásai listájának beolvasása. |
> | Műveletek | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása |
> | Műveletek | Microsoft.Relay/namespaces/authorizationRules/write | A Namespace szintű engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és másodlagos kulcsok lehet frissíteni. |
> | Műveletek | Microsoft.Relay/namespaces/Delete | Egy névtérerőforrás törlése |
> | Műveletek | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | A vészhelyreállítás elsődleges névterére vonatkozó engedélyezési szabályok kulcsainak beolvasása |
> | Műveletek | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | Vészhelyreállítás elsődleges névterére vonatkozó engedélyezési szabályok beolvasása |
> | Műveletek | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | A vészhelyreállítás letiltása és a módosítások elsődleges névtérből másodlagos névtérbe történő replikálásának leállítása. |
> | Műveletek | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | A névtéralias adott előfizetésben való elérhetőségének ellenőrzése. |
> | Műveletek | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | A névtér társított vész-helyreállítási konfiguráció törlése. Ezt a műveletet csak az elsődleges névtér keresztül kell meghívni. |
> | Műveletek | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | GEO DR feladatátvételének meghívása és a névtéralias beállítása úgy, hogy a másodlagos névtérre mutasson. |
> | Műveletek | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | A névtérhez hozzárendelt vész-helyreállítási konfiguráció beolvasása. |
> | Műveletek | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | A névtérhez hozzárendelt vész-helyreállítási konfiguráció létrehozása vagy frissítése. |
> | Műveletek | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/action | A művelet HybridConnection frissítéséhez. Ez a művelet nem támogatott az API verzió 2017-04-01. Az engedélyezési szabályok. Egy PUT hívás segítségével engedélyezési szabály frissítése. |
> | Műveletek | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/delete | A művelet törli HybridConnection engedélyezési szabályokat |
> | Műveletek | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/listkeys/action | A kapcsolati karakterláncot HybridConnection beolvasása |
> | Műveletek | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/read |  A HybridConnection engedélyezési szabályok listájának lekérdezése |
> | Műveletek | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása |
> | Műveletek | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/write | HybridConnection engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Az engedélyezési szabályok hozzáférési jogok lehet frissíteni. |
> | Műveletek | Microsoft.Relay/namespaces/HybridConnections/Delete | A művelet HybridConnection erőforrás törlése |
> | Műveletek | Microsoft.Relay/namespaces/HybridConnections/read | Listájának HybridConnection erőforrás leírása |
> | Műveletek | Microsoft.Relay/namespaces/HybridConnections/write | Hozzon létre vagy frissítés HybridConnection tulajdonságai. |
> | Műveletek | Microsoft.Relay/namespaces/messagingPlan/read | Lekérdezi az névtér az üzenetküldési megtervezése.<br>Ez az API elavult.<br>A MessagingPlan erőforrás keresztül elérhetővé tett tulajdonságok (szülő) Namespace erőforrás későbbi API-verziók kerülnek...<br>Ez a művelet nem támogatott az API verzió 2017-04-01. |
> | Műveletek | Microsoft.Relay/namespaces/messagingPlan/write | Frissíti az üzenetkezelési tervezése névtér.<br>Ez az API elavult.<br>A MessagingPlan erőforrás keresztül elérhetővé tett tulajdonságok (szülő) Namespace erőforrás későbbi API-verziók kerülnek...<br>Ez a művelet nem támogatott az API verzió 2017-04-01. |
> | Műveletek | Microsoft.Relay/namespaces/operationresults/read | Névtérművelet állapotának beolvasása |
> | Műveletek | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Namespace metrikák erőforrás leírása listájának beolvasása |
> | Műveletek | Microsoft.Relay/namespaces/read | A névtérerőforrások leírásai listájának beolvasása |
> | Műveletek | Microsoft.Relay/namespaces/removeAcsNamepsace/action | ACS-névtér eltávolítása |
> | Műveletek | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/action | A művelet WcfRelay frissítéséhez. Ez a művelet nem támogatott az API verzió 2017-04-01. Az engedélyezési szabályok. Egy PUT hívás segítségével engedélyezési szabály frissítése. |
> | Műveletek | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/delete | A művelet törli WcfRelay engedélyezési szabályokat |
> | Műveletek | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/listkeys/action | A kapcsolati karakterláncot WcfRelay beolvasása |
> | Műveletek | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/read |  A WcfRelay engedélyezési szabályok listájának lekérdezése |
> | Műveletek | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/action | Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása |
> | Műveletek | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/write | WcfRelay engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Az engedélyezési szabályok hozzáférési jogok lehet frissíteni. |
> | Műveletek | Microsoft.Relay/namespaces/WcfRelays/Delete | A művelet WcfRelay erőforrás törlése |
> | Műveletek | Microsoft.Relay/namespaces/WcfRelays/read | Listájának WcfRelay erőforrás leírása |
> | Műveletek | Microsoft.Relay/namespaces/WcfRelays/write | Hozzon létre vagy frissítés WcfRelay tulajdonságai. |
> | Műveletek | Microsoft.Relay/namespaces/write | Namespace erőforrás létrehozása és frissítése a tulajdonságait. Címkék és a kapacitás, a Namespace nem frissíthető tulajdonságait. |
> | Műveletek | Microsoft.Relay/operations/read | Műveletek beolvasása |
> | Műveletek | Microsoft.Relay/register/action | A Relay erőforrás-szolgáltató előfizetésének regisztrálása és a Relay-erőforrások létrehozásának engedélyezése |
> | Műveletek | Microsoft.Relay/unregister/action | A Relay erőforrás-szolgáltató előfizetésének regisztrálása és a Relay-erőforrások létrehozásának engedélyezése |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | Beolvassa a megadott erőforrás rendelkezésre állási állapotát |
> | Műveletek | Microsoft.ResourceHealth/AvailabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Műveletek | Microsoft.ResourceHealth/events/read | Az adott előfizetés Service Health-eseményeinek beolvasása |
> | Műveletek | Microsoft.Resourcehealth/healthevent/action | A megadott erőforrás állapotának változását jelöli |
> | Műveletek | Microsoft.Resourcehealth/healthevent/Activated/action | A megadott erőforrás állapotának változását jelöli |
> | Műveletek | Microsoft.Resourcehealth/healthevent/InProgress/action | A megadott erőforrás állapotának változását jelöli |
> | Műveletek | Microsoft.Resourcehealth/healthevent/Pending/action | A megadott erőforrás állapotának változását jelöli |
> | Műveletek | Microsoft.Resourcehealth/healthevent/Resolved/action | A megadott erőforrás állapotának változását jelöli |
> | Műveletek | Microsoft.Resourcehealth/healthevent/Updated/action | A megadott erőforrás állapotának változását jelöli |
> | Műveletek | Microsoft.ResourceHealth/impactedResources/read | Az adott előfizetés érintett erőforrásainak beolvasása |
> | Műveletek | Microsoft.ResourceHealth/register/action | A Microsoft Resource Health-előfizetés regisztrálása |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Resources/checkPolicyCompliance/action | Annak ellenőrzése, hogy egy adott erőforrás megfelelőségi állapota megfelel-e az erőforrás-szabályzatoknak. |
> | Műveletek | Microsoft.Resources/checkResourceName/action | Erőforrásnév érvényességének ellenőrzése. |
> | Műveletek | Microsoft.Resources/deployments/cancel/action | Megszakítja az üzembe helyezést. |
> | Műveletek | Microsoft.Resources/deployments/delete | Törli az üzemelő példányt. |
> | Műveletek | Microsoft.Resources/deployments/operations/read | Beolvassa vagy listázza az üzembe helyezési műveleteket. |
> | Műveletek | Microsoft.Resources/deployments/read | Beolvassa vagy listázza az üzemelő példányokat. |
> | Műveletek | Microsoft.Resources/deployments/validate/action | Érvényesíti az üzemelő példányt. |
> | Műveletek | Microsoft.Resources/deployments/write | Létrehozza vagy frissíti az üzemelő példányt. |
> | Műveletek | Microsoft.Resources/links/delete | Törli az erőforrás-hivatkozást. |
> | Műveletek | Microsoft.Resources/links/read | Beolvassa vagy listázza az erőforrás-hivatkozásokat. |
> | Műveletek | Microsoft.Resources/links/write | Létrehozza vagy frissíti az erőforrás-hivatkozást. |
> | Műveletek | Microsoft.Resources/marketplace/purchase/action | Erőforrás vásárlása a Piactéren. |
> | Műveletek | Microsoft.Resources/providers/read | A szolgáltatók listájának lekérése. |
> | Műveletek | Microsoft.Resources/resources/read | Az erőforrások listájának beolvasása szűrők alapján. |
> | Műveletek | Microsoft.Resources/subscriptions/locations/read | Beolvassa a támogatott helyek listáját. |
> | Műveletek | Microsoft.Resources/subscriptions/operationresults/read | Az előfizetési művelet eredményeinek lekérése. |
> | Műveletek | Microsoft.Resources/subscriptions/providers/read | Beolvassa vagy listázza az erőforrás-szolgáltatókat. |
> | Műveletek | Microsoft.Resources/subscriptions/read | Beolvassa az előfizetések listáját. |
> | Műveletek | Microsoft.Resources/subscriptions/resourceGroups/delete | Törli az erőforráscsoportot és az ahhoz tartozó összes erőforrást. |
> | Műveletek | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | Beolvassa vagy listázza az üzembe helyezési műveleteket. |
> | Műveletek | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | Az üzembe helyezési műveletek állapotának beolvasása vagy listázása. |
> | Műveletek | Microsoft.Resources/subscriptions/resourcegroups/deployments/read | Beolvassa vagy listázza az üzemelő példányokat. |
> | Műveletek | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | Létrehozza vagy frissíti az üzemelő példányt. |
> | Műveletek | Microsoft.Resources/subscriptions/resourceGroups/moveResources/action | Erőforrásokat helyez át az egyik erőforráscsoportból egy másikba. |
> | Műveletek | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Műveletek | Microsoft.Resources/subscriptions/resourcegroups/resources/read | Az erőforráscsoporthoz tartozó erőforrások lekérése. |
> | Műveletek | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | Erőforrások erőforráscsoportok közötti áthelyezésének ellenőrzése. |
> | Műveletek | Microsoft.Resources/subscriptions/resourceGroups/write | Létrehozza vagy frissíti az erőforráscsoportot. |
> | Műveletek | Microsoft.Resources/subscriptions/resources/read | Beolvassa az előfizetéshez tartozó erőforrásokat. |
> | Műveletek | Microsoft.Resources/subscriptions/tagNames/delete | Törli az előfizetéscímkét. |
> | Műveletek | Microsoft.Resources/subscriptions/tagNames/read | Beolvassa vagy listázza az előfizetéscímkéket. |
> | Műveletek | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | Törli az előfizetéscímke értékét. |
> | Műveletek | Microsoft.Resources/subscriptions/tagNames/tagValues/read | Beolvassa vagy listázza az előfizetéscímkék értékeit. |
> | Műveletek | Microsoft.Resources/subscriptions/tagNames/tagValues/write | Hozzáadja az előfizetéscímke értékét. |
> | Műveletek | Microsoft.Resources/subscriptions/tagNames/write | Előfizetéscímke hozzáadása. |
> | Műveletek | Microsoft.Resources/tenants/read | Beolvassa a bérlők listáját. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Scheduler/jobcollections/delete | A feladatgyűjtemény törlése. |
> | Műveletek | Microsoft.Scheduler/jobcollections/disable/action | A feladatgyűjtemény letiltása. |
> | Műveletek | Microsoft.Scheduler/jobcollections/enable/action | A feladatgyűjtemény engedélyezése. |
> | Műveletek | Microsoft.Scheduler/jobcollections/jobs/delete | A feladat törlése. |
> | Műveletek | Microsoft.Scheduler/jobcollections/jobs/generateLogicAppDefinition/action | Logic App-definíció előállítása egy Scheduler-feladat alapján. |
> | Műveletek | Microsoft.Scheduler/jobcollections/jobs/jobhistories/read | A feladatelőzmények beolvasása. |
> | Műveletek | Microsoft.Scheduler/jobcollections/jobs/read | A feladat beolvasása. |
> | Műveletek | Microsoft.Scheduler/jobcollections/jobs/run/action | A feladat futtatása. |
> | Műveletek | Microsoft.Scheduler/jobcollections/jobs/write | Feladatok létrehozása és frissítése |
> | Műveletek | Microsoft.Scheduler/jobcollections/read | Feladatgyűjtemény beolvasása |
> | Műveletek | Microsoft.Scheduler/jobcollections/write | Feladatgyűjtemények létrehozása és frissítése |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Search/checkNameAvailability/action | A szolgáltatásnév rendelkezésre állását ellenőrzi. |
> | Műveletek | Microsoft.Search/operations/read | A Microsoft.Search szolgáltató elérhető műveleteinek listája. |
> | Műveletek | Microsoft.Search/register/action | A keresési erőforrás-szolgáltató előfizetésének regisztrálása, és lehetővé teszi a keresési szolgáltatások létrehozását. |
> | Műveletek | Microsoft.Search/searchServices/createQueryKey/action | A lekérdezési kulcsot hoz létre. |
> | Műveletek | Microsoft.Search/searchServices/delete | Törli a keresési szolgáltatást. |
> | Műveletek | Microsoft.Search/searchServices/deleteQueryKey/delete | Törli a lekérdezési kulcsot. |
> | Műveletek | Microsoft.Search/searchServices/diagnosticSettings/read | Lekérdezi a diganostic beállítást olvassa el az erőforrás |
> | Műveletek | Microsoft.Search/searchServices/diagnosticSettings/write | Létrehozza vagy frissíti az erőforrást a diganostic beállítása |
> | Műveletek | Microsoft.Search/searchServices/listAdminKeys/action | Az adminisztrációs kulcsok beolvasása. |
> | Műveletek | Microsoft.Search/searchServices/listQueryKeys/read | Az adott Azure Search szolgáltatás API lekérdezési kulcsok listáját adja vissza. |
> | Műveletek | Microsoft.Search/searchServices/logDefinitions/read | A keresési szolgáltatás naplók beolvasása. |
> | Műveletek | Microsoft.Search/searchServices/metricDefinitions/read | A keresési szolgáltatáshoz elérhető metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.Search/searchServices/read | Olvassa be a keresési szolgáltatást. |
> | Műveletek | Microsoft.Search/searchServices/regenerateAdminKey/action | Az adminisztrátori kulcs újragenerálása. |
> | Műveletek | Microsoft.Search/searchServices/start/action | Elindítja a keresési szolgáltatást. |
> | Műveletek | Microsoft.Search/searchServices/stop/action | A keresési szolgáltatás leállítása. |
> | Műveletek | Microsoft.Search/searchServices/write | Létrehozza vagy frissíti a keresési szolgáltatást. |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Security/alerts/read | Lekérdezi az összes rendelkezésre álló biztonsági riasztások |
> | Műveletek | Microsoft.Security/applicationWhitelistings/read | Az alkalmazás whitelistings beolvasása |
> | Műveletek | Microsoft.Security/applicationWhitelistings/write | Létrehoz egy új alkalmazások engedélyezése vagy frissít egy meglévő |
> | Műveletek | Microsoft.Security/complianceResults/read | A megfelelőségi eredményeket az erőforrás beolvasása |
> | Műveletek | Microsoft.Security/locations/alerts/activate/action | Aktiválja a biztonsági riasztások |
> | Műveletek | Microsoft.Security/locations/alerts/dismiss/action | A biztonsági riasztások elvetése |
> | Műveletek | Microsoft.Security/locations/alerts/read | Lekérdezi az összes rendelkezésre álló biztonsági riasztások |
> | Műveletek | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | Kezdeményezi a közvetlenül az időponthoz kötött hálózati hozzáférési házirend |
> | Műveletek | Microsoft.Security/locations/jitNetworkAccessPolicies/read | Lekérdezi a közvetlenül az időponthoz kötött hálózati hozzáférési házirendek |
> | Műveletek | Microsoft.Security/locations/jitNetworkAccessPolicies/write | Létrehoz egy új just-in-time hálózati hozzáférési házirendet, vagy frissít egy meglévő |
> | Műveletek | Microsoft.Security/locations/read | Lekérdezi a biztonsági adatok helye |
> | Műveletek | Microsoft.Security/locations/tasks/activate/action | Biztonsági ajánlás olyan környezetekben aktiválása |
> | Műveletek | Microsoft.Security/locations/tasks/dismiss/action | Biztonsági ajánlás olyan környezetekben elvetése |
> | Műveletek | Microsoft.Security/locations/tasks/read | Lekérdezi az összes rendelkezésre álló biztonsági javaslatok |
> | Műveletek | Microsoft.Security/locations/tasks/resolve/action | Biztonsági ajánlás olyan környezetekben feloldása |
> | Műveletek | Microsoft.Security/locations/tasks/start/action | Indítsa el a biztonsági ajánlás olyan környezetekben |
> | Műveletek | Microsoft.Security/policies/read | Lekérdezi a biztonsági házirend |
> | Műveletek | Microsoft.Security/policies/write | A biztonsági házirend frissítése |
> | Műveletek | Microsoft.Security/pricings/delete | Törli a hatókör árképzési beállításokat |
> | Műveletek | Microsoft.Security/pricings/read | A hatókör árképzési beállításainak beolvasása |
> | Műveletek | Microsoft.Security/pricings/write | Frissíti a hatókör árképzési beállításait |
> | Műveletek | Microsoft.Security/register/action | Az Azure Security Center előfizetésének regisztrálása |
> | Műveletek | Microsoft.Security/securityContacts/delete | A biztonsági ügyfél törlése |
> | Műveletek | Microsoft.Security/securityContacts/read | A biztonsági ügyfél beolvasása |
> | Műveletek | Microsoft.Security/securityContacts/write | A biztonsági ügyfél frissítése |
> | Műveletek | Microsoft.Security/securitySolutions/delete | Olyan biztonsági megoldás törlése |
> | Műveletek | Microsoft.Security/securitySolutions/read | Lekérdezi a biztonsági megoldások |
> | Műveletek | Microsoft.Security/securitySolutions/write | Létrehoz egy új biztonsági megoldást, vagy frissít egy meglévő |
> | Műveletek | Microsoft.Security/securitySolutionsReferenceData/read | Lekérdezi a biztonsági megoldások referenciaadatok |
> | Műveletek | Microsoft.Security/securityStatuses/read | Az Azure-erőforrások állapotát állapotok a biztonsági beolvasása |
> | Műveletek | Microsoft.Security/securityStatusesSummaries/read | A biztonsági állapot-összefoglalók lekérdezi a hatókör |
> | Műveletek | Microsoft.Security/tasks/read | Lekérdezi az összes rendelkezésre álló biztonsági javaslatok |
> | Műveletek | Microsoft.Security/webApplicationFirewalls/delete | Webalkalmazási tűzfal törlése |
> | Műveletek | Microsoft.Security/webApplicationFirewalls/read | Lekérdezi a webes alkalmazás tűzfalak |
> | Műveletek | Microsoft.Security/webApplicationFirewalls/write | Létrehoz egy új webalkalmazási tűzfal vagy frissít egy meglévő |
> | Műveletek | Microsoft.Security/workspaceSettings/connect/action | Munkaterület beállítások újracsatlakozás beállításainak módosítása |
> | Műveletek | Microsoft.Security/workspaceSettings/delete | A munkaterület beállításainak törlése |
> | Műveletek | Microsoft.Security/workspaceSettings/read | A munkaterület beállításainak beolvasása |
> | Műveletek | Microsoft.Security/workspaceSettings/write | A munkaterület beállításainak frissítése |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.SecurityGraph/diagnosticsettings/delete | A diagnosztikai beállítás törlése |
> | Műveletek | Microsoft.SecurityGraph/diagnosticsettings/read | A diagnosztikai beállítás beolvasásakor. |
> | Műveletek | Microsoft.SecurityGraph/diagnosticsettings/write | A diagnosztikai beállításának írása |
> | Műveletek | Microsoft.SecurityGraph/diagnosticsettingscategories/read | A diagnosztikai beállításának kategóriák olvasása |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ServiceBus/checkNameAvailability/action | A névtér adott előfizetésben való elérhetőségének ellenőrzése. |
> | Műveletek | Microsoft.ServiceBus/checkNamespaceAvailability/action | A névtér adott előfizetésben való elérhetőségének ellenőrzése. Ez az API elavult. használja helyette a CheckNameAvailabiltiy. |
> | Műveletek | Microsoft.ServiceBus/namespaces/authorizationRules/action | Frissítések Namespace engedélyezési szabályt. Ez az API depricated. A Namespace engedélyezési szabály sorkészletének frissítéséhez használja a PUT hívás... Ez a művelet nem támogatott az API verzió 2017-04-01. |
> | Műveletek | Microsoft.ServiceBus/namespaces/authorizationRules/delete | Namespace engedélyezési szabály törlése. Az alapértelmezett Namespace engedélyezési szabály nem törölhető.  |
> | Műveletek | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | A névtérhez tartozó kapcsolati karakterlánc beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/authorizationRules/read | A névtér-engedélyezési szabályok leírásai listájának beolvasása. |
> | Műveletek | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása |
> | Műveletek | Microsoft.ServiceBus/namespaces/authorizationRules/write | A Namespace szintű engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és másodlagos kulcsok lehet frissíteni. |
> | Műveletek | Microsoft.ServiceBus/namespaces/Delete | Egy névtérerőforrás törlése |
> | Műveletek | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | A vészhelyreállítás elsődleges névterére vonatkozó engedélyezési szabályok kulcsainak beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | Vészhelyreállítás elsődleges névterére vonatkozó engedélyezési szabályok beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | A vészhelyreállítás letiltása és a módosítások elsődleges névtérből másodlagos névtérbe történő replikálásának leállítása. |
> | Műveletek | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | A névtéralias adott előfizetésben való elérhetőségének ellenőrzése. |
> | Műveletek | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | A névtér társított vész-helyreállítási konfiguráció törlése. Ezt a műveletet csak az elsődleges névtér keresztül kell meghívni. |
> | Műveletek | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | GEO DR feladatátvételének meghívása és a névtéralias beállítása úgy, hogy a másodlagos névtérre mutasson. |
> | Műveletek | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | A névtérhez hozzárendelt vész-helyreállítási konfiguráció beolvasása. |
> | Műveletek | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | A névtérhez hozzárendelt vész-helyreállítási konfiguráció létrehozása vagy frissítése. |
> | Műveletek | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | A névtérhez társított Event Grid-szűrő törlése. |
> | Műveletek | Microsoft.ServiceBus/namespaces/eventGridFilters/read | A névtérhez társított Event Grid-szűrő beolvasása. |
> | Műveletek | Microsoft.ServiceBus/namespaces/eventGridFilters/write | A névtérhez társított Event Grid-szűrő létrehozása vagy frissítése. |
> | Műveletek | Microsoft.ServiceBus/namespaces/eventhubs/read | Az EventHub erőforrás leírások listáját |
> | Műveletek | Microsoft.ServiceBus/namespaces/messagingPlan/read | Lekérdezi az névtér az üzenetküldési megtervezése.<br>Ez az API elavult.<br>A MessagingPlan erőforrás keresztül elérhetővé tett tulajdonságok (szülő) Namespace erőforrás későbbi API-verziók kerülnek...<br>Ez a művelet nem támogatott az API verzió 2017-04-01. |
> | Műveletek | Microsoft.ServiceBus/namespaces/messagingPlan/write | Frissíti az üzenetkezelési tervezése névtér.<br>Ez az API elavult.<br>A MessagingPlan erőforrás keresztül elérhetővé tett tulajdonságok (szülő) Namespace erőforrás későbbi API-verziók kerülnek...<br>Ez a művelet nem támogatott az API verzió 2017-04-01. |
> | Műveletek | Microsoft.ServiceBus/namespaces/migrate/action | Névtérmigrálási művelet |
> | Műveletek | Microsoft.ServiceBus/namespaces/operationresults/read | Névtérművelet állapotának beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Namespace diagnosztikai beállítások erőforrás leírása listájának beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Namespace diagnosztikai beállítások erőforrás leírása listájának beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | Listájának Namespace naplók erőforrás leírása |
> | Műveletek | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Namespace metrikák erőforrás leírása listájának beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | Várólista frissítési művelete. Ez a művelet nem támogatott az API verzió 2017-04-01. Az engedélyezési szabályok. Egy PUT hívás segítségével engedélyezési szabály frissítése. |
> | Műveletek | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | Művelet az engedélyezési szabályok várólista törlése |
> | Műveletek | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | A kapcsolati karakterlánc várólistára beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  A várólista-engedélyezési szabályok listájának lekérdezése |
> | Műveletek | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása |
> | Műveletek | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | Várólista engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Az engedélyezési szabályok hozzáférési jogok lehet frissíteni. |
> | Műveletek | Microsoft.ServiceBus/namespaces/queues/Delete | A művelet várólista erőforrás törlése |
> | Műveletek | Microsoft.ServiceBus/namespaces/queues/read | Várólista erőforrás leírása listájának beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/queues/write | Hozzon létre vagy frissítés várólista-tulajdonságok. |
> | Műveletek | Microsoft.ServiceBus/namespaces/read | A névtérerőforrások leírásai listájának beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | ACS-névtér eltávolítása |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | A témakör frissítése műveletet. Ez a művelet nem támogatott az API verzió 2017-04-01. Az engedélyezési szabályok. Egy PUT hívás segítségével engedélyezési szabály frissítése. |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | A művelet törli a témakör az engedélyezési szabályok |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | A témakör a kapcsolati karakterlánc beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  A témakör az engedélyezési szabályok listájának lekérdezése |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | A témakör az engedélyezési szabályok létrehozása és frissítése a tulajdonságait. Az engedélyezési szabályok hozzáférési jogok lehet frissíteni. |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/Delete | A művelet témakör erőforrás törlése |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/read | A témakör erőforrás leírások listáját |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | A művelet TopicSubscription erőforrás törlése |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | Listájának TopicSubscription erőforrás leírása |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | Műveletet, szabály erőforrás törlése |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | Szabály erőforrás leírása listájának beolvasása |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/write | Hozzon létre vagy frissítés szabály tulajdonságait. |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | Hozzon létre vagy frissítés TopicSubscription tulajdonságai. |
> | Műveletek | Microsoft.ServiceBus/namespaces/topics/write | Hozzon létre vagy frissítés témakör tulajdonságai. |
> | Műveletek | Microsoft.ServiceBus/namespaces/write | Namespace erőforrás létrehozása és frissítése a tulajdonságait. Címkék és a kapacitás, a Namespace nem frissíthető tulajdonságait. |
> | Műveletek | Microsoft.ServiceBus/operations/read | Műveletek beolvasása |
> | Műveletek | Microsoft.ServiceBus/register/action | Regisztrálja az előfizetést a ServiceBus erőforrás-szolgáltatóra, és lehetővé teszi a ServiceBus-erőforrások létrehozását |
> | Műveletek | Microsoft.ServiceBus/sku/read | Termékváltozat erőforrás leírása listájának beolvasása |
> | Műveletek | Microsoft.ServiceBus/sku/regions/read | Listájának SkuRegions erőforrás leírása |
> | Műveletek | Microsoft.ServiceBus/unregister/action | Regisztrálja az előfizetést a ServiceBus erőforrás-szolgáltatóra, és lehetővé teszi a ServiceBus-erőforrások létrehozását |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.ServiceFabric/clusters/applications/delete | Tetszőleges alkalmazás törlése |
> | Műveletek | Microsoft.ServiceFabric/clusters/applications/read | Tetszőleges alkalmazás beolvasása |
> | Műveletek | Microsoft.ServiceFabric/clusters/applications/services/delete | Tetszőleges szolgáltatás törlése |
> | Műveletek | Microsoft.ServiceFabric/clusters/applications/services/partitions/read | Tetszőleges partíció beolvasása |
> | Műveletek | Microsoft.ServiceFabric/clusters/applications/services/partitions/replicas/read | Tetszőleges replika beolvasása |
> | Műveletek | Microsoft.ServiceFabric/clusters/applications/services/read | Tetszőleges szolgáltatás beolvasása |
> | Műveletek | Microsoft.ServiceFabric/clusters/applications/services/statuses/read | Bármely szolgáltatásállapot beolvasása |
> | Műveletek | Microsoft.ServiceFabric/clusters/applications/services/write | Tetszőleges szolgáltatás létrehozása vagy frissítése |
> | Műveletek | Microsoft.ServiceFabric/clusters/applications/write | Tetszőleges alkalmazás létrehozása vagy frissítése |
> | Műveletek | Microsoft.ServiceFabric/clusters/applicationTypes/delete | Tetszőleges alkalmazástípus törlése |
> | Műveletek | Microsoft.ServiceFabric/clusters/applicationTypes/read | Tetszőleges alkalmazástípus beolvasása |
> | Műveletek | Microsoft.ServiceFabric/clusters/applicationTypes/versions/delete | Tetszőleges alkalmazástípus-verzió törlése |
> | Műveletek | Microsoft.ServiceFabric/clusters/applicationTypes/versions/read | Tetszőleges alkalmazástípus-verzió beolvasása |
> | Műveletek | Microsoft.ServiceFabric/clusters/applicationTypes/versions/write | Tetszőleges alkalmazástípus-verzió létrehozása vagy frissítése |
> | Műveletek | Microsoft.ServiceFabric/clusters/applicationTypes/write | Tetszőleges alkalmazástípus létrehozása vagy frissítése |
> | Műveletek | Microsoft.ServiceFabric/clusters/delete | Tetszőleges fürt törlése |
> | Műveletek | Microsoft.ServiceFabric/clusters/nodes/read | Tetszőleges csomópont beolvasása |
> | Műveletek | Microsoft.ServiceFabric/clusters/read | Tetszőleges fürt beolvasása |
> | Műveletek | Microsoft.ServiceFabric/clusters/statuses/read | Bármely fürtállapot beolvasása |
> | Műveletek | Microsoft.ServiceFabric/clusters/write | Tetszőleges fürt létrehozása vagy frissítése |
> | Műveletek | Microsoft.ServiceFabric/locations/clusterVersions/read | Minden fürtverzió beolvasása |
> | Műveletek | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | Tetszőleges fürtverzió beolvasása egy adott környezetben |
> | Műveletek | Microsoft.ServiceFabric/locations/operationresults/read | Tetszőleges műveleti eredmények beolvasása |
> | Műveletek | Microsoft.ServiceFabric/locations/operations/read | Tetszőleges műveletek beolvasása hely szerint |
> | Műveletek | Microsoft.ServiceFabric/operations/read | Tetszőleges használható műveletek beolvasása |
> | Műveletek | Microsoft.ServiceFabric/register/action | Tetszőleges művelet regisztrálása |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.SignalRService/checknameavailability/action | Ellenőrzi, hogy a név egy új szolgáltatással SignalR használható |
> | Műveletek | Microsoft.SignalRService/register/action | Előfizetés az "Microsoft.SignalRService" erőforrás-szolgáltató regisztrálása |
> | Műveletek | Microsoft.SignalRService/SignalR/delete | A teljes SignalR törlése |
> | Műveletek | Microsoft.SignalRService/SignalR/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.SignalRService/SignalR/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.SignalRService/SignalR/providers/Microsoft.Insights/metricDefinitions/read | A SignalR elérhető metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.SignalRService/SignalR/read | A SignalR beállítás és konfiguráció megtekintése, a kezelési portál vagy API-n keresztül |
> | Műveletek | Microsoft.SignalRService/SignalR/write | Módosítsa a SignalR beállítás és konfiguráció a kezelési portál vagy API-n keresztül |
> | Műveletek | Microsoft.SignalRService/unregister/action | Előfizetés az "Microsoft.SignalRService" erőforrás-szolgáltató regisztrációjának törlése |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Solutions/applicationDefinitions/delete | Alkalmazásdefiníció eltávolítása. |
> | Műveletek | Microsoft.Solutions/applicationDefinitions/read | Az alkalmazásdefiníciók listájának beolvasása. |
> | Műveletek | Microsoft.Solutions/applicationDefinitions/write | Alkalmazásdefiníció hozzáadása vagy módosítása. |
> | Műveletek | Microsoft.Solutions/applications/delete | Alkalmazás eltávolítása. |
> | Műveletek | Microsoft.Solutions/applications/read | Az alkalmazások listájának beolvasása. |
> | Műveletek | Microsoft.Solutions/applications/write | Létrehoz egy alkalmazást. |
> | Műveletek | Microsoft.Solutions/locations/operationStatuses/read | Az erőforrás műveleti állapotának beolvasása. |
> | Műveletek | Microsoft.Solutions/register/action | Regisztrálás megoldásokra. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Sql/checkNameAvailability/action | Ellenőrizze, hogy a megadott kiszolgáló neve rendelkezésre álló világszerte az adott előfizetéshez. |
> | Műveletek | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | A kiterjesztett kiszolgáló blob naplózási házirend-beállítási művelet eredményének beolvasása |
> | Műveletek | Microsoft.Sql/locations/auditingSettingsOperationResults/read | A kiszolgáló blob naplózási házirend-beállítási művelet eredményének beolvasása |
> | Műveletek | Microsoft.Sql/locations/capabilities/read | A képességek lekérdezi az előfizetés egy adott helyen |
> | Műveletek | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | Egy adatbázis-művelet állapotát olvassa be. |
> | Műveletek | Microsoft.Sql/locations/databaseOperationResults/read | Egy adatbázis-művelet állapotát olvassa be. |
> | Műveletek | Microsoft.Sql/locations/deletedServerAsyncOperation/read | Lekérdezi a folyamatban lévő műveletek törölt kiszolgálón |
> | Műveletek | Microsoft.Sql/locations/deletedServerOperationResults/read | Lekérdezi a folyamatban lévő műveletek törölt kiszolgálón |
> | Műveletek | Microsoft.Sql/locations/deletedServers/read | A törölt kiszolgálók vagy lekérdezi a megadott törölt kiszolgáló tulajdonságok listáját adja vissza. |
> | Műveletek | Microsoft.Sql/locations/deletedServers/recover/action | Törölt kiszolgáló helyreállítása |
> | Műveletek | Microsoft.Sql/locations/deleteVirtualNetworkOrSubnets/action | Egy virtuális hálózathoz vagy alhálózathoz társított virtuális hálózati szabály törlése |
> | Műveletek | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | Az azure aszinkron művelet, a rugalmas készlet aszinkron művelet lekérdezi |
> | Műveletek | Microsoft.Sql/locations/elasticPoolOperationResults/read | Egy rugalmas készlet művelet eredményének beolvasása. |
> | Műveletek | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | A kiterjesztett kiszolgáló blob naplózási házirend-beállítási művelet eredményének beolvasása |
> | Műveletek | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | A kiterjesztett kiszolgáló blob naplózási házirend-beállítási művelet eredményének beolvasása |
> | Műveletek | Microsoft.Sql/locations/instanceFailoverGroups/delete | Egy meglévő példányát feladatátvételi csoport törlése. |
> | Műveletek | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | Tervezett feladatátvétel végrehajtása a meglévő példány feladatátvételi csoport. |
> | Műveletek | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | Egy meglévő feladatátvevő példánycsoport kényszerített feladatátvételi hajtja végre. |
> | Műveletek | Microsoft.Sql/locations/instanceFailoverGroups/read | Az a példány feladatátvételi csoportokat vagy a megadott példány feladatátvételi csoport tulajdonságainak beolvasása adja vissza. |
> | Műveletek | Microsoft.Sql/locations/instanceFailoverGroups/write | Példány feladatátvevő csoportot hoz létre a megadott paraméterekkel, vagy frissíti a Tulajdonságok vagy a megadott példány feladatátvételi csoport címkék. |
> | Műveletek | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | Felügyelt adatbázis-visszaállítási művelet befejezéséről. |
> | Műveletek | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | Lekérdezi a folyamatban lévő műveletek felügyelt adatbázis átlátható adattitkosítás |
> | Műveletek | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | Lekérdezi a folyamatban lévő műveletek felügyelt adatbázis átlátható adattitkosítás |
> | Műveletek | Microsoft.Sql/locations/networkInterfaceAzureAsyncOperation/read | Egy adott hálózati adapter Azure aszinkron művelet adatait adja vissza. |
> | Műveletek | Microsoft.Sql/locations/networkInterfaceOperationResults/read | A megadott hálózati illesztő működési adatait adja vissza. |
> | Műveletek | Microsoft.Sql/locations/read | Lekérdezi a megadott előfizetéshez tartozó helyeket |
> | Műveletek | Microsoft.Sql/locations/syncAgentOperationResults/read | A szinkronizálási ügynök erőforrás művelet eredményének beolvasása |
> | Műveletek | Microsoft.Sql/locations/syncDatabaseIds/read | A szinkronizálási adatbázis-azonosító egy adott régióban, és az előfizetés beolvasása |
> | Műveletek | Microsoft.Sql/locations/syncGroupOperationResults/read | A szinkronizálás erőforrás-művelet eredményének beolvasása |
> | Műveletek | Microsoft.Sql/locations/syncMemberOperationResults/read | A szinkronizálás tag erőforrás művelet eredményének beolvasása |
> | Műveletek | Microsoft.Sql/locations/usages/read | Ehhez az előfizetéshez, egy helyen lekérdezi a szoftverhasználati mérési adatok gyűjteménye |
> | Műveletek | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | Adatait adja vissza. a megadott virtuális hálózati szabályok azure aszinkron művelet  |
> | Műveletek | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | A megadott virtuális hálózati szabályok művelet adatait adja vissza.  |
> | Műveletek | Microsoft.Sql/managedInstances/administrators/delete | Törli a meglévő rendszergazda felügyelt példány. |
> | Műveletek | Microsoft.Sql/managedInstances/administrators/read | Felügyelt példány rendszergazdák listájának lekérése. |
> | Műveletek | Microsoft.Sql/managedInstances/administrators/write | Létrehozza vagy frissíti a felügyelt példány rendszergazda a megadott paraméterekkel. |
> | Műveletek | Microsoft.Sql/managedInstances/databases/delete | Törli a meglévő felügyelt adatbázis |
> | Műveletek | Microsoft.Sql/managedInstances/databases/read | Lekérdezi a meglévő felügyelt adatbázisba |
> | Műveletek | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | Az adatbázis fenyegetés szabályzat egy adott felügyelt adatbázisnak konfigurált részleteinek beolvasása |
> | Műveletek | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | Egy adott felügyelt adatbázisnak adatbázis threat detection szabályzatának módosítása |
> | Műveletek | Microsoft.Sql/managedInstances/databases/securityEvents/read | Beolvassa a felügyelt adatbázis biztonsági események |
> | Műveletek | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | Egy adott felügyelt adatbázison átlátható adattitkosítási adatbázis adatait beolvasása |
> | Műveletek | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | Az adatbázis az átlátható adattitkosítási egy adott felügyelt adatbázis |
> | Műveletek | Microsoft.Sql/managedInstances/databases/write | Létrehoz egy új adatbázist, vagy egy meglévő adatbázist frissíti. |
> | Műveletek | Microsoft.Sql/managedInstances/delete | Törli a meglévő felügyelt példány. |
> | Műveletek | Microsoft.Sql/managedInstances/metricDefinitions/read | Felügyelt példány metrikai meghatározásainak beolvasása |
> | Műveletek | Microsoft.Sql/managedInstances/metrics/read | Felügyelt példány metrikák beolvasása |
> | Műveletek | Microsoft.Sql/managedInstances/read | A figyelt példányokat vagy lekérdezi a megadott felügyelt példány tulajdonságainak listáját adja vissza. |
> | Műveletek | Microsoft.Sql/managedInstances/securityAlertPolicies/read | A felügyelt kiszolgáló fenyegetés szabályzat egy adott felügyelt kiszolgálón konfigurált részleteinek beolvasása |
> | Műveletek | Microsoft.Sql/managedInstances/securityAlertPolicies/write | A felügyelt kiszolgáló fenyegetés szabályzat egy adott felügyelt kiszolgáló módosítása |
> | Műveletek | Microsoft.Sql/managedInstances/write | Létrehoz egy felügyelt példányt a megadott paraméterekkel, vagy tulajdonságainak vagy a megadott felügyelt példány címkék frissítése. |
> | Műveletek | Microsoft.Sql/operations/read | Lekérdezi a használható további műveletek |
> | Műveletek | Microsoft.Sql/register/action | A Microsoft SQL-adatbázis erőforrás-szolgáltató előfizetésének regisztrálása, és lehetővé teszi a Microsoft SQL-adatbázisok létrehozását. |
> | Műveletek | Microsoft.Sql/servers/administratorOperationResults/read | Lekérdezi a folyamatban lévő műveleteket a kiszolgáló-rendszergazdák |
> | Műveletek | Microsoft.Sql/servers/administrators/delete | Törölje a kiszolgáló rendszergazdája |
> | Műveletek | Microsoft.Sql/servers/administrators/read | Rendszergazda kiszolgálóadatok beolvasása |
> | Műveletek | Microsoft.Sql/servers/administrators/write | Létrehozni vagy frissíteni a kiszolgáló rendszergazdája |
> | Műveletek | Microsoft.Sql/servers/advisors/read | A kiszolgáló elérhető tanácsadók listáját adja vissza |
> | Műveletek | Microsoft.Sql/servers/advisors/recommendedActions/read | Az a kiszolgáló megadott advisor ajánlott műveletek listáját adja vissza |
> | Műveletek | Microsoft.Sql/servers/advisors/recommendedActions/write | A javasolt műveletet alkalmazza a kiszolgálón |
> | Műveletek | Microsoft.Sql/servers/advisors/write | Frissítések automatikus-hajtható végre az advisor a kiszolgáló szintjén állapotát. |
> | Műveletek | Microsoft.Sql/servers/auditingPolicies/read | Az alapértelmezett kiszolgáló táblázat naplózás a megadott kiszolgálón konfigurált házirend részleteinek beolvasása |
> | Műveletek | Microsoft.Sql/servers/auditingPolicies/write | Az alapértelmezett kiszolgáló tábla naplózását egy adott kiszolgáló módosítása |
> | Műveletek | Microsoft.Sql/servers/auditingSettings/operationResults/read | A kiszolgáló blob naplózási házirend-beállítási művelet eredményének beolvasása |
> | Műveletek | Microsoft.Sql/servers/auditingSettings/read | A kiszolgáló blob naplózási házirend adatait a megadott kiszolgálón konfigurált beolvasása |
> | Műveletek | Microsoft.Sql/servers/auditingSettings/write | A kiszolgáló blob naplózását egy adott kiszolgáló módosítása |
> | Műveletek | Microsoft.Sql/servers/automaticTuning/read | A kiszolgáló automatikus hangolási beállításainak adja vissza |
> | Műveletek | Microsoft.Sql/servers/automaticTuning/write | A kiszolgáló automatikus hangolási beállításainak frissíti, és adja vissza a frissített beállításokkal |
> | Műveletek | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | Egy meglévő biztonsági mentési, archiválási tárolóban törli. |
> | Műveletek | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | Ez a művelet egy biztonsági mentési hosszú távú megőrzési tárolóban eléréséhez használatos. A tárolóban regisztrált erre a kiszolgálóra vonatkozó információkat ad vissza |
> | Műveletek | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | Ez a művelet egy biztonsági mentési hosszú távú megőrzési regisztrálásához használt kiszolgálóra tároló |
> | Műveletek | Microsoft.Sql/servers/communicationLinks/delete | Törli a meglévő kiszolgálói kommunikáció hivatkozás. |
> | Műveletek | Microsoft.Sql/servers/communicationLinks/read | A megadott kiszolgáló a kommunikációs kapcsolatok listáját adja vissza. |
> | Műveletek | Microsoft.Sql/servers/communicationLinks/write | Létrehozása vagy frissítése server kommunikációs kapcsolatot. |
> | Műveletek | Microsoft.Sql/servers/connectionPolicies/read | Térjen vissza a megadott kiszolgáló kiszolgálói kapcsolat házirendek listájában. |
> | Műveletek | Microsoft.Sql/servers/connectionPolicies/write | Létrehozása vagy módosítása egy kiszolgáló kapcsolatkezelési házirendet. |
> | Műveletek | Microsoft.Sql/servers/databases/advisors/read | Az adatbázis elérhető tanácsadók listáját adja vissza |
> | Műveletek | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | A megadott advisort az adatbázishoz ajánlott műveletek listáját adja vissza |
> | Műveletek | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | A javasolt műveletet alkalmazza az adatbázishoz |
> | Műveletek | Microsoft.Sql/servers/databases/advisors/write | Frissítés Automatikus végrehajtás az advisor adatbázis szinten állapotának. |
> | Műveletek | Microsoft.Sql/servers/databases/auditingPolicies/read | A naplózási házirend tábla, egy adott adatbázisnak konfigurált adatait beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/auditingPolicies/write | A tábla egy adott adatbázisnak a naplózási házirend módosítása |
> | Műveletek | Microsoft.Sql/servers/databases/auditingSettings/read | Egy adott adatbázisnak konfigurált blob naplózási házirend részleteinek beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/auditingSettings/write | A blob egy adott adatbázisnak a naplózási házirend módosítása |
> | Műveletek | Microsoft.Sql/servers/databases/auditRecords/read | A blob naplózási rekordok beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/automaticTuning/read | Automatikus hangolása egy adatbázis-beállításainak beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/automaticTuning/write | Egy adatbázis automatikus hangolási beállításainak frissíti, és adja vissza a frissített beállításokkal |
> | Műveletek | Microsoft.Sql/servers/databases/azureAsyncOperation/read | Egy adatbázis-művelet állapotát olvassa be. |
> | Műveletek | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | Térjen vissza a megadott adatbázis biztonsági mentési archiválási házirendek listájában. |
> | Műveletek | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | Hozzon létre, vagy frissítse az adatbázis biztonsági mentési, archiválási házirend. |
> | Műveletek | Microsoft.Sql/servers/databases/connectionPolicies/read | A kapcsolat házirend egy adott adatbázisnak konfigurált részleteinek beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/connectionPolicies/write | Módosítsa a kapcsolatkezelési házirendet az egy adott adatbázisnak |
> | Műveletek | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | Az adatbázis adatmaszkolási házirendek listáját adja vissza. |
> | Műveletek | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | Egy adott adatbázis házirendszabály adatmaszkolási törlése |
> | Műveletek | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | A adatmaszkolási egy adott adatbázisnak konfigurált házirendszabály részleteit beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | Adatok házirend szabály egy adott adatbázisnak a maszkolás módosítása |
> | Műveletek | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | Adatmaszkolási egy adott adatbázis-házirend módosítása |
> | Műveletek | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | Információkat ad vissza az elosztott lekérdezés lépés adatraktár lekérdezés kijelölt lépés azonosítója |
> | Műveletek | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | Az adatraktár terjesztési lekérdezés információi a kijelölt lekérdezés Azonosítóját adja vissza |
> | Műveletek | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | Lekéri a felhasználói tevékenység fut, és a felfüggesztett lekérdezések köztük az SQL Data Warehouse-példány |
> | Műveletek | Microsoft.Sql/servers/databases/delete | Törli a meglévő adatbázist. |
> | Műveletek | Microsoft.Sql/servers/databases/export/action | Az Azure SQL adatbázis exportálása |
> | Műveletek | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Egy adott adatbázisnak konfigurált bővített blob naplózási házirend részleteinek beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | A bővített blob egy adott adatbázisnak a naplózási házirend módosítása |
> | Műveletek | Microsoft.Sql/servers/databases/extensions/read | Az adatbázis lekérdezi a bővítmények gyűjteménye. |
> | Műveletek | Microsoft.Sql/servers/databases/extensions/write | A megadott adatbázis bővítmény módosítása |
> | Műveletek | Microsoft.Sql/servers/databases/geoBackupPolicies/read | Georedundáns biztonsági mentési házirendek egy adott adatbázis beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/geoBackupPolicies/write | Hozzon létre vagy egy adatbázis geobackup házirend frissítése |
> | Műveletek | Microsoft.Sql/servers/databases/importExportOperationResults/read | Lekérdezi a folyamatban lévő importálási/exportálási műveleteket |
> | Műveletek | Microsoft.Sql/servers/databases/metricDefinitions/read | Térjen vissza a típusú ítélt adatbázisokhoz |
> | Műveletek | Microsoft.Sql/servers/databases/metrics/read | Térjen vissza az adatbázisok metrikák |
> | Műveletek | Microsoft.Sql/servers/databases/move/action | Az Azure SQL-adatbázis átnevezése |
> | Műveletek | Microsoft.Sql/servers/databases/operationResults/read | Egy adatbázis-művelet állapotát olvassa be. |
> | Műveletek | Microsoft.Sql/servers/databases/operations/cancel/action | Az Azure SQL Database megszakítja a függőben lévő aszinkron művelet, amely még nem fejeződött be. |
> | Műveletek | Microsoft.Sql/servers/databases/operations/read | Térjen vissza az adatbázison végrehajtott műveletek listája |
> | Műveletek | Microsoft.Sql/servers/databases/pause/action | Az Azure SQL-adatraktár adatbázisa felfüggesztése |
> | Műveletek | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | Az adatbázisok naplók beolvasása. |
> | Műveletek | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/metricDefinitions/read | Térjen vissza a típusú ítélt adatbázisokhoz |
> | Műveletek | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | A gyűjtemény lekérdezési szövegek, amelyek megfelelnek a megadott paraméterek adja vissza. |
> | Műveletek | Microsoft.Sql/servers/databases/queryStore/read | A Lekérdezéstár beállítása az adatbázis aktuális értékek beolvasása. |
> | Műveletek | Microsoft.Sql/servers/databases/queryStore/write | A Lekérdezéstár-beállítás az adatbázis frissítése |
> | Műveletek | Microsoft.Sql/servers/databases/read | Adatbázisok vagy lekérdezi a megadott adatbázis tulajdonságainak megadása listáját adja vissza. |
> | Műveletek | Microsoft.Sql/servers/databases/replicationLinks/delete | Állítsa le a replikációs kapcsolat kényszerített módon, és az esetleges adatvesztés |
> | Műveletek | Microsoft.Sql/servers/databases/replicationLinks/failover/action | Feladatátvételi minden szinkronizálás után az elsődleges megváltozik, ezt az adatbázist és a replikációs relationship\u0027s az elsődleges és a távoli elsődleges és másodlagos be |
> | Műveletek | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | Feladatátvétel azonnal esetleges adatvesztés, ezt az adatbázist és a replikációs relationship\u0027s az elsődleges és a távoli elsődleges és másodlagos be |
> | Műveletek | Microsoft.Sql/servers/databases/replicationLinks/read | Egy adott adatbázist létrehozni a replikációs hivatkozások visszatérési részletei |
> | Műveletek | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | Állítsa le a replikációs kapcsolat kényszerített módon vagy a partnerrel való szinkronizálás után |
> | Műveletek | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | A frissítési replikációs mód csatolás szinkron vagy aszinkron módban |
> | Műveletek | Microsoft.Sql/servers/databases/restorePoints/action | Új visszaállítási pont létrehozása |
> | Műveletek | Microsoft.Sql/servers/databases/restorePoints/delete | Törli az adatbázis visszaállítási pontot. |
> | Műveletek | Microsoft.Sql/servers/databases/restorePoints/read | Vissza visszaállítási pontok az adatbázishoz. |
> | Műveletek | Microsoft.Sql/servers/databases/resume/action | Az Azure SQL-adatraktár adatbázisa folytatása |
> | Műveletek | Microsoft.Sql/servers/databases/schemas/read | Adatbázis sémák listájának beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Egy tábla oszlopainak listájának beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | A megadott oszlop tartalomcímke törlése |
> | Műveletek | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | A megadott oszlop tartalomcímke beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | Létrehozni vagy frissíteni az adott oszlop tartalomcímke |
> | Műveletek | Microsoft.Sql/servers/databases/schemas/tables/read | Egy adatbázis táblák listáját beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | Egy adatbázishoz ajánlott index listájának beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | Alkalmazza az ajánlott index |
> | Műveletek | Microsoft.Sql/servers/databases/securityAlertPolicies/read | A fenyegetés szabályzat egy adott adatbázisnak konfigurált részleteinek beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/securityAlertPolicies/write | Egy adott adatbázisnak threat detection szabályzatának módosítása |
> | Műveletek | Microsoft.Sql/servers/databases/securityMetrics/read | Lekérdezi az adatbázis biztonsági metrikák gyűjteménye |
> | Műveletek | Microsoft.Sql/servers/databases/sensitivityLabels/read | Egy adott adatbázisnak érzékenységi címkéket listája |
> | Műveletek | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | Térjen javaslat adatbázis méretezésével felfelé vagy lefelé kapcsolatos teljesítményének javítása és a költségek csökkentése a lekérdezés végrehajtási statisztika alapján |
> | Műveletek | Microsoft.Sql/servers/databases/skus/read | Az adatbázis elérhető termékváltozatok gyűjteménye beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | Szinkronizálási csoport szinkronizálás megszakítása |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/delete | Törli a meglévő szinkronizálási csoport. |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | Térjen vissza a szinkronizálási listájának hub adatbázis sémák |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/logs/read | Térjen vissza a szinkronizálási csoport naplók listája |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/read | Térjen vissza a szinkronizálási csoportok, vagy a megadott szinkronizálási csoport tulajdonságainak beolvasása. |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | Szinkronizáló központ adatbázis séma frissítése |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | A Szinkronizáló központ séma frissítési művelet eredményének beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | Törli a meglévő szinkronizálási tagjára. |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | A megadott szinkronizálási tag térjen vissza a szinkronizálási tagok vagy lekérdezi a tulajdonságok listáját. |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | Szinkronizálási tag séma frissítése |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | A szinkronizálás tag séma frissítési művelet eredményének beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | Térjen vissza a szinkronizálási listájának tag adatbázis sémák |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | Hoz létre a szinkronizálási tagja a megadott paramétereket, vagy a megadott szinkronizálási tag tulajdonságainak frissítéséhez. |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | Eseményindító szinkronizálási csoport szinkronizálási |
> | Műveletek | Microsoft.Sql/servers/databases/syncGroups/write | Szinkronizálási csoportot hoz létre a megadott paraméterekkel, vagy a megadott szinkronizálási csoport tulajdonságainak frissítéséhez. |
> | Műveletek | Microsoft.Sql/servers/databases/topQueries/queryText/action | A kijelölt lekérdezés Azonosítóját a Transact-SQL szöveget adja vissza |
> | Műveletek | Microsoft.Sql/servers/databases/topQueries/read | Értéket ad vissza kijelölt időszakra összesített értéket a kijelölt lekérdezésre vonatkozó futásidejű statisztikák |
> | Műveletek | Microsoft.Sql/servers/databases/topQueries/statistics/read | Értéket ad vissza kijelölt időszakra összesített értéket a kijelölt lekérdezésre vonatkozó futásidejű statisztikák |
> | Műveletek | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | Lekérdezi a folyamatban lévő műveletek átlátható adattitkosítás |
> | Műveletek | Microsoft.Sql/servers/databases/transparentDataEncryption/read | Állapot és átlátható titkosítási biztonsági szolgáltatást egy adott adatbázisnak részleteinek beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/transparentDataEncryption/write | Átlátható titkosítási állapot módosítása |
> | Műveletek | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | Frissítés Azure SQL-adatraktár adatbázisa |
> | Műveletek | Microsoft.Sql/servers/databases/usages/read | Az Azure SQL Database módjait információ lekérése |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | Távolítsa el a megadott adatbázis biztonsági réseinek értékelése |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | A biztonsági réseinek értékelése, egy adott adatbázisnak konfigurált részleteinek beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | Távolítsa el a biztonsági rés értékelése szabály alaptervet egy adott adatbázisnak |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | Egy adott adatbázisnak a biztonsági rés értékelése szabály alapterv lekérése |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | Egy adott adatbázisnak a biztonsági rés értékelése szabály alaptervet módosítása |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | Egy meglévő vizsgálati eredmény átalakítása emberi olvasható formátumba. Ha már létezik semmi nem történik, |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | A biztonsági rés adatbázis vizsgálat hajtható végre. |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | Az adatbázis biztonsági rés listája assessment vizsgálat rekordot ad vissza, vagy a vizsgálat rekord lekérése a megadott vizsgálat azonosítóját. |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | A biztonsági réseinek értékelése egy adott adatbázis módosítása |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | A biztonsági rés adatbázis vizsgálat hajtható végre. |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | Az adatbázis biztonsági rés vizsgálatának végrehajtásához hajtsa végre a művelet eredményének beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | A biztonsági réseinek értékelése, egy adott adatbázisnak konfigurált részleteinek beolvasása |
> | Műveletek | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | A biztonsági réseinek értékelése egy adott adatbázis módosítása |
> | Műveletek | Microsoft.Sql/servers/databases/write | Létrehoz egy adatbázist a megadott paraméterekkel, vagy a Tulajdonságok vagy a megadott adatbázis címkék frissítése. |
> | Műveletek | Microsoft.Sql/servers/delete | Egy meglévő kiszolgáló törlése. |
> | Műveletek | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | Egy meglévő vész-helyreállítási beállítások egy adott kiszolgáló törlése |
> | Műveletek | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | Feladatátvétel egy DisasterRecoveryConfiguration |
> | Műveletek | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | Feladatátvétel egy DisasterRecoveryConfiguration kényszerítése |
> | Műveletek | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | Lekérdezi a katasztrófa gyűjteménye, amely tartalmazza az ehhez a kiszolgálóhoz helyreállítási konfigurációk |
> | Műveletek | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | Kiszolgáló vész helyreállítási konfigurációjának módosítása |
> | Műveletek | Microsoft.Sql/servers/elasticPoolEstimates/read | Ehhez a kiszolgálóhoz már létrehozott rugalmas készlet becslések listáját adja vissza |
> | Műveletek | Microsoft.Sql/servers/elasticPoolEstimates/write | Hoz létre új rugalmas készletbecslés megadott adatbázisok listája |
> | Műveletek | Microsoft.Sql/servers/elasticPools/advisors/read | A rugalmas készlet elérhető tanácsadók listáját adja vissza |
> | Műveletek | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | Javasolt művelet a megadott advisor a rugalmas készlet listáját adja vissza |
> | Műveletek | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | A rugalmas készlet alkalmazni a javasolt művelet |
> | Műveletek | Microsoft.Sql/servers/elasticPools/advisors/write | Frissítés Automatikus végrehajtás az advisor szinten a rugalmas készlet állapotát. |
> | Műveletek | Microsoft.Sql/servers/elasticPools/databases/read | Rugalmas készletek adatbázisok listájának lekérése |
> | Műveletek | Microsoft.Sql/servers/elasticPools/delete | A meglévő rugalmas készlet törlése |
> | Műveletek | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | Tevékenységek és egy adott rugalmas adatbáziskészlet részleteinek beolvasása |
> | Műveletek | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | Tevékenységek és egy adott adatbázisnak, amely része a rugalmas adatbáziskészlet részleteinek beolvasása |
> | Műveletek | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | Térjen vissza a rugalmas adatbáziskészletek elérhető metrikák típusú |
> | Műveletek | Microsoft.Sql/servers/elasticPools/metrics/read | Térjen vissza a rugalmas adatbáziskészletek metrikák |
> | Műveletek | Microsoft.Sql/servers/elasticPools/operations/cancel/action | Visszavonja az Azure SQL rugalmas készlet függőben lévő aszinkron művelet, amely még nem fejeződött be. |
> | Műveletek | Microsoft.Sql/servers/elasticPools/operations/read | A művelet végrehajtását, a rugalmas készlet listáját adja vissza |
> | Műveletek | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | Térjen vissza a rugalmas adatbáziskészletek elérhető metrikák típusú |
> | Műveletek | Microsoft.Sql/servers/elasticPools/read | A rugalmas készlet a megadott kiszolgálón részleteinek beolvasása |
> | Műveletek | Microsoft.Sql/servers/elasticPools/skus/read | Elérhető termékváltozatok gyűjteménye lekérdezi a rugalmas készletek |
> | Műveletek | Microsoft.Sql/servers/elasticPools/write | Hozzon létre egy új vagy meglévő rugalmas készlet tulajdonságainak módosítása |
> | Műveletek | Microsoft.Sql/servers/encryptionProtector/read | Kiszolgáló titkosítási protectors listáját adja vissza, vagy tulajdonságait olvassa be a megadott kiszolgálóhoz tartozó titkosítási védőt. |
> | Műveletek | Microsoft.Sql/servers/encryptionProtector/write | Az a megadott kiszolgáló titkosítási védő tulajdonságainak frissítéséhez. |
> | Műveletek | Microsoft.Sql/servers/extendedAuditingSettings/read | A kiterjesztett kiszolgáló blob a megadott kiszolgálón konfigurált naplórendet részleteinek beolvasása |
> | Műveletek | Microsoft.Sql/servers/extendedAuditingSettings/write | A kiterjesztett kiszolgáló blob naplózását egy adott kiszolgáló módosítása |
> | Műveletek | Microsoft.Sql/servers/failoverGroups/delete | Egy meglévő feladatátvevő csoport törlése. |
> | Műveletek | Microsoft.Sql/servers/failoverGroups/failover/action | Tervezett feladatátvétel végrehajtása egy meglévő feladatátvevő csoportban. |
> | Műveletek | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | Végrehajtja a kényszerített feladatátvételi egy meglévő feladatátvevő csoportban. |
> | Műveletek | Microsoft.Sql/servers/failoverGroups/read | A lista a feladatátvétel csoportokat vagy a megadott feladatátvételi csoport tulajdonságainak beolvasása adja vissza. |
> | Műveletek | Microsoft.Sql/servers/failoverGroups/write | Feladatátvételi csoportot hoz létre a megadott paraméterekkel, vagy frissíti a Tulajdonságok vagy a megadott feladatátvételi csoport címkék. |
> | Műveletek | Microsoft.Sql/servers/firewallRules/delete | Meglévő kiszolgáló tűzfalszabály törlése. |
> | Műveletek | Microsoft.Sql/servers/firewallRules/read | A kiszolgáló tűzfal listáját szabályok vagy tulajdonságait olvassa be a megadott kiszolgálóhoz tartozó tűzfalszabály vissza. |
> | Műveletek | Microsoft.Sql/servers/firewallRules/write | Létrehoz egy tűzfalszabály létrehozása a megadott paraméterekkel, a megadott szabály tulajdonságainak frissítéséhez, vagy minden meglévő szabályok felülírása az új kiszolgáló tűzfal-szabályok. |
> | Műveletek | Microsoft.Sql/servers/import/action | Hozzon létre egy új adatbázist a kiszolgálón, és a séma és adatainak áttelepítését egy DacPac csomag telepítése |
> | Műveletek | Microsoft.Sql/servers/importExportOperationResults/read | Lekérdezi a folyamatban lévő importálási/exportálási műveleteket |
> | Műveletek | Microsoft.Sql/servers/keys/delete | Törli a meglévő kiszolgáló-kulcsot. |
> | Műveletek | Microsoft.Sql/servers/keys/read | Térjen vissza a kiszolgáló a kulcsok vagy a megadott kiszolgáló kulcs tulajdonságainak beolvasása. |
> | Műveletek | Microsoft.Sql/servers/keys/write | Létrehoz egy kulcsot a megadott paraméterekkel, vagy a Tulajdonságok vagy a megadott kiszolgálókulcs címkék frissítése. |
> | Műveletek | Microsoft.Sql/servers/networkInterfaces/read | A megadott hálózati adapter tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Sql/servers/networkInterfaces/write | A megadott paraméterekkel a hálózati illesztő létrehozása vagy frissítése. a Tulajdonságok vagy a címkék a megadott hálózati adapter |
> | Műveletek | Microsoft.Sql/servers/operationResults/read | Lekérdezi a folyamatban lévő kiszolgáló műveletei |
> | Műveletek | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | Térjen vissza a kiszolgálók esetén elérhetők metrikák típusai |
> | Műveletek | Microsoft.Sql/servers/read | A kiszolgálók vagy lekérdezi a megadott kiszolgálóhoz tartozó tulajdonságok listáját adja vissza. |
> | Műveletek | Microsoft.Sql/servers/recommendedElasticPools/databases/read | Egy adott kiszolgálóhoz ajánlott rugalmas adatbáziskészletek metrikák beolvasása |
> | Műveletek | Microsoft.Sql/servers/recommendedElasticPools/read | Ajánlott rugalmas adatbáziskészlet költségeinek csökkentése vagy historica erőforrás-használat alapján a teljesítmény javítása beolvasása |
> | Műveletek | Microsoft.Sql/servers/recoverableDatabases/read | Ez a művelet segítségével élő adatbázis vész-helyreállítási utolsó ismert helyes biztonsági mentési pontok-adatbázis helyreállításához. Olyan információkat ad vissza a legutóbbi helyes biztonsági másolatot, de az kapcsolatos doesn\u0027t ténylegesen állítsa vissza az adatbázist. |
> | Műveletek | Microsoft.Sql/servers/restorableDroppedDatabases/read | Az adatbázisok listája, amelyek el lettek dobva a megadott kiszolgálón még mindig belüli adatmegőrzési beolvasása. |
> | Műveletek | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | A kiszolgáló fenyegetések észlelése házirend írási művelet eredményének beolvasása |
> | Műveletek | Microsoft.Sql/servers/securityAlertPolicies/read | A megadott kiszolgálón konfigurált kiszolgáló fenyegetés észlelési házirend részleteinek beolvasása |
> | Műveletek | Microsoft.Sql/servers/securityAlertPolicies/write | A kiszolgáló fenyegetés szabályzat egy adott kiszolgáló módosítása |
> | Műveletek | Microsoft.Sql/servers/serviceObjectives/read | A szolgáltatási szint célkitűzései (más néven teljesítmény rétegek) a megadott kiszolgálón elérhető listájának beolvasása |
> | Műveletek | Microsoft.Sql/servers/syncAgents/delete | Törli a meglévő szinkronizálási ügynök. |
> | Műveletek | Microsoft.Sql/servers/syncAgents/generateKey/action | Szinkronizálási ügynök regisztrálás kulcs létrehozása |
> | Műveletek | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | A listájának kapcsolódó ügynök adatbázisok szinkronizálása |
> | Műveletek | Microsoft.Sql/servers/syncAgents/read | A megadott szinkronizálási ügynök térjen vissza a szinkronizálási ügynökök vagy lekérdezi a tulajdonságok listáját. |
> | Műveletek | Microsoft.Sql/servers/syncAgents/write | A megadott paraméterek hoz létre a sync-ügynök vagy a megadott szinkronizálási ügynök tulajdonságainak frissítéséhez. |
> | Műveletek | Microsoft.Sql/servers/usages/read | Térjen vissza a kiszolgáló DTU-kvótáról és aktuális DTU consuption minden adatbázis-kiszolgálón belül |
> | Műveletek | Microsoft.Sql/servers/virtualNetworkRules/delete | Egy meglévő virtuális hálózat szabály törlése |
> | Műveletek | Microsoft.Sql/servers/virtualNetworkRules/read | Térjen vissza a virtuális hálózat szabályok vagy a megadott virtuális hálózati szabály tulajdonságainak beolvasása. |
> | Műveletek | Microsoft.Sql/servers/virtualNetworkRules/write | A virtuális hálózati szabályt hoz létre a megadott paraméterekkel, vagy a Tulajdonságok vagy a megadott virtuális hálózati szabály címkék frissítése. |
> | Műveletek | Microsoft.Sql/servers/write | Kiszolgáló létrehozása a megadott paraméterekkel, vagy frissítse a Tulajdonságok vagy a címkék a megadott kiszolgáló. |
> | Műveletek | Microsoft.Sql/unregister/action | Az előfizetés a Microsoft SQL-adatbázis erőforrás-szolgáltató regisztrációjának törlése, és lehetővé teszi a Microsoft SQL-adatbázisok létrehozását. |
> | Műveletek | Microsoft.Sql/virtualClusters/read | A virtuális fürtök vagy lekérdezi a megadott virtuális fürt tulajdonságok listáját adja vissza. |
> | Műveletek | Microsoft.Sql/virtualClusters/write | Frissíti a virtuális fürt címkék. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Storage/checknameavailability/read | Ellenőrzi, hogy érvényes-e a fióknév, és nincs-e használatban. |
> | Műveletek | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | A Microsoft.Storage értesítése arról, hogy virtuális hálózat vagy alhálózat törlése van folyamatban |
> | Műveletek | Microsoft.Storage/operations/read | Az aszinkrón műveletek állapotának lekérdezése. |
> | Műveletek | Microsoft.Storage/register/action | A tárolásierőforrás-szolgáltató előfizetésének regisztrálása és a tárfiókkészítés engedélyezése. |
> | Műveletek | Microsoft.Storage/skus/read | A Microsoft.Storage által támogatott termékváltozatok listázása. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action | A blobtartalom hozzáadásának eredményét adja vissza |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Egy blob törlésének eredményét adja vissza |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Blobot vagy blobok listáját adja vissza |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Blob írásának eredményét adja vissza |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | Blobtároló visszatartásának törlése |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/containers/delete | A tároló törlésének eredményét adja vissza |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | Blobtároló módosíthatatlansági szabályzatának törlése |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | Blobtároló bővítésére vonatkozó módosíthatatlansági szabályzat |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | Blobtároló zárolására vonatkozó módosíthatatlansági szabályzat |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | Blobtároló beolvasására vonatkozó módosíthatatlansági szabályzat |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | Blobtárolón végrehajtott put műveletre vonatkozó módosíthatatlansági szabályzat |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/containers/read | Egy tárolót vagy tárolók listáját adja vissza |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | Blobtároló visszatartásának beállítása |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/containers/write | A put vagy lease blobtároló-művelet eredményének visszaadása |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításának beolvasása. |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése. |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/providers/Microsoft.Insights/metricDefinitions/read | A Microsoft Storage metrikadefinícióit tartalmazó lista beolvasása. |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/read | A Blob szolgáltatás tulajdonságait vagy a statisztikát adja vissza |
> | Műveletek | Microsoft.Storage/storageAccounts/blobServices/write | A blobszolgáltatás tulajdonságain végrehajtott Put művelet eredményének visszaadása |
> | Műveletek | Microsoft.Storage/storageAccounts/delete | Meglévő tárfiók törlése. |
> | Műveletek | Microsoft.Storage/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításának beolvasása. |
> | Műveletek | Microsoft.Storage/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése. |
> | Műveletek | Microsoft.Storage/storageAccounts/fileServices/providers/Microsoft.Insights/metricDefinitions/read | A Microsoft Storage metrikadefinícióit tartalmazó lista beolvasása. |
> | Műveletek | Microsoft.Storage/storageAccounts/listAccountSas/action | A fiók SAS-jogkivonatát adja vissza a megadott tárfiókhoz. |
> | Műveletek | Microsoft.Storage/storageAccounts/listkeys/action | A megadott tárfiók hozzáférési kulcsainak lekérése. |
> | Műveletek | Microsoft.Storage/storageAccounts/listServiceSas/action | A szolgáltatás SAS-jogkivonatát adja vissza a meghatározott tárfiókhoz. |
> | Műveletek | Microsoft.Storage/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításának beolvasása. |
> | Műveletek | Microsoft.Storage/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése. |
> | Műveletek | Microsoft.Storage/storageAccounts/providers/Microsoft.Insights/metricDefinitions/read | A Microsoft Storage metrikadefinícióit tartalmazó lista beolvasása. |
> | Műveletek | Microsoft.Storage/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése. |
> | Műveletek | Microsoft.Storage/storageAccounts/queueServices/providers/Microsoft.Insights/metricDefinitions/read | A Microsoft Storage metrikadefinícióit tartalmazó lista beolvasása. |
> | Műveletek | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Sor törlésének eredményét adja vissza |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Üzenet hozzáadásának eredményét adja vissza |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Üzenet törlésének eredményét adja vissza |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Üzenet feldolgozásának eredményét adja vissza |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Egy üzenetet ad vissza |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Üzenet írásának eredményét adja vissza |
> | Műveletek | Microsoft.Storage/storageAccounts/queueServices/queues/read | A sort vagy a sorok listáját adja vissza. |
> | Műveletek | Microsoft.Storage/storageAccounts/queueServices/queues/write | A sor írásának eredményét adja vissza |
> | Műveletek | Microsoft.Storage/storageAccounts/queueServices/read | A Queue szolgáltatás tulajdonságait vagy a statisztikát adja vissza. |
> | Műveletek | Microsoft.Storage/storageAccounts/queueServices/write | A Queue szolgáltatás tulajdonságai beállításának eredményét adja vissza |
> | Műveletek | Microsoft.Storage/storageAccounts/read | A tárfiókok listájának vagy a megadott tárfiók tulajdonságainak lekérése. |
> | Műveletek | Microsoft.Storage/storageAccounts/regeneratekey/action | A megadott tárfiók hozzáférési kulcsainak újragenerálása. |
> | Műveletek | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | Tárfiók diagnosztikai beállításainak létrehozása vagy frissítése. |
> | Műveletek | Microsoft.Storage/storageAccounts/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításának beolvasása. |
> | Műveletek | Microsoft.Storage/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításának beolvasása. |
> | Műveletek | Microsoft.Storage/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításának létrehozása vagy frissítése. |
> | Műveletek | Microsoft.Storage/storageAccounts/tableServices/providers/Microsoft.Insights/metricDefinitions/read | A Microsoft Storage metrikadefinícióit tartalmazó lista beolvasása. |
> | Műveletek | Microsoft.Storage/storageAccounts/write | Tárfiók létrehozása a megadott paraméterekkel, a tulajdonságok vagy címkék frissítése, vagy egyéni tartomány felvétele a megadott tárfiókhoz. |
> | Műveletek | Microsoft.Storage/usages/read | A megadott előfizetésben szereplő erőforrások korlátját és jelenlegi kihasználtsági értéket adja vissza |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | microsoft.storagesync/storageSyncServices/delete | A tárolási szinkronizálási szolgáltatások törlése |
> | Műveletek | microsoft.storagesync/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read | A tárolási szinkronizálási szolgáltatások elérhető metrikai meghatározások beolvasása |
> | Műveletek | microsoft.storagesync/storageSyncServices/read | Olvassa el a tárolási szinkronizálási szolgáltatások |
> | Műveletek | microsoft.storagesync/storageSyncServices/registeredServers/delete | Bármely regisztrált kiszolgáló törlése |
> | Műveletek | microsoft.storagesync/storageSyncServices/registeredServers/read | Bármely regisztrált kiszolgáló olvasása |
> | Műveletek | microsoft.storagesync/storageSyncServices/registeredServers/write | Létrehozni vagy frissíteni a regisztrált kiszolgáló |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | Felhő végpontok törlése |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | Hely api aszinkron biztonsági mentési hívások |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | Biztonsági mentés után a művelet hívása |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | Ez a művelet visszaállítás után hívható |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | Biztonsági mentés előtt. Ez a művelet hívása |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | Ez a művelet a visszaállítási előtt hívható |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | Olvassa el a felhő végpontok |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | Szívverés visszaállítása |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | Létrehozni vagy frissíteni a felhő végpontok |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/delete | A szinkronizálási csoportok törlése |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/read | Olvassa el a szinkronizálási csoportok |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | Kiszolgáló végpontok törlése |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | Olvassa el a kiszolgáló végpontok |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | Ez a művelet egy visszahívás hívása |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | Létrehozni vagy frissíteni Server végpontok |
> | Műveletek | microsoft.storagesync/storageSyncServices/syncGroups/write | A szinkronizálási csoportok létrehozása vagy frissítése |
> | Műveletek | microsoft.storagesync/storageSyncServices/write | A tárolási szinkronizálási szolgáltatások létrehozása vagy módosítása |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.StorSimple/managers/accessControlRecords/delete | A hozzáférés-vezérlési rekordokat törlése |
> | Műveletek | Microsoft.StorSimple/managers/accessControlRecords/read | Sorolja fel, vagy a hozzáférés-vezérlési rekordokat beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/accessControlRecords/write | A hozzáférés-vezérlő rekordok létrehozása vagy frissítése |
> | Műveletek | Microsoft.StorSimple/managers/alerts/read | Lekérdezi a riasztásokat vagy listája |
> | Műveletek | Microsoft.StorSimple/managers/bandwidthSettings/delete | Törli a meglévő sávszélesség-beállítások (8000 sorozat csak) |
> | Műveletek | Microsoft.StorSimple/managers/bandwidthSettings/read | A sávszélesség-beállítások (csak 8000 sorozat) |
> | Műveletek | Microsoft.StorSimple/managers/bandwidthSettings/write | Létrehoz egy új, vagy a sávszélesség-beállítások frissítése (8000 sorozat csak) |
> | Műveletek | Microsoft.StorSimple/Managers/certificates/write | A frissítés erőforrás tanúsítvány művelet frissíti az erőforrás/tárolói hitelesítő adatainak tanúsítványa. |
> | Műveletek | Microsoft.StorSimple/managers/clearAlerts/action | Törölje a jelet az Eszközkezelőben társított összes riasztást. |
> | Műveletek | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | A felhő készülék lista támogatott konfigurációk |
> | Műveletek | Microsoft.StorSimple/managers/configureDevice/action | Eszközök konfigurálása |
> | Műveletek | Microsoft.StorSimple/managers/delete | Törli az eszköz-kezelők |
> | Műveletek | Microsoft.StorSimple/Managers/delete | A tároló törlése a művelet törli a megadott Azure-erőforrás "tárolóban" típusú |
> | Műveletek | Microsoft.StorSimple/managers/devices/alertSettings/read | Sorolja fel, vagy a riasztási beállításainak beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/alertSettings/write | A riasztási beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.StorSimple/managers/devices/backupPolicies/backup/action | Igény szerinti létrehozásához manuális biztonsági mentés készítése a házirend által védett összes kötet biztonsági mentését. |
> | Műveletek | Microsoft.StorSimple/managers/devices/backupPolicies/delete | Törli a meglévő biztonsági mentési házirendek (8000 sorozat csak) |
> | Műveletek | Microsoft.StorSimple/managers/devices/backupPolicies/read | A lista a biztonsági mentési házirendek (csak 8000 sorozat) |
> | Műveletek | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | Egy meglévő ütemezés törlése |
> | Műveletek | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/read | Az ütemezések felsorolása |
> | Műveletek | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/write | Létrehoz egy új, vagy az ütemezések frissítése |
> | Műveletek | Microsoft.StorSimple/managers/devices/backupPolicies/write | Létrehoz egy új, vagy frissíti a biztonsági mentési házirendek (8000 sorozat csak) |
> | Műveletek | Microsoft.StorSimple/managers/devices/backups/delete | A biztonságimásolat-készlet törlése |
> | Műveletek | Microsoft.StorSimple/managers/devices/backups/elements/clone/action | Klónozni egy fájlmegosztás vagy kötet egy biztonsági mentési elem használatával. |
> | Műveletek | Microsoft.StorSimple/managers/devices/backups/read | Lekérdezi a biztonságimásolat-készletet vagy listája |
> | Műveletek | Microsoft.StorSimple/managers/devices/backups/restore/action | Állítsa vissza az összes kötet a biztonságimásolat-készletből. |
> | Műveletek | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | A biztonsági mentés ütemezése csoportok törlése |
> | Műveletek | Microsoft.StorSimple/managers/devices/backupScheduleGroups/read | Sorolja fel, vagy a biztonsági mentési ütemezés csoportok beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/backupScheduleGroups/write | A biztonsági mentési ütemezés csoportok létrehozása vagy frissítése |
> | Műveletek | Microsoft.StorSimple/managers/devices/chapSettings/delete | Törli a Chap-beállításokat |
> | Műveletek | Microsoft.StorSimple/managers/devices/chapSettings/read | Sorolja fel, vagy a Chap beállításainak beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/chapSettings/write | A Chap beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.StorSimple/managers/devices/deactivate/action | Egy eszköz inaktiválja. |
> | Műveletek | Microsoft.StorSimple/managers/devices/delete | Az eszközök törlése |
> | Műveletek | Microsoft.StorSimple/managers/devices/download/action | Egy eszköz letöltési frissítéseket. |
> | Műveletek | Microsoft.StorSimple/managers/devices/failover/action | Az eszköz feladatátvétele. |
> | Műveletek | Microsoft.StorSimple/managers/devices/fileservers/backup/action | Egy fájlkiszolgáló biztonsági másolatok készítéséhez. |
> | Műveletek | Microsoft.StorSimple/managers/devices/fileservers/delete | Törli a fájlkiszolgálókon |
> | Műveletek | Microsoft.StorSimple/managers/devices/fileservers/metrics/read | Sorolja fel, vagy a metrikák beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | Sorolja fel, vagy a metrikák meghatározások beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/fileservers/read | Lekérdezi a fájlkiszolgálók vagy listája |
> | Műveletek | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | Törli a megosztások |
> | Műveletek | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | Sorolja fel, vagy a metrikák beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | Sorolja fel, vagy a metrikák meghatározások beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/fileservers/shares/read | Lekérdezi a megosztásokat vagy listája |
> | Műveletek | Microsoft.StorSimple/managers/devices/fileservers/shares/write | Létrehozni vagy frissíteni a megosztásokat |
> | Műveletek | Microsoft.StorSimple/managers/devices/fileservers/write | Létrehozni vagy frissíteni a fájlkiszolgálókon |
> | Műveletek | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | Hardver összetevőcsoportok vezérlő power állapotának módosítása |
> | Műveletek | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | A hardver összetevő csoportokat |
> | Műveletek | Microsoft.StorSimple/managers/devices/install/action | Frissítések telepítése egy eszközön. |
> | Műveletek | Microsoft.StorSimple/managers/devices/installUpdates/action | Telepíti a frissítéseket az eszközökön |
> | Műveletek | Microsoft.StorSimple/managers/devices/iscsiservers/backup/action | Az iSCSI-kiszolgálók biztonsági másolatok készítéséhez. |
> | Műveletek | Microsoft.StorSimple/managers/devices/iscsiservers/delete | Törli az iSCSI-kiszolgálók |
> | Műveletek | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | A lemezek törlése |
> | Műveletek | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metrics/read | Sorolja fel, vagy a metrikák beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | Sorolja fel, vagy a metrikák meghatározások beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/iscsiservers/disks/read | Lekérdezi a lemezek vagy listája |
> | Műveletek | Microsoft.StorSimple/managers/devices/iscsiservers/disks/write | Létrehozni vagy frissíteni a lemezek |
> | Műveletek | Microsoft.StorSimple/managers/devices/iscsiservers/metrics/read | Sorolja fel, vagy a metrikák beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | Sorolja fel, vagy a metrikák meghatározások beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/iscsiservers/read | Sorolja fel, vagy az iSCSI kiszolgálók beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/iscsiservers/write | Létrehozni vagy frissíteni az iSCSI-kiszolgálók |
> | Műveletek | Microsoft.StorSimple/managers/devices/jobs/cancel/action | Egy futó feladat megszakítása |
> | Műveletek | Microsoft.StorSimple/managers/devices/jobs/read | Sorolja fel, vagy a feladat beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/listFailoverSets/action | A lista a feladatátvétel beállítja egy meglévő eszközt. |
> | Műveletek | Microsoft.StorSimple/managers/devices/listFailoverTargets/action | Az eszközök feladatátvételi céljainak |
> | Műveletek | Microsoft.StorSimple/managers/devices/metrics/read | Sorolja fel, vagy a metrikák beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/metricsDefinitions/read | Sorolja fel, vagy a metrikák meghatározások beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigration/action | Megerősíti, hogy a sikeres áttelepítéshez és véglegesítheti. |
> | Műveletek | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action | Áttelepítési megerősítése állapotának beolvasása. |
> | Műveletek | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | Az áttelepítési becslés feladat állapotának beolvasása. |
> | Műveletek | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action | Az áttelepítés állapotának beolvasása. |
> | Műveletek | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | Az áttelepítéshez forrás konfigurációk importálása |
> | Műveletek | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigration/action | Forrás-konfigurációk használatával áttelepítés indítása |
> | Műveletek | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action | Az áttelepítési folyamat időtartamának becsléséhez feladat indítása. |
> | Műveletek | Microsoft.StorSimple/managers/devices/networkSettings/read | Sorolja fel, vagy a hálózati beállításainak beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/networkSettings/write | Létrehoz egy új, vagy a hálózati beállítások frissítése |
> | Műveletek | Microsoft.StorSimple/managers/devices/publicEncryptionKey/action | Lista nyilvános titkosítási kulcsot, az Eszközkezelőben |
> | Műveletek | Microsoft.StorSimple/managers/devices/publishSupportPackage/action | Egy eszköz a Microsoft Support hibaelhárítási támogatási csomag közzététele. |
> | Műveletek | Microsoft.StorSimple/managers/devices/read | Sorolja fel, vagy az eszköz beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/scanForUpdates/action | Egy eszköz frissítések keresése. |
> | Műveletek | Microsoft.StorSimple/managers/devices/securitySettings/read | A biztonsági beállítások |
> | Műveletek | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | A távoli felügyeleti tanúsítvány eszköz szinkronizálása. |
> | Műveletek | Microsoft.StorSimple/managers/devices/securitySettings/update/action | A biztonsági beállítások frissítése. |
> | Műveletek | Microsoft.StorSimple/managers/devices/securitySettings/write | Létrehoz egy új, vagy a biztonsági beállítások frissítése |
> | Műveletek | Microsoft.StorSimple/managers/devices/sendTestAlertEmail/action | Konfigurált küldési e-mail címzetteknek teszt figyelmeztető e-mailt küldeni. |
> | Műveletek | Microsoft.StorSimple/managers/devices/timeSettings/read | Sorolja fel, vagy a idő beállításainak beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/timeSettings/write | Létrehoz egy új, vagy frissíti az idő beállítása |
> | Műveletek | Microsoft.StorSimple/managers/devices/updateSummary/read | Sorolja fel, vagy a frissítés összegzés beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/delete | Törli a meglévő Kötettárolók (8000 sorozat csak) |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/listEncryptionKeys/action | Lista titkosítási kulcsokat a Kötettárolók |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | A metrikák felsorolása |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/metricsDefinitions/read | A metrikák definíciók felsorolása |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/read | Kötettárolók listázása (csak 8000 sorozat) |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/rolloverEncryptionKey/action | Kötettárolók a helyettesítő titkosítási kulcsok |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/delete | Egy meglévő kötetek törlése |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | A metrikák felsorolása |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | A metrikák definíciók felsorolása |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/read | A kötetek felsorolása |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/write | Létrehoz egy új vagy frissít kötetek |
> | Műveletek | Microsoft.StorSimple/managers/devices/volumeContainers/write | Létrehoz egy új vagy frissít Kötettárolók (8000 sorozat csak) |
> | Műveletek | Microsoft.StorSimple/managers/devices/write | Létrehozása vagy frissítése az eszközöket |
> | Műveletek | Microsoft.StorSimple/managers/encryptionSettings/read | Sorolja fel, vagy a titkosítási beállításainak beolvasása |
> | Műveletek | Microsoft.StorSimple/Managers/extendedInformation/delete | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Műveletek | Microsoft.StorSimple/Managers/extendedInformation/read | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Műveletek | Microsoft.StorSimple/Managers/extendedInformation/write | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Műveletek | Microsoft.StorSimple/managers/getActivationKey/action | Aktiválási kulcs lekérése az Eszközkezelőben. |
> | Műveletek | Microsoft.StorSimple/managers/getEncryptionKey/action | Titkosítási kulcs beszerzése az Eszközkezelőben. |
> | Műveletek | Microsoft.StorSimple/managers/listActivationKey/action | Az aktiválási kulcsot a StorSimple eszköz Manager lekérdezi. |
> | Műveletek | Microsoft.StorSimple/managers/listPrivateEncryptionKey/action | Lekérdezi a személyes titkosítási kulcs egy StorSimple az Eszközkezelőben. |
> | Műveletek | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | Nyilvános titkosítási kulcsok egy StorSimple Device Manager listában. |
> | Műveletek | Microsoft.StorSimple/managers/metrics/read | Sorolja fel, vagy a metrikák beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/metricsDefinitions/read | Sorolja fel, vagy a metrikák meghatározások beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/provisionCloudAppliance/action | Hozzon létre egy új felhőalapú készülék. |
> | Műveletek | Microsoft.StorSimple/managers/read | Sorolja fel, vagy az eszköz kezelők beolvasása |
> | Műveletek | Microsoft.StorSimple/Managers/read | A tároló Get művelet lekérdezi az Azure-erőforrás "tárolóban" típusú képviselő objektum |
> | Műveletek | Microsoft.StorSimple/managers/regenarateRegistationCertificate/action | Az eszköz kezelők a regisztrációs tanúsítvány újbóli előállítása. |
> | Műveletek | Microsoft.StorSimple/managers/regenerateActivationKey/action | A kezelő aktiválási kulcs újragenerálása. |
> | Műveletek | Microsoft.StorSimple/managers/storageAccountCredentials/delete | A Tárfiók hitelesítő adatainak törlése |
> | Műveletek | Microsoft.StorSimple/managers/storageAccountCredentials/listAccessKey/action | A Tárfiók hitelesítő adatainak hozzáférési listázása |
> | Műveletek | Microsoft.StorSimple/managers/storageAccountCredentials/read | Sorolja fel, vagy a Tárfiók hitelesítő adatainak beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/storageAccountCredentials/write | Létrehozni vagy frissíteni a Tárfiók hitelesítő adatait |
> | Műveletek | Microsoft.StorSimple/managers/storageDomains/delete | A tárolási tartományok törlése |
> | Műveletek | Microsoft.StorSimple/managers/storageDomains/read | Sorolja fel, vagy a tárolási tartományok beolvasása |
> | Műveletek | Microsoft.StorSimple/managers/storageDomains/write | Hozzon létre vagy a Storage-tartományok frissítése |
> | Műveletek | Microsoft.StorSimple/managers/write | Létrehozni vagy frissíteni az eszköz-kezelők |
> | Műveletek | Microsoft.StorSimple/Managers/write | A tárlétrehozási művelettel vault típusú Azure-erőforrás hozható létre |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.StreamAnalytics/locations/quotas/Read | Olvasási Stream Analytics előfizetési kvóta |
> | Műveletek | Microsoft.StreamAnalytics/operations/Read | Olvasási Stream Analytics-műveletet |
> | Műveletek | Microsoft.StreamAnalytics/Register/action | Regisztrálni az előfizetést a Stream Analytics erőforrás-szolgáltató |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/Delete | A Stream Analytics-feladat törlése |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | Törölje a Stream Analytics-feladat függvény |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/functions/operationresults/Read | Olvassa el a művelet eredménye a Stream Analytics-feladat függvény |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/functions/Read | Olvasási Stream Analytics feladatcsoportot |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | A Stream Analytics feladat függvény az alapértelmezett definíció beolvasása |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | Teszt Stream Analytics feladatcsoportot |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/functions/Write | Írás a Stream Analytics-feladat függvény |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/inputs/Delete | Stream Analytics feladat bemeneti törlése |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/inputs/operationresults/Read | Olvassa el a művelet eredménye a Stream Analytics feladat |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/inputs/Read | Olvasási Stream Analytics feladat bemeneti |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/inputs/Sample/action | A minta Stream Analytics feladat bemeneti |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | Teszt Stream Analytics feladat bemeneti |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | Írás Analytics-feladat adatfolyam-bemenet |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | Olvassa el a metrikai meghatározásainak |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | Olvassa el a művelet eredménye Stream Analytics-feladat |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | Stream Analytics-feladat kimeneti törlése |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | Olvassa el a művelet eredménye a Stream Analytics Job Output |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | Olvasási Stream Analytics Feladatkiemenetét |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | Teszt Stream Analytics feladat eredménye |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | Írás Analytics-feladat kimeneti adatfolyam |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read | Olvassa el a diagnosztikai beállítást. |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write | Diagnosztikai beállításának írási. |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | Streamingjobs naplók beolvasása. |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | A streamingjobs elérhető metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/Read | Olvasási Stream Analytics-feladat |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/Start/action | A Stream Analytics-feladat indítása |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/Stop/action | A Stream Analytics-feladat leállítása |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | Törölje a Stream Analytics feladat átalakítása |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | Olvasási Stream Analytics feladat átalakítása |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | Írás a Stream Analytics feladat átalakítása |
> | Műveletek | Microsoft.StreamAnalytics/streamingjobs/Write | Írás a Stream Analytics-feladat |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Subscription/CreateSubscription/action | Azure-előfizetés létrehozása |
> | Műveletek | Microsoft.Subscription/SubscriptionDefinitions/read | A felügyeleti csoporton belül az Azure-előfizetés definíciójának lekérdezése. |
> | Műveletek | Microsoft.Subscription/SubscriptionDefinitions/write | Egy Azure-előfizetés-definíció létrehozása |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Support/register/action | Regisztrálás a támogatási erőforrás-szolgáltatóra |
> | Műveletek | Microsoft.Support/supportTickets/read | Egy támogatási jegy adatainak beolvasása (beleértve az állapotot, a súlyosságot, a kapcsolattartási adatokat és a kommunikációs elemeket), vagy az előfizetésekhez tartozó támogatási jegyek listájának beolvasása. |
> | Műveletek | Microsoft.Support/supportTickets/write | Létrehozza vagy frissíti egy támogatási jegy. Létrehozhat egy támogatási jegy műszaki, számlázási, kvóták és előfizetés-kezeléssel kapcsolatos problémákat. Súlyossága, kapcsolattartási adatai és a meglévő támogatási jegyek kommunikációs frissítheti. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | A hozzáférési házirend törlése. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | Hozzáférési házirend tulajdonságait olvassa be. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | Létrehoz egy új hozzáférési házirendet egy olyan környezetben, vagy frissíti a meglévő hozzáférési házirendek. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/delete | Törli a környezetben. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/eventsources/delete | Törli a esemény forrását. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/eventsources/eventsources/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/eventsources/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/eventsources/providers/Microsoft.Insights/metricDefinitions/read | A eventsources elérhető metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/eventsources/read | Egy eseményforrás tulajdonságait olvassa be. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/eventsources/write | Létrehoz egy új eseményforrás a következőhöz egy olyan környezetben, vagy egy meglévő eseményforrás frissíti. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/providers/Microsoft.Insights/metricDefinitions/read | A környezetek elérhető metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/read | A környezet tulajdonságait olvassa be. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | Törli a referencia-adatkészlet. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | A referencia-adatkészlet tulajdonságait olvassa be. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | Létrehoz egy új referencia-adatkészlet környezet vagy frissít egy meglévő referencia-adatkészlet. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/status/read | A környezet, a kapcsolódó műveleteket, például érkező állapotának állapotának beolvasása. |
> | Műveletek | Microsoft.TimeSeriesInsights/environments/write | Létrehoz egy új környezetben, vagy frissíti a meglévő környezetben. |
> | Műveletek | Microsoft.TimeSeriesInsights/register/action | Az idő adatsorozat Insights erőforrás-szolgáltató előfizetésének regisztrálása, és lehetővé teszi a idő adatsorozat Insights környezetek létrehozását. |

## <a name="microsoftvisualstudio"></a>Microsoft.visualstudio

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.VisualStudio/Account/Delete | Fiók törlése |
> | Műveletek | Microsoft.VisualStudio/Account/Read | Olvassa el a fiókhoz |
> | Műveletek | Microsoft.VisualStudio/Account/Write | Fiók beállítása |
> | Műveletek | Microsoft.VisualStudio/Extension/Delete | Bővítmény törlése |
> | Műveletek | Microsoft.VisualStudio/Extension/Read | Olvassa el a kiterjesztés |
> | Műveletek | Microsoft.VisualStudio/Extension/Write | Virtuáliskapcsoló-kiterjesztés beállítása |
> | Műveletek | Microsoft.VisualStudio/Project/Delete | Projekt törlése |
> | Műveletek | Microsoft.VisualStudio/Project/Read | Olvasási projekt |
> | Műveletek | Microsoft.VisualStudio/Project/Write | Beállíthatja a projekt |

## <a name="microsoftweb"></a>microsoft.web

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.Web/apimanagementaccounts/apiacls/Read | Api Management fiókok Apiacls beolvasása. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/apiacls/DELETE | Törli az Api Management fiókok API-k Apiacls. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/apiacls/Read | Api Management fiókok API-k Apiacls beolvasása. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/apiacls/Write | Frissítse az Api Management fiókok API-k Apiacls. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/connectionacls/Read | Api Management fiókok API-k Connectionacls beolvasása. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/Connections/confirmconsentcode/Action | Erősítse meg a hozzájárulási kód Api Management fiókok API-k kapcsolatok. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/Connections/connectionacls/DELETE | Törli az Api Management fiókok API-k kapcsolatok Connectionacls. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/Connections/connectionacls/Read | Api Management fiókok API-k kapcsolatok Connectionacls beolvasása. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/Connections/connectionacls/Write | Frissítse az Api Management fiókok API-k kapcsolatok Connectionacls. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/Connections/DELETE | Törli az Api Management fiókok API-k kapcsolatok. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/Connections/getconsentlinks/Action | Hozzájárulás hivatkozások Api Management fiókok API-k kapcsolatokhoz. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/Connections/listconnectionkeys/Action | Lista kapcsolat kulcsokat Api Management fiókok API-k kapcsolatok. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/Connections/listsecrets/Action | Lista titkok Api Management fiókok API-k kapcsolatok. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/Connections/Read | Lekérni az Api Management fiókok API-k kapcsolatokat. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/Connections/Write | Frissítse az Api Management fiókok API-k kapcsolatok. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/DELETE | Törli az Api Management fiókok API-k. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/localizeddefinitions/DELETE | Törli az Api Management fiókok API-k honosított definíciókat. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/localizeddefinitions/Read | Az Api Management beolvasása fiókok API-k honosított definíciókat. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/localizeddefinitions/Write | Frissítés Api Management fiókok API-k honosított definíciókat. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/Read | Az beszerzése Api fiókok API-val. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/APIs/Write | Frissítse az Api Management fiókok API-k. |
> | Műveletek | Microsoft.Web/apimanagementaccounts/connectionacls/Read | Api Management fiókok Connectionacls beolvasása. |
> | Műveletek | Microsoft.Web/availablestacks/Read | Rendelkezésre álló verem beolvasása. |
> | Műveletek | Microsoft.Web/billingmeters/Read | Számlázási mérőszámok listájának beolvasása. |
> | Műveletek | Microsoft.Web/certificates/Delete | Törölje a meglévő tanúsítványt. |
> | Műveletek | Microsoft.Web/certificates/Read | A tanúsítványok listájának lekérdezése. |
> | Műveletek | Microsoft.Web/certificates/Write | Adja hozzá az új tanúsítványt, vagy frissítsen egy meglévőt. |
> | Műveletek | Microsoft.Web/checknameavailability/Read | Ellenőrizze, hogy az erőforrásnév érhető el. |
> | Műveletek | Microsoft.Web/classicmobileservices/Read | Klasszikus mobilszolgáltatások beolvasása. |
> | Műveletek | Microsoft.Web/connectionGateways/Delete | Egy kapcsolat átjáró törlése. |
> | Műveletek | Microsoft.Web/connectionGateways/Join/Action | Egy kapcsolat átjáró csatlakozik. |
> | Műveletek | Microsoft.Web/connectionGateways/ListStatus/Action | Megadja a kapcsolat átjáró állapotát. |
> | Műveletek | Microsoft.Web/connectionGateways/Move/Action | Egy kapcsolat átjáró helyezi. |
> | Műveletek | Microsoft.Web/connectionGateways/Read | A kapcsolat átjárók listájának lekérdezése. |
> | Műveletek | Microsoft.Web/connectionGateways/Write | Létrehozza vagy frissíti a kapcsolat átjáró. |
> | Műveletek | Microsoft.Web/Connections/confirmconsentcode/Action | Kapcsolatok hozzájárulási kód megerősítése. |
> | Műveletek | Microsoft.Web/connections/Delete | Törli a kapcsolatot. |
> | Műveletek | Microsoft.Web/connections/Join/Action | A kapcsolat csatlakozik. |
> | Műveletek | Microsoft.Web/Connections/listconsentlinks/Action | Hozzájárulás hivatkozások kapcsolatokhoz. |
> | Műveletek | Microsoft.Web/connections/Move/Action | Egy kapcsolatot helyezi. |
> | Műveletek | Microsoft.Web/connections/Read | Kapcsolatok listájának beolvasása. |
> | Műveletek | Microsoft.Web/connections/Write | Létrehozza vagy frissíti a kapcsolatot. |
> | Műveletek | Microsoft.Web/customApis/Delete | Törli az egyéni API. |
> | Műveletek | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | API-definíció kiolvassa a egy WSDL. |
> | Műveletek | Microsoft.Web/customApis/Join/Action | Egy egyéni API csatlakozik. |
> | Műveletek | Microsoft.Web/customApis/listWsdlInterfaces/Action | Egy egyéni API felületek WSDL jeleníti meg. |
> | Műveletek | Microsoft.Web/customApis/Move/Action | Egy egyéni API helyezi. |
> | Műveletek | Microsoft.Web/customApis/Read | Egyéni API listájának. |
> | Műveletek | Microsoft.Web/customApis/Write | Létrehozza vagy frissíti egy egyéni API-t. |
> | Műveletek | Microsoft.Web/deploymentlocations/Read | Beolvasni a központi telepítési helyét. |
> | Műveletek | Microsoft.Web/geoRegions/Read | A földrajzi régiók között listájának lekérdezése. |
> | Műveletek | Microsoft.Web/hostingenvironments/capacities/Read | Az üzemeltetési környezetek kapacitások beolvasása. |
> | Műveletek | Microsoft.Web/hostingEnvironments/Delete | App Service-környezet törlése |
> | Műveletek | Microsoft.Web/hostingenvironments/detectors/Read | Az üzemeltetési környezetek érzékelők beolvasása. |
> | Műveletek | Microsoft.Web/hostingenvironments/Diagnostics/Read | Első üzemeltetési környezetekben diagnosztika. |
> | Műveletek | Microsoft.Web/hostingenvironments/inboundnetworkdependenciesendpoints/Read | Az összes bejövő függősége a hálózati végpont beolvasása. |
> | Műveletek | Microsoft.Web/hostingenvironments/metricdefinitions/Read | Az üzemeltetési környezetek metrikai meghatározásainak beolvasása. |
> | Műveletek | Microsoft.Web/hostingenvironments/multirolepools/metricdefinitions/Read | Az üzemeltetési környezetek többcélú készletek metrikai meghatározásainak beolvasása. |
> | Műveletek | Microsoft.Web/hostingenvironments/multirolepools/Metrics/Read | Első üzemeltetési környezetekben többcélú készletek metrikákat. |
> | Műveletek | Microsoft.Web/hostingEnvironments/multiRolePools/providers/Microsoft.Insights/metricDefinitions/Read | Az App Service-környezet MultiRole elérhető metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.Web/hostingEnvironments/multiRolePools/Read | Egy App Service Environment-környezetben egy előtér-címkészlet tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Web/hostingenvironments/multirolepools/skus/Read | Első üzemeltetési környezetekben többcélú készletek SKU. |
> | Műveletek | Microsoft.Web/hostingenvironments/multirolepools/usages/Read | Első üzemeltetési környezetekben többcélú készletek is érvényesek. |
> | Műveletek | Microsoft.Web/hostingEnvironments/multiRolePools/Write | Előtér-készlet létrehozása az App Service-környezetben, vagy egy meglévő frissítése |
> | Műveletek | Microsoft.Web/hostingenvironments/Operations/Read | Első üzemeltetési környezetekben műveletek. |
> | Műveletek | Microsoft.Web/hostingenvironments/outboundnetworkdependenciesendpoints/Read | A hálózati végpont minden kimenő függőségi beolvasása. |
> | Műveletek | microsoft.web/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | microsoft.web/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.Web/hostingEnvironments/Read | Az App Service-környezetek tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Web/hostingEnvironments/reboot/Action | Indítsa újra a egy App Service-környezetben lévő összes gépen |
> | Műveletek | Microsoft.Web/hostingenvironments/Resume/Action | Végezze el újra üzemeltetési környezetekben. |
> | Műveletek | Microsoft.Web/hostingenvironments/serverfarms/Read | Első üzemeltetési környezetekben App Service-csomagokról. |
> | Műveletek | Microsoft.Web/hostingenvironments/Sites/Read | Első környezetek webalkalmazások üzemeltetéséhez. |
> | Műveletek | Microsoft.Web/hostingenvironments/suspend/Action | Felfüggesztheti üzemeltetési környezetekben. |
> | Műveletek | Microsoft.Web/hostingenvironments/usages/Read | Első üzemeltetési környezetekben is érvényesek. |
> | Műveletek | Microsoft.Web/hostingenvironments/workerpools/metricdefinitions/Read | Első üzemeltetési környezetekben Workerpools metrika definíciókat. |
> | Műveletek | Microsoft.Web/hostingenvironments/workerpools/Metrics/Read | Első üzemeltetési környezetekben Workerpools metrikákat. |
> | Műveletek | Microsoft.Web/hostingEnvironments/workerPools/providers/Microsoft.Insights/metricDefinitions/Read | Az App Service-környezet WorkerPool elérhető metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.Web/hostingEnvironments/workerPools/Read | A Feldolgozókészleten egy App Service Environment-környezetben tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Web/hostingenvironments/workerpools/skus/Read | Első üzemeltetési környezetekben Workerpools SKU. |
> | Műveletek | Microsoft.Web/hostingenvironments/workerpools/usages/Read | Első üzemeltetési környezetekben Workerpools módjait. |
> | Műveletek | Microsoft.Web/hostingEnvironments/workerPools/Write | Hozzon létre egy új Feldolgozókészletek egy App Service Environment-környezetben, vagy egy meglévő frissítése |
> | Műveletek | Microsoft.Web/hostingEnvironments/Write | Egy új App Service Environment-környezet létrehozása vagy meglévő ütemezés frissítése |
> | Műveletek | Microsoft.Web/ishostingenvironmentnameavailable/Read | GET, ha üzemeltetési környezet neve érhető el. |
> | Műveletek | Microsoft.Web/ishostnameavailable/Read | Ellenőrizze, hogy az állomásnév érhető el. |
> | Műveletek | Microsoft.Web/isusernameavailable/Read | Ellenőrizze, hogy a felhasználónév érhető el. |
> | Műveletek | Microsoft.Web/listSitesAssignedToHostName/Read | Lekérése az állomásnév rendelt helyek nevét. |
> | Műveletek | Microsoft.Web/Locations/apioperations/Read | Helyek API műveleteinek beolvasása. |
> | Műveletek | Microsoft.Web/Locations/connectiongatewayinstallations/Read | Helyek kapcsolat átjáró telepítések beolvasása. |
> | Műveletek | Microsoft.Web/Locations/extractapidefinitionfromwsdl/Action | Api-definíció kinyerése WSDL helyeket. |
> | Műveletek | Microsoft.Web/Locations/listwsdlinterfaces/Action | Lista WSDL-felületek helyeket. |
> | Műveletek | Microsoft.Web/Locations/managedapis/apioperations/Read | Helyek felügyelt API-műveletek beolvasása. |
> | Műveletek | Microsoft.Web/locations/managedapis/Join/Action | A felügyelt API csatlakozik. |
> | Műveletek | Microsoft.Web/Locations/managedapis/Read | Helyek felügyelt API-k beolvasása. |
> | Műveletek | Microsoft.Web/Operations/Read | Műveletek beolvasása. |
> | Műveletek | Microsoft.Web/publishingusers/Read | Első közzététel számára. |
> | Műveletek | Microsoft.Web/publishingusers/Write | A frissítés közzététele a felhasználók. |
> | Műveletek | Microsoft.Web/recommendations/Read | Az előfizetések javaslatok listájának. |
> | Műveletek | Microsoft.Web/register/Action | Az előfizetés Microsoft.Web erőforrás-szolgáltató regisztrálása. |
> | Műveletek | Microsoft.Web/resourcehealthmetadata/Read | Erőforrás állapota metaadatot beszerezni. |
> | Műveletek | Microsoft.Web/serverfarms/Capabilities/Read | Az alkalmazásszolgáltatási csomagok lehetőségek elérése. |
> | Műveletek | Microsoft.Web/serverfarms/Delete | Egy meglévő App Service-csomag törlése |
> | Műveletek | Microsoft.Web/serverfarms/firstpartyapps/Settings/DELETE | Törli az App Service csomagokban első entitás alkalmazásokra vonatkozó beállítások. |
> | Műveletek | Microsoft.Web/serverfarms/firstpartyapps/Settings/Read | Az alkalmazásszolgáltatási csomagok első entitás alkalmazások beállításainak beolvasása. |
> | Műveletek | Microsoft.Web/serverfarms/firstpartyapps/Settings/Write | Frissítés App Service-csomagok első entitás alkalmazásokra vonatkozó beállítások. |
> | Műveletek | Microsoft.Web/serverfarms/hybridconnectionnamespaces/relays/DELETE | Az alkalmazásszolgáltatási csomagok hibrid kapcsolat névterek továbbítók törlése. |
> | Műveletek | Microsoft.Web/serverfarms/hybridconnectionnamespaces/relays/Read | Az alkalmazásszolgáltatási csomagok hibrid kapcsolat névterek továbbítók beolvasása. |
> | Műveletek | Microsoft.Web/serverfarms/hybridconnectionnamespaces/relays/Sites/Read | Az App Service csomagokban hibrid kapcsolat névterek továbbítók webes alkalmazások beszerzéséhez. |
> | Műveletek | Microsoft.Web/serverfarms/hybridconnectionplanlimits/Read | Az alkalmazásszolgáltatási csomagok hibrid kapcsolat terv korlátok beolvasása. |
> | Műveletek | Microsoft.Web/serverfarms/hybridconnectionrelays/Read | Az alkalmazásszolgáltatási csomagok hibrid kapcsolat továbbítók beolvasása. |
> | Műveletek | Microsoft.Web/serverfarms/metricdefinitions/Read | Az alkalmazásszolgáltatási csomagok metrikai meghatározásainak beolvasása. |
> | Műveletek | Microsoft.Web/serverfarms/Metrics/Read | Az alkalmazásszolgáltatási csomagok metrikákat kaphat. |
> | Műveletek | Microsoft.Web/serverfarms/operationresults/Read | Az alkalmazásszolgáltatási csomagok művelet eredményt. |
> | Műveletek | microsoft.web/serverfarms/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | microsoft.web/serverfarms/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.Web/serverfarms/providers/Microsoft.Insights/metricDefinitions/Read | App Service-csomag elérhető metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.Web/serverfarms/Read | Az egy App Service-csomag tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Web/serverfarms/restartSites/Action | Indítsa újra az összes webes alkalmazás az App Service-csomag |
> | Műveletek | Microsoft.Web/serverfarms/Sites/Read | Az App Service csomagokban webes alkalmazások beszerzéséhez. |
> | Műveletek | Microsoft.Web/serverfarms/skus/Read | Az alkalmazásszolgáltatási csomagok termékváltozatok beolvasása. |
> | Műveletek | Microsoft.Web/serverfarms/usages/Read | Az alkalmazásszolgáltatási csomagok módjait beolvasása. |
> | Műveletek | Microsoft.Web/serverfarms/virtualnetworkconnections/Gateways/Write | Az App Service csomagokban virtuális hálózati kapcsolatok átjárók frissítése. |
> | Műveletek | Microsoft.Web/serverfarms/virtualnetworkconnections/Read | Az alkalmazásszolgáltatási csomagok virtuális hálózati kapcsolatok lekérése. |
> | Műveletek | Microsoft.Web/serverfarms/virtualnetworkconnections/routes/DELETE | Az App Service csomagokban virtuális hálózati kapcsolatok útvonalak törlése. |
> | Műveletek | Microsoft.Web/serverfarms/virtualnetworkconnections/routes/Read | Az App Service csomagokban virtuális hálózati kapcsolatok útvonalak beolvasása. |
> | Műveletek | Microsoft.Web/serverfarms/virtualnetworkconnections/routes/Write | Az alkalmazásszolgáltatási csomagok virtuális hálózati kapcsolatok az útvonalak frissítése. |
> | Műveletek | Microsoft.Web/serverfarms/workers/reboot/Action | Indítsa újra az App Service csomagokban munkavállalók. |
> | Műveletek | Microsoft.Web/serverfarms/Write | Egy új App Service-csomag létrehozása vagy meglévő pillanatkép frissítése |
> | Műveletek | Microsoft.Web/Sites/analyzecustomhostname/Read | Elemezze az egyéni állomásnevet. |
> | Műveletek | Microsoft.Web/sites/applySlotConfig/Action | Webes alkalmazás tárolóhely konfigurációt cél tárolóhelyről az aktuális webes alkalmazás |
> | Műveletek | Microsoft.Web/sites/backup/Action | Hozzon létre egy új webes alkalmazás biztonsági mentése |
> | Műveletek | Microsoft.Web/Sites/Backup/Read | Web Apps biztonsági mentés beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Backup/Write | Web Apps másolat frissítéséhez. |
> | Műveletek | Microsoft.Web/Sites/backups/Action | Meglévő alkalmazás biztonsági másolat, amely az Azure storage blob visszaállíthatóak deríti fel. |
> | Műveletek | Microsoft.Web/Sites/backups/DELETE | Delete Web Apps Backups. |
> | Műveletek | Microsoft.Web/Sites/backups/List/Action | Lista Web Apps biztonsági mentéseket. |
> | Műveletek | Microsoft.Web/sites/backups/Read | A webalkalmazás biztonsági mentése tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Web/Sites/backups/restore/Action | Állítsa vissza a Web Apps biztonsági mentéseket. |
> | Műveletek | Microsoft.Web/Sites/backups/Write | Frissítse a Web Apps biztonsági mentéseket. |
> | Műveletek | Microsoft.Web/Sites/config/DELETE | Delete Web Apps Config. |
> | Műveletek | Microsoft.Web/sites/config/list/Action | Webes alkalmazás biztonsági bizalmas beállítások, például a hitelesítő adatokat, az alkalmazásbeállítások és az kapcsolati karakterláncok |
> | Műveletek | Microsoft.Web/sites/config/Read | A webalkalmazás konfigurációs beállításainak beolvasása |
> | Műveletek | Microsoft.Web/sites/config/Write | Webes alkalmazás konfigurációs beállításainak frissítése |
> | Műveletek | Microsoft.Web/Sites/containerlogs/Action | Tároló naplók beolvasása zip webalkalmazás. |
> | Műveletek | Microsoft.Web/Sites/continuouswebjobs/DELETE | Delete Web Apps Continuous Web Jobs. |
> | Műveletek | Microsoft.Web/Sites/continuouswebjobs/Read | Webes alkalmazások folyamatos webes feladatok beolvasása. |
> | Műveletek | Microsoft.Web/Sites/continuouswebjobs/Start/Action | Indítsa el a webes alkalmazások folyamatos webes feladatok. |
> | Műveletek | Microsoft.Web/Sites/continuouswebjobs/STOP/Action | Állítsa le a webes alkalmazások folyamatos webes feladatok. |
> | Műveletek | Microsoft.Web/sites/Delete | Egy már meglévő webalkalmazás törlése |
> | Műveletek | Microsoft.Web/Sites/Deployments/DELETE | Webes alkalmazások központi telepítéseit törli. |
> | Műveletek | Microsoft.Web/Sites/Deployments/log/Read | Webes alkalmazások központi telepítésének napló beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Deployments/Read | Webes alkalmazások központi telepítésének beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Deployments/Write | Webes alkalmazások központi telepítések frissítése. |
> | Műveletek | Microsoft.Web/Sites/detectors/Read | Web Apps érzékelők beolvasása. |
> | Műveletek | microsoft.web/sites/diagnostics/analyses/execute/Action | Webes alkalmazások diagnosztika elemzés futtatása. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/analyses/Read | Web Apps diagnosztika elemzés beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/aspnetcore/Read | Az ASP.NET Core app a Web Apps diagnosztika beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/autoheal/Read | Web Apps diagnosztika elindulásáról beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/Deployment/Read | A webes alkalmazások Diagnostics telepítése beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/Deployments/Read | Web Apps diagnosztika központi telepítések beolvasása. |
> | Műveletek | microsoft.web/sites/diagnostics/detectors/execute/Action | Futtassa a Web Apps diagnosztika érzékelő. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/detectors/Read | Web Apps diagnosztika érzékelő beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/failedrequestsperuri/Read | Webes alkalmazások diagnosztika a sikertelen kérelmek / Uri beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/frebanalysis/Read | Web Apps diagnosztika FREB elemzés beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/loganalyzer/Read | Web Apps diagnosztikai naplófájl Analyzer beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/Read | Web Apps diagnosztika kategóriák beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/runtimeavailability/Read | Web Apps diagnosztika futásidejű rendelkezésre beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/servicehealth/Read | Webes alkalmazások diagnosztika szolgáltatás állapotának beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/sitecpuanalysis/Read | Web Apps diagnosztika hely CPU elemzés beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/sitecrashes/Read | Web Apps diagnosztika hely összeomlások beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/sitelatency/Read | Web Apps diagnosztika hely késés beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/sitememoryanalysis/Read | Web Apps diagnosztika hely memória elemzés beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/siterestartsettingupdate/Read | Web Apps diagnosztika hely újraindítás beállítás frissítésének letöltése. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/siterestartuserinitiated/Read | Web Apps diagnosztika hely újraindítás felhasználó által kezdeményezett beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/siteswap/Read | Web Apps diagnosztika hely Swap beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/THREADCOUNT/Read | Web Apps diagnosztika szálak számának beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/workeravailability/Read | Web Apps diagnosztika Workeravailability beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Diagnostics/workerprocessrecycle/Read | Webes alkalmazások diagnosztika munkavégző folyamat újrahasznosítást beolvasása. |
> | Műveletek | Microsoft.Web/Sites/domainownershipidentifiers/Read | Megkapja a Web Apps tartományi tulajdonjoga azonosítókat. |
> | Műveletek | Microsoft.Web/Sites/domainownershipidentifiers/Write | Frissítse a Web Apps tartományi tulajdonjoga azonosítók. |
> | Műveletek | Microsoft.Web/Sites/Functions/Action | Webalkalmazások funkciók. |
> | Műveletek | Microsoft.Web/Sites/Functions/DELETE | Törölje a Web Apps funkciók. |
> | Műveletek | Microsoft.Web/Sites/Functions/listsecrets/Action | Lista titkok Web Apps funkciók. |
> | Műveletek | Microsoft.Web/Sites/Functions/masterkey/Read | Web Apps funkciók főkulcsos beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Functions/Read | Web Apps funkciók beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Functions/token/Read | Get Web Apps funkciók jogkivonat. |
> | Műveletek | Microsoft.Web/Sites/Functions/Write | Frissítse a Web Apps funkciók. |
> | Műveletek | Microsoft.Web/Sites/hostnamebindings/DELETE | Delete Web Apps Hostname Bindings. |
> | Műveletek | Microsoft.Web/Sites/hostnamebindings/Read | Get Web Apps Hostname Bindings. |
> | Műveletek | Microsoft.Web/Sites/hostnamebindings/Write | Update Web Apps Hostname Bindings. |
> | Műveletek | Microsoft.Web/Sites/hybridconnection/DELETE | Web Apps hibrid kapcsolat törlése. |
> | Műveletek | Microsoft.Web/Sites/hybridconnection/Read | Web Apps a hibrid kapcsolat beolvasása. |
> | Műveletek | Microsoft.Web/Sites/hybridconnection/Write | Web Apps hibrid kapcsolat frissítése. |
> | Műveletek | Microsoft.Web/Sites/hybridconnectionnamespaces/relays/DELETE | Törölje a Web Apps hibrid kapcsolat névterek továbbítók. |
> | Műveletek | Microsoft.Web/Sites/hybridconnectionnamespaces/relays/listkeys/Action | Listában kulcsok Web Apps hibrid kapcsolat névterek továbbítók. |
> | Műveletek | Microsoft.Web/Sites/hybridconnectionnamespaces/relays/Read | Web Apps hibrid kapcsolat névterek továbbítók beolvasása. |
> | Műveletek | Microsoft.Web/Sites/hybridconnectionnamespaces/relays/Write | Frissítse a Web Apps hibrid kapcsolat névterek továbbítók. |
> | Műveletek | Microsoft.Web/Sites/hybridconnectionrelays/Read | Web Apps hibrid kapcsolat továbbítók beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Instances/Deployments/DELETE | Törölje a Web Apps példányok központi telepítéseket. |
> | Műveletek | Microsoft.Web/Sites/Instances/Deployments/Read | Web Apps példányok központi telepítések beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Instances/Extensions/log/Read | Web Apps példányok bővítmények napló beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Instances/Extensions/Read | Web Apps példányok kiterjesztések beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Instances/Processes/DELETE | Törölje a Web Apps példányok folyamatokat. |
> | Műveletek | Microsoft.Web/Sites/Instances/Processes/Read | Web Apps példányok folyamatok beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Instances/Read | Web Apps-példányokat beszerezni. |
> | Műveletek | Microsoft.Web/Sites/listsyncfunctiontriggerstatus/Action | Lista szinkronizálási függvény eseményindító állapot webalkalmazások. |
> | Műveletek | Microsoft.Web/Sites/metricdefinitions/Read | Web Apps metrika meghatározások beolvasása. |
> | Műveletek | Microsoft.Web/Sites/Metrics/Read | Web Apps metrikákat kaphat. |
> | Műveletek | Microsoft.Web/Sites/metricsdefinitions/Read | Web Apps metrikák meghatározások beolvasása. |
> | Műveletek | Microsoft.Web/Sites/migratemysql/Action | MySql Web Apps alkalmazások áttelepítése. |
> | Műveletek | Microsoft.Web/Sites/migratemysql/Read | Első Web Apps alkalmazások áttelepítése a MySql. |
> | Műveletek | Microsoft.Web/Sites/networktrace/Action | Hálózati nyomkövetés webalkalmazások. |
> | Műveletek | Microsoft.Web/Sites/newpassword/Action | Newpassword Web Apps. |
> | Műveletek | Microsoft.Web/Sites/operationresults/Read | Web Apps művelet eredményt. |
> | Műveletek | Microsoft.Web/Sites/Operations/Read | Web Apps műveleteinek beolvasása. |
> | Műveletek | Microsoft.Web/Sites/perfcounters/Read | Web Apps teljesítményszámlálók beolvasása. |
> | Műveletek | Microsoft.Web/Sites/premieraddons/DELETE | Törölje a Web Apps Premier bővítményei. |
> | Műveletek | Microsoft.Web/Sites/premieraddons/Read | Web Apps Premier bővítményei beolvasása. |
> | Műveletek | Microsoft.Web/Sites/premieraddons/Write | Frissítse a Web Apps Premier bővítményei. |
> | Műveletek | Microsoft.Web/Sites/Processes/Read | Web Apps folyamatok beolvasása. |
> | Műveletek | microsoft.web/sites/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | microsoft.web/sites/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.Web/sites/providers/Microsoft.Insights/metricDefinitions/Read | Webalkalmazás elérhető metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.Web/Sites/publiccertificates/DELETE | Törli a Web Apps nyilvános tanúsítványokat. |
> | Műveletek | Microsoft.Web/Sites/publiccertificates/Read | Web Apps nyilvános tanúsítványokat le. |
> | Műveletek | Microsoft.Web/Sites/publiccertificates/Write | Web Apps nyilvános tanúsítványok frissítése. |
> | Műveletek | Microsoft.Web/sites/publish/Action | A webes alkalmazás közzététele |
> | Műveletek | Microsoft.Web/sites/publishxml/Action | Közzétételi profil xml egy webalkalmazást az beszerzése |
> | Műveletek | Microsoft.Web/Sites/publishxml/Read | Kérhető le a webes közzétételi XML. |
> | Műveletek | Microsoft.Web/sites/Read | A webes alkalmazás tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Web/Sites/recommendationhistory/Read | Webes alkalmazások javaslat előzmények beolvasása. |
> | Műveletek | Microsoft.Web/Sites/recommendations/disable/Action | Tiltsa le a Web Apps javaslatokat. |
> | Műveletek | Microsoft.Web/sites/recommendations/Read | A webalkalmazás javaslatok listája az beszerzése. |
> | Műveletek | Microsoft.Web/Sites/RECOVER/Action | Recover Web Apps. |
> | Műveletek | Microsoft.Web/sites/resetSlotConfig/Action | Alaphelyzetbe állítja a webes alkalmazás konfigurálása |
> | Műveletek | Microsoft.Web/Sites/resourcehealthmetadata/Read | Web Apps erőforrás állapotának metaadatot beszerezni. |
> | Műveletek | Microsoft.Web/sites/restart/Action | A webalkalmazás újraindítása |
> | Műveletek | Microsoft.Web/Sites/restore/Read | Web Apps visszaállítási beolvasása. |
> | Műveletek | Microsoft.Web/Sites/restore/Write | Állítsa vissza a webalkalmazásokat. |
> | Műveletek | Microsoft.Web/Sites/restoresnapshot/Action | Állítson vissza egy Web Apps pillanatképet. |
> | Műveletek | Microsoft.Web/Sites/siteextensions/DELETE | Törölje a Web Apps hely bővítmények. |
> | Műveletek | Microsoft.Web/Sites/siteextensions/Read | Web Apps helyhez kiterjesztések beolvasása. |
> | Műveletek | Microsoft.Web/Sites/siteextensions/Write | Web Apps hely bővítmények frissítése. |
> | Műveletek | Microsoft.Web/Sites/slots/analyzecustomhostname/Read | Első webes alkalmazások üzembe helyezési ponti elemezheti az egyéni állomásnevet. |
> | Műveletek | Microsoft.Web/sites/slots/applySlotConfig/Action | Webes alkalmazás tárolóhely konfiguráció cél tárolóhelyről az aktuális tárolóhelyre alkalmazásához. |
> | Műveletek | Microsoft.Web/sites/slots/backup/Action | Hozzon létre új webalkalmazás biztonsági mentése. |
> | Műveletek | Microsoft.Web/Sites/slots/Backup/Read | Webes alkalmazások üzembe helyezési ponti biztonsági mentés beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Backup/Write | Webes alkalmazások üzembe helyezési ponti másolat frissítéséhez. |
> | Műveletek | Microsoft.Web/Sites/slots/backups/DELETE | Törölje a webes alkalmazások üzembe helyezési ponti biztonsági mentéseket. |
> | Műveletek | Microsoft.Web/Sites/slots/backups/List/Action | Lista webes alkalmazások üzembe helyezési ponti biztonsági mentéseket. |
> | Műveletek | Microsoft.Web/sites/slots/backups/Read | A webalkalmazás tárolóhelyei biztonsági másolat tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Web/Sites/slots/backups/restore/Action | Állítsa vissza a webes alkalmazások üzembe helyezési ponti biztonsági mentéseket. |
> | Műveletek | Microsoft.Web/Sites/slots/config/DELETE | Webes alkalmazások üzembe helyezési ponti konfiguráció törlése. |
> | Műveletek | Microsoft.Web/sites/slots/config/list/Action | Webes alkalmazás a tárhely biztonsági bizalmas beállítások, például a hitelesítő adatokat, az alkalmazásbeállítások és az kapcsolati karakterláncok |
> | Műveletek | Microsoft.Web/sites/slots/config/Read | Webes alkalmazás a tárhely konfigurációs beállításainak beolvasása |
> | Műveletek | Microsoft.Web/sites/slots/config/Write | Webes alkalmazás a tárhely konfigurációs beállításainak frissítése |
> | Műveletek | Microsoft.Web/Sites/slots/continuouswebjobs/DELETE | Webes alkalmazások üzembe helyezési ponti folyamatos webes feladatok törlése. |
> | Műveletek | Microsoft.Web/Sites/slots/continuouswebjobs/Read | Webes alkalmazások üzembe helyezési ponti folyamatos webes feladatok beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/continuouswebjobs/Start/Action | Indítsa el a webes alkalmazások üzembe helyezési ponti folyamatos webes feladatok. |
> | Műveletek | Microsoft.Web/Sites/slots/continuouswebjobs/STOP/Action | Állítsa le a webes alkalmazások üzembe helyezési ponti folyamatos webes feladatok. |
> | Műveletek | Microsoft.Web/sites/slots/Delete | Törölje a meglévő Web App tárhelyek |
> | Műveletek | Microsoft.Web/Sites/slots/Deployments/DELETE | Törölje a webes alkalmazások üzembe helyezési ponti központi telepítések. |
> | Műveletek | Microsoft.Web/Sites/slots/Deployments/log/Read | Webes alkalmazások üzembe helyezési ponti központi telepítések napló beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Deployments/Read | Webes alkalmazások üzembe helyezési ponti központi telepítések beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Deployments/Write | Webes alkalmazások üzembe helyezési ponti központi telepítések frissítése. |
> | Műveletek | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | Webes alkalmazások üzembe helyezési ponti diagnosztika elemzés futtatása. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/analyses/Read | Webes alkalmazások üzembe helyezési ponti diagnosztika elemzés beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/aspnetcore/Read | Az ASP.NET Core app a webes alkalmazások üzembe helyezési ponti diagnosztika beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/autoheal/Read | Webes alkalmazások üzembe helyezési ponti diagnosztika elindulásáról beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/Deployment/Read | A webes alkalmazások üzembe helyezési ponti Diagnostics telepítése beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/Deployments/Read | Webes alkalmazások üzembe helyezési ponti diagnosztika központi telepítések beolvasása. |
> | Műveletek | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | Futtassa a webes alkalmazások üzembe helyezési ponti diagnosztika érzékelő. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/detectors/Read | Webes alkalmazások üzembe helyezési ponti diagnosztika érzékelő beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/frebanalysis/Read | Webes alkalmazások üzembe helyezési ponti diagnosztika FREB elemzés beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/loganalyzer/Read | Webes alkalmazások üzembe helyezési ponti diagnosztikai naplófájl Analyzer beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/Read | Webes alkalmazások üzembe helyezési ponti diagnosztika beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/runtimeavailability/Read | Webes alkalmazások üzembe helyezési ponti diagnosztika futásidejű rendelkezésre beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/servicehealth/Read | Első webes alkalmazások üzembe helyezési ponti diagnosztika szolgáltatás állapotát. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/sitecpuanalysis/Read | Webes alkalmazások üzembe helyezési ponti diagnosztika hely CPU elemzés beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/sitecrashes/Read | Webes alkalmazások üzembe helyezési ponti diagnosztika hely összeomlások beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/sitelatency/Read | Webes alkalmazások üzembe helyezési ponti diagnosztika hely késés beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/sitememoryanalysis/Read | Webes alkalmazások üzembe helyezési ponti diagnosztika hely memória elemzés beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/siterestartsettingupdate/Read | Webes alkalmazások üzembe helyezési ponti diagnosztika hely újraindítás beállítás frissítésének letöltése. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/siterestartuserinitiated/Read | Webes alkalmazások üzembe helyezési ponti diagnosztika webhely újraindítását felhasználó által kezdeményezett beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/siteswap/Read | Webes alkalmazások üzembe helyezési ponti diagnosztika hely Swap beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/THREADCOUNT/Read | Webes alkalmazások üzembe helyezési ponti diagnosztika szálak számának beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/workeravailability/Read | Webes alkalmazások üzembe helyezési ponti diagnosztika Workeravailability beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Diagnostics/workerprocessrecycle/Read | Webes alkalmazások üzembe helyezési ponti diagnosztika munkavégző folyamat újrahasznosítást beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/domainownershipidentifiers/Read | Megkapja a webes alkalmazások üzembe helyezési ponti tartomány tulajdonjoga azonosítókat. |
> | Műveletek | Microsoft.Web/Sites/slots/hostnamebindings/DELETE | Delete Web Apps Slots Hostname Bindings. |
> | Műveletek | Microsoft.Web/Sites/slots/hostnamebindings/Read | Get Web Apps Slots Hostname Bindings. |
> | Műveletek | Microsoft.Web/Sites/slots/hostnamebindings/Write | Update Web Apps Slots Hostname Bindings. |
> | Műveletek | Microsoft.Web/Sites/slots/hybridconnection/DELETE | Webes alkalmazások üzembe helyezési ponti hibrid kapcsolat törlése. |
> | Műveletek | Microsoft.Web/Sites/slots/hybridconnection/Read | Webes alkalmazások üzembe helyezési ponti hibrid kapcsolat lekérdezése. |
> | Műveletek | Microsoft.Web/Sites/slots/hybridconnection/Write | Webes alkalmazások üzembe helyezési ponti hibrid kapcsolat frissítése. |
> | Műveletek | Microsoft.Web/Sites/slots/hybridconnectionnamespaces/relays/DELETE | Webes alkalmazások üzembe helyezési ponti hibrid kapcsolat névterek továbbítók törlése. |
> | Műveletek | Microsoft.Web/Sites/slots/hybridconnectionnamespaces/relays/Write | Frissítse a webes alkalmazások üzembe helyezési ponti hibrid kapcsolat névterek továbbítók. |
> | Műveletek | Microsoft.Web/Sites/slots/hybridconnectionrelays/Read | Webes alkalmazások üzembe helyezési ponti hibrid kapcsolat továbbítók beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Instances/Deployments/Read | Webes alkalmazások üzembe helyezési ponti példányok központi telepítések beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Instances/Processes/DELETE | Törölje a webes alkalmazások üzembe helyezési ponti példányok folyamatokat. |
> | Műveletek | Microsoft.Web/Sites/slots/Instances/Processes/Read | Webes alkalmazások üzembe helyezési ponti példányok folyamatok beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Instances/Read | Webes alkalmazások üzembe helyezési ponti-példányokat beszerezni. |
> | Műveletek | Microsoft.Web/Sites/slots/metricdefinitions/Read | Webes alkalmazások üzembe helyezési ponti metrikai meghatározásainak beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/Metrics/Read | Webes alkalmazások üzembe helyezési ponti metrikákat kaphat. |
> | Műveletek | Microsoft.Web/Sites/slots/migratemysql/Read | Első webes alkalmazások üzembe helyezési ponti MySql áttelepíteni. |
> | Műveletek | Microsoft.Web/Sites/slots/networktrace/Action | Hálózati nyomkövetés Web Apps tárolóhelye. |
> | Műveletek | Microsoft.Web/Sites/slots/newpassword/Action | ÚjJelszó Web Apps tárolóhelye. |
> | Műveletek | Microsoft.Web/Sites/slots/operationresults/Read | Webes alkalmazások üzembe helyezési ponti művelet eredményekhez juthat. |
> | Műveletek | Microsoft.Web/Sites/slots/Operations/Read | Webes alkalmazások üzembe helyezési ponti műveleteinek beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/perfcounters/Read | Webes alkalmazások üzembe helyezési ponti teljesítményszámlálók beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/phplogging/Read | Webes alkalmazások üzembe helyezési ponti Phplogging beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/premieraddons/DELETE | Webes alkalmazások üzembe helyezési ponti Premier bővítményei törlése. |
> | Műveletek | Microsoft.Web/Sites/slots/premieraddons/Read | Webes alkalmazások üzembe helyezési ponti Premier bővítményei beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/premieraddons/Write | Frissítse a webes alkalmazások üzembe helyezési ponti Premier bővítményei. |
> | Műveletek | microsoft.web/sites/slots/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Műveletek | microsoft.web/sites/slots/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Műveletek | Microsoft.Web/sites/slots/providers/Microsoft.Insights/metricDefinitions/Read | A webalkalmazás elérhető metrikai meghatározások beolvasása |
> | Műveletek | Microsoft.Web/Sites/slots/publiccertificates/DELETE | Törli a webes alkalmazások üzembe helyezési ponti nyilvános tanúsítványokat. |
> | Műveletek | Microsoft.Web/Sites/slots/publiccertificates/Read | Webes alkalmazások üzembe helyezési ponti nyilvános tanúsítványokat le. |
> | Műveletek | Microsoft.Web/Sites/slots/publiccertificates/Write | Hozzon létre, vagy a Web Apps üzembe helyezési ponti nyilvános tanúsítványok frissítése. |
> | Műveletek | Microsoft.Web/sites/slots/publish/Action | Egy webes tárolóhelye közzététele |
> | Műveletek | Microsoft.Web/sites/slots/publishxml/Action | Profil xml közzétételt a webalkalmazás beolvasása |
> | Műveletek | Microsoft.Web/sites/slots/Read | A webalkalmazás üzembe helyezési pont tulajdonságainak beolvasása |
> | Műveletek | Microsoft.Web/Sites/slots/RECOVER/Action | Állítsa helyre a Web Apps tárolóhelye. |
> | Műveletek | Microsoft.Web/sites/slots/resetSlotConfig/Action | Webes alkalmazás helyezési pont konfigurációjának visszaállítása |
> | Műveletek | Microsoft.Web/Sites/slots/resourcehealthmetadata/Read | Webes alkalmazások üzembe helyezési ponti Resource Health metaadatot beszerezni. |
> | Műveletek | Microsoft.Web/sites/slots/restart/Action | Indítsa újra a webes alkalmazás tárhely |
> | Műveletek | Microsoft.Web/Sites/slots/restore/Read | Webes alkalmazások üzembe helyezési ponti visszaállítási beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/restore/Write | Állítsa vissza a Web Apps tárolóhelye. |
> | Műveletek | Microsoft.Web/Sites/slots/restoresnapshot/Action | Állítson vissza egy webes alkalmazások üzembe helyezési ponti pillanatképet. |
> | Műveletek | Microsoft.Web/Sites/slots/siteextensions/DELETE | Webes alkalmazások üzembe helyezési ponti helyhez kiterjesztések törlése. |
> | Műveletek | Microsoft.Web/Sites/slots/siteextensions/Read | Webes alkalmazások üzembe helyezési ponti helyhez kiterjesztések beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/siteextensions/Write | Webes alkalmazások üzembe helyezési ponti hely bővítmények frissítése. |
> | Műveletek | Microsoft.Web/sites/slots/slotsdiffs/Action | Konfiguráció web app és az üzembe helyezési ponti közötti különbségek beolvasása |
> | Műveletek | Microsoft.Web/sites/slots/slotsswap/Action | Felcserélni a webalkalmazás üzembe helyezési |
> | Műveletek | Microsoft.Web/Sites/slots/snapshots/Read | Webes alkalmazások üzembe helyezési ponti pillanatképek beolvasása. |
> | Műveletek | Microsoft.Web/sites/slots/sourcecontrols/Delete | Webes alkalmazás a tárhely forrás a konfigurációs beállítások törlése |
> | Műveletek | Microsoft.Web/sites/slots/sourcecontrols/Read | Webes alkalmazás a tárhely verziókezelő konfigurációs beállításainak beolvasása |
> | Műveletek | Microsoft.Web/sites/slots/sourcecontrols/Write | Webes alkalmazás a tárhely forrás a konfigurációs beállítások frissítése |
> | Műveletek | Microsoft.Web/sites/slots/start/Action | Indítsa el a webes alkalmazás tárhely |
> | Műveletek | Microsoft.Web/sites/slots/stop/Action | Állítsa le a webes alkalmazás tárhely |
> | Műveletek | Microsoft.Web/Sites/slots/Sync/Action | Szinkronizálási Web Apps tárolóhelye. |
> | Műveletek | Microsoft.Web/Sites/slots/triggeredwebjobs/DELETE | Webes alkalmazások üzembe helyezési ponti indított webjobs-feladatok törlése. |
> | Műveletek | Microsoft.Web/Sites/slots/triggeredwebjobs/Read | Webes alkalmazások üzembe helyezési ponti indított webjobs-feladatok beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/triggeredwebjobs/Run/Action | Webes alkalmazások üzembe helyezési ponti indított webjobs-feladatok futtatásához. |
> | Műveletek | Microsoft.Web/Sites/slots/usages/Read | Webes alkalmazások üzembe helyezési ponti módjait beolvasása. |
> | Műveletek | Microsoft.Web/Sites/slots/virtualnetworkconnections/DELETE | Törölje a webes alkalmazások üzembe helyezési ponti virtuális hálózati kapcsolatokat. |
> | Műveletek | Microsoft.Web/Sites/slots/virtualnetworkconnections/Gateways/Write | Frissítse a webes alkalmazások üzembe helyezési ponti virtuális hálózati kapcsolatok átjárókat. |
> | Műveletek | Microsoft.Web/Sites/slots/virtualnetworkconnections/Read | Lekérni a webes alkalmazások üzembe helyezési ponti virtuális hálózati kapcsolatokat. |
> | Műveletek | Microsoft.Web/Sites/slots/virtualnetworkconnections/Write | Frissítse a webes alkalmazások üzembe helyezési ponti virtuális hálózati kapcsolatokat. |
> | Műveletek | Microsoft.Web/Sites/slots/webjobs/Read | Webes alkalmazások üzembe helyezési ponti webjobs-feladatok beolvasása. |
> | Műveletek | Microsoft.Web/sites/slots/Write | Webes alkalmazás új tárhely létrehozása vagy meglévő pillanatkép frissítése |
> | Műveletek | Microsoft.Web/sites/slotsdiffs/Action | Konfiguráció web app és az üzembe helyezési ponti közötti különbségek beolvasása |
> | Műveletek | Microsoft.Web/sites/slotsswap/Action | Felcserélni a webalkalmazás üzembe helyezési |
> | Műveletek | Microsoft.Web/Sites/snapshots/Read | Web Apps pillanatképek beolvasása. |
> | Műveletek | Microsoft.Web/sites/sourcecontrols/Delete | Webes alkalmazás forrás a konfigurációs beállítások törlése |
> | Műveletek | Microsoft.Web/sites/sourcecontrols/Read | Webalkalmazás verziókövetésének konfigurációs beállításainak beolvasása |
> | Műveletek | Microsoft.Web/sites/sourcecontrols/Write | Webes alkalmazás forrás a konfigurációs beállítások frissítése |
> | Műveletek | Microsoft.Web/sites/start/Action | A webalkalmazás elindítása |
> | Műveletek | Microsoft.Web/sites/stop/Action | A webalkalmazás leállítása |
> | Műveletek | Microsoft.Web/Sites/Sync/Action | Sync Web Apps. |
> | Műveletek | Microsoft.Web/Sites/syncfunctiontriggers/Action | Szinkronizálási függvény eseményindítók webes alkalmazásokhoz. |
> | Műveletek | Microsoft.Web/Sites/triggeredwebjobs/DELETE | Web Apps indított webjobs-feladatok törlése. |
> | Műveletek | Microsoft.Web/Sites/triggeredwebjobs/history/Read | Webes alkalmazások indított webjobs-feladatok előzményeinek lekérése. |
> | Műveletek | Microsoft.Web/Sites/triggeredwebjobs/Read | Web Apps indított webjobs-feladatok beolvasása. |
> | Műveletek | Microsoft.Web/Sites/triggeredwebjobs/Run/Action | Web Apps indított webjobs-feladatok futtatásához. |
> | Műveletek | Microsoft.Web/Sites/usages/Read | Web Apps módjait beolvasása. |
> | Műveletek | Microsoft.Web/Sites/virtualnetworkconnections/DELETE | Törölje a Web Apps virtuális hálózati kapcsolatokat. |
> | Műveletek | Microsoft.Web/Sites/virtualnetworkconnections/Gateways/Read | Web Apps virtuális hálózati kapcsolatok átjárók beolvasása. |
> | Műveletek | Microsoft.Web/Sites/virtualnetworkconnections/Gateways/Write | Frissítse a Web Apps virtuális hálózati kapcsolatok átjárókat. |
> | Műveletek | Microsoft.Web/Sites/virtualnetworkconnections/Read | Lekérni a Web Apps virtuális hálózati kapcsolatokat. |
> | Műveletek | Microsoft.Web/Sites/virtualnetworkconnections/Write | Frissítse a Web Apps virtuális hálózati kapcsolatokat. |
> | Műveletek | Microsoft.Web/Sites/webjobs/Read | Web Apps webjobs-feladatok beolvasása. |
> | Műveletek | Microsoft.Web/sites/Write | Új webalkalmazás létrehozása vagy meglévő pillanatkép frissítése |
> | Műveletek | Microsoft.Web/skus/Read | SKU beolvasása. |
> | Műveletek | Microsoft.Web/sourcecontrols/Read | Adatforrás-vezérlők beolvasása. |
> | Műveletek | Microsoft.Web/sourcecontrols/Write | Frissítse az adatforrás-vezérlők. |
> | Műveletek | Microsoft.Web/unregister/Action | Az előfizetés Microsoft.Web erőforrás-szolgáltató regisztrációját. |
> | Műveletek | Microsoft.Web/Validate/Action | Ellenőrzése. |
> | Műveletek | Microsoft.Web/verifyhostingenvironmentvnet/Action | Ellenőrizze, hogy üzemeltetési környezet Vnet. |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | Művelettípus | Művelet | Leírás |
> | --- | --- | --- |
> | Műveletek | Microsoft.WorkloadMonitor/components/read | Olvasási műveletek erőforrások |
> | Műveletek | Microsoft.WorkloadMonitor/healthInstances/read | Olvasási műveletek erőforrások |
> | Műveletek | Microsoft.WorkloadMonitor/Operations/read | Olvasási műveletek erőforrások |
> | Műveletek | Microsoft.WorkloadMonitor/workloads/delete | A munkaterhelés erőforrás törlése |
> | Műveletek | Microsoft.WorkloadMonitor/workloads/read | A munkaterhelés erőforrás beolvasása |
> | Műveletek | Microsoft.WorkloadMonitor/workloads/write | Írja a munkaterhelés erőforrás |

## <a name="next-steps"></a>További lépések

- [Egyéni szerepkörök](custom-roles.md)
- [Beépített szerepkörök](built-in-roles.md)
