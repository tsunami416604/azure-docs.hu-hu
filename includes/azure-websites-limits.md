| Erőforrás | Ingyenes | Közös (előzetes) | Basic | Standard | Prémium (előzetes verzió)</th> |
| --- | --- | --- | --- | --- | --- |
| [Webes, mobil és API apps](https://azure.microsoft.com/services/app-service/) / [App Service-csomag](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)<sup>1</sup> |10 |100 |Korlátlan<sup>2</sup> |Korlátlan<sup>2</sup> |Korlátlan<sup>2</sup> |
| [A Logic apps](https://azure.microsoft.com/services/app-service/logic/) / [App Service-csomag](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)</a><sup>1</sup> |10 |10 |10 |20 / mag |20 / mag |
| [App Service-csomag](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) |régiónként 1 |az adott erőforráscsoport 10 |az adott erőforráscsoport 100 |az adott erőforráscsoport 100 |az adott erőforráscsoport 100 |
| Számítási sablonpéldány típusúvá |Közös |Közös |Dedikált<sup>3</sup> |Dedikált<sup>3</sup> |Dedikált<sup>3</sup></p> |
| [Kibővített](../articles/app-service/web-sites-scale.md) (max példányok) |megosztott 1 |megosztott 1 |dedikált 3<sup>3</sup> |dedikált 10<sup>3</sup> |20 dedikált (50 ASE)<sup>3,4</sup> |
| Tárolási<sup>5</sup> |1 GB-OS<sup>5</sup> |1 GB-OS<sup>5</sup> |10 GB-OS<sup>5</sup> |50 GB-OS<sup>5</sup> |500 GB<sup>4,5</sup></p> |
| CPU-idő (azaz 5 perc)<sup>6</sup> |3 perc |3 perc |Korlátlan, kell fizetnie, amennyit a standard [díjszabás](https://azure.microsoft.com/pricing/details/app-service/)</a> |Korlátlan, normál díjszabás fizetett |Korlátlan, normál díjszabás fizetett |
| CPU-idő (nap)<sup>6</sup> |60 perc |240 perc |Korlátlan, kell fizetnie, amennyit a standard [díjszabás](https://azure.microsoft.com/pricing/details/app-service/)</a> |Korlátlan, normál díjszabás fizetett |Korlátlan, normál díjszabás fizetett |
| Memória (1 óra) |1024 MB méretű App Service-csomag |Alkalmazásonkénti 1024 MB |N/A |N/A |N/A |
| Sávszélesség |165 MB |Korlátlan, [adatátviteli sebességet](https://azure.microsoft.com/pricing/details/data-transfers/) alkalmazása |Korlátlan adatforgalom díjszabás érvényes |Korlátlan adatforgalom díjszabás érvényes |Korlátlan adatforgalom díjszabás érvényes |
| Alkalmazásarchitektúra |32 bites |32 bites |32 bites vagy 64 bites |32 bites vagy 64 bites |32 bites vagy 64 bites |
| Webes szoftvercsatornák példányonként<sup>7</sup> |5 |35 |350 |Korlátlan |Korlátlan |
| Egyidejű [kapcsolatok hibakereső](../articles/app-service/web-sites-dotnet-troubleshoot-visual-studio.md) alkalmazásonként |1 |1 |1 |5 |5 |
| [az FTP/mp és az SSL azurewebsites.NET altartomány](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |X |X |X |X |X |
| [Az egyéni tartomány](../articles/app-service/app-service-web-tutorial-custom-domain.md) támogatja | |X |X |X |X |
| Az egyéni tartomány [SSL-támogatás](../articles/app-service/app-service-web-tutorial-custom-ssl.md) | | |Korlátlan SNI SSL-kapcsolatok |Korlátlan SNI SSL és 1 IP SSL-kapcsolatok tartalmazza |Korlátlan SNI SSL és 1 IP SSL-kapcsolatok tartalmazza |
| Integrált Load Balancer | |X |X |X |X |
| [Always On](../articles/app-service/web-sites-configure.md) | | |X |X |X |
| [Ütemezett biztonsági mentések](../articles/app-service/web-sites-backup.md) | | | | Ütemezett biztonsági mentések 2 óránként legfeljebb 12 biztonsági mentések naponta (manuális + ütemezett) | Ütemezett biztonsági mentések óránként legfeljebb 50 biztonsági mentések naponta (manuális + ütemezett) |
| [Automatikus méretezése](../articles/app-service/web-sites-scale.md) | | | |X |X |
| [Webjobs-feladatok](../articles/app-service/web-sites-create-web-jobs.md)<sup>8</sup> |X |X |X |X |X |
| [Azure Schedulerrel](https://azure.microsoft.com/services/scheduler/) támogatja | |X |X |X |X |
| [Végpontmonitoring](../articles/app-service/web-sites-monitor.md) | | |X |X |X |
| [Átmeneti pontokon](../articles/app-service/web-sites-staged-publishing.md) | | | |5 |20 |
| Alkalmazásonkénti egyéni tartományok</a> | |500 |500 |500 |500 |
| SLA | |<p> |99.9% |99.95%<sup>10</sup> |99.95%<sup>9</sup> |

<sup>1</sup>alkalmazások és a tárolási kvótákat is egy App Service-csomag hacsak másként nem jelezzük.  
<sup>2</sup>a tényleges száma tárolhatja, ezeknek a gépeknek az alkalmazások az alkalmazások tevékenységét, a gép példányok és a megfelelő erőforrás-használat méretétől függ.  
<sup>3</sup>dedikált példányok különböző méretű lehet. Lásd: [App Service szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/data-transfers/pricing/details/app-service/) további részleteket.  
<sup>4</sup>prémium csomagban lehetővé teszi, hogy legfeljebb 50 kiszámítja példányok (függvényében a rendelkezésre állás), és 500 GB lemezterület használata az App Service Environment-környezetek esetén, és 20 számítási példányok és 250 GB tárhely ellenkező esetben.  
<sup>5</sup>a tárolási kapacitás a teljes tartalom mérete összes alkalmazások között ugyanazt az App Service-csomag. További tárolási lehetőségek érhetők el [App Service Environment-környezet](../articles/app-service/environment/app-service-web-configure-an-app-service-environment.md#storage)  
<sup>6</sup>ezeket az erőforrásokat csak korlátozottan fizikai erőforrások dedikált példányokon (a példány mérete és a példányok száma).  
<sup>7</sup>Ha egy alkalmazás az alapszintű rétegben két példányokhoz, 350 egyidejű kapcsolatok az egyes két példánya van.  
<sup>8</sup>egyéni végrehajtható fájlok és/vagy parancsfájlok futtatása az igény szerinti, ütemezés szerint, vagy folyamatosan háttérfeladatként belül az App Service-példány. A folyamatos WebJobs-végrehajtáshoz folyamatos üzem szükséges. Az ütemezett WebJobs-végrehajtás csak az Azure Scheduler Ingyenes vagy Standard rétegével használható. Webjobs-feladatok, futtathat egy App Service-példány számára előre meghatározott korlátozva van, de mi az alkalmazás kódjának van végrehajtását megkísérlő függő gyakorlati határát.   
<sup>9</sup>használó központi telepítések több példányt az Azure Traffic Manager konfigurálva a feladatátvételi előírt 99,95 %-os SLA-t.  

