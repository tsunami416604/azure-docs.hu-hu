| Erőforrás | Ingyenes | Közös (előzetes) | Alapszintű | Standard | Prémium </th> |
| --- | --- | --- | --- | --- | --- |
| [Webes, mobil-, és API apps](https://azure.microsoft.com/services/app-service/) kiszolgálónként [App Service-csomag](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)<sup>1</sup> |10 |100 |Korlátlan számú<sup>2</sup> |Korlátlan számú<sup>2</sup> |Korlátlan számú<sup>2</sup> |
| [A Logic apps](https://azure.microsoft.com/services/app-service/logic/) kiszolgálónként [App Service-csomag](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)</a><sup>1</sup> |10 |10 |10 |20 magonként |20 magonként |
| [App Service-csomag](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) |1 és régiónként |az adott erőforráscsoport esetében 10 |az adott erőforráscsoport esetében 100 |az adott erőforráscsoport esetében 100 |az adott erőforráscsoport esetében 100 |
| Számítási példány típusa |Közös |Közös |Dedikált<sup>3</sup> |Dedikált<sup>3</sup> |Dedikált<sup>3</sup></p> |
| [Horizontális Felskálázás](../articles/app-service/web-sites-scale.md) (példányok max.) |a megosztott 1 |a megosztott 1 |3 dedikált<sup>3</sup> |dedikált 10<sup>3</sup> |20 dedikált (50 az ASE környezetben)<sup>3,4</sup> |
| Storage<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |500 GB-OS<sup>4,5</sup></p> |
| CPU-idő (5 perc)<sup>6</sup> |3 perc |3 perc |Azért kell fizetnie, a standard szintű, korlátlan [díjszabása](https://azure.microsoft.com/pricing/details/app-service/)</a> |Korlátlan, a normál díjszabásnak megfelelően felszámítjuk használatalapú |Korlátlan, a normál díjszabásnak megfelelően felszámítjuk használatalapú |
| Processzoridő (nap)<sup>6</sup> |60 perc |240 perc |Azért kell fizetnie, a standard szintű, korlátlan [díjszabása](https://azure.microsoft.com/pricing/details/app-service/)</a> |Korlátlan, a normál díjszabásnak megfelelően felszámítjuk használatalapú |Korlátlan, a normál díjszabásnak megfelelően felszámítjuk használatalapú |
| Memória (1 óra) |App Service-csomag 1024 MB |Az alkalmazásonkénti 1024 MB |– |N/A |– |
| Bandwidth |165 MB |Korlátlan, [adatátviteli díjak](https://azure.microsoft.com/pricing/details/data-transfers/) alkalmazása |Korlátlan, az adatátviteli díjak érvényesek |Korlátlan, az adatátviteli díjak érvényesek |Korlátlan, az adatátviteli díjak érvényesek |
| Alkalmazásarchitektúra |32 bites |32 bites |32 bites vagy 64 bites |32 bites vagy 64 bites |32 bites vagy 64 bites |
| Webes szoftvercsatornák példányonként<sup>7</sup> |5 |35 |350 |Korlátlan |Korlátlan |
| Egyidejű [ladicího programu kapcsolatok](../articles/app-service/web-sites-dotnet-troubleshoot-visual-studio.md) alkalmazásonként |1 |1 |1 |5 |5 |
| [azurewebsites.net subdomain with FTP/S and SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |X |X |X |X |X |
| [Egyéni tartomány](../articles/app-service/app-service-web-tutorial-custom-domain.md) támogatása | |X |X |X |X |
| Egyéni tartomány [SSL-támogatás](../articles/app-service/app-service-web-tutorial-custom-ssl.md) | | |Korlátlan számú SNI SSL-kapcsolatok |Korlátlan számú SNI SSL és 1 IP SSL-kapcsolatot tartalmaz |Korlátlan számú SNI SSL és 1 IP SSL-kapcsolatot tartalmaz |
| Integrált Load Balancer | |X |X |X |X |
| [Always On](../articles/app-service/web-sites-configure.md) | | |X |X |X |
| [Ütemezett biztonsági mentések](../articles/app-service/web-sites-backup.md) | | | | Ütemezett biztonsági mentések 2 óránként legfeljebb 12 biztonsági mentések naponta (manuális + ütemezett) | Ütemezett biztonsági mentések óránként legfeljebb 50 biztonsági mentések naponta (manuális + ütemezett) |
| [Automatikus méretezés](../articles/app-service/web-sites-scale.md) | | | |X |X |
| [WebJobs](../articles/app-service/web-sites-create-web-jobs.md)<sup>8</sup> |X |X |X |X |X |
| [Az Azure Scheduler](https://azure.microsoft.com/services/scheduler/) támogatása | |X |X |X |X |
| [Végpontmonitoring](../articles/app-service/web-sites-monitor.md) | | |X |X |X |
| [Átmeneti pontok szolgálnak](../articles/app-service/web-sites-staged-publishing.md) | | | |5 |20 |
| Egyéni tartományok alkalmazásonként</a> | |500 |500 |500 |500 |
| SLA | |<p> |99.9% |99.95%<sup>10</sup> |99.95%<sup>9</sup> |

<sup>1</sup>alkalmazások és a tárolási kvótákat van egy App Service-csomag hacsak másként nem jelezzük.  
<sup>2</sup>alkalmazásokat, amelyek az ezeken a gépeken üzemeltetheti tényleges számát a tevékenység az alkalmazások, a gép példány és a megfelelő erőforrás-használat méretétől függ.  
<sup>3</sup>dedikált példányok különböző méretű lehet. Lásd: [App Service díjszabását](https://azure.microsoft.com/pricing/details/app-service/) további részletekért.  
<sup>4</sup>prémium szint lehetővé teszi, hogy akár 50 számítási példány (a készlet erejéig) és 500 GB méretű lemezterület is App Service Environment-környezetek használatakor, és 20 számítási példány és 250 GB tárhely ellenkező esetben.  
<sup>5</sup>az App Service-csomag a megadott tárolási kapacitása az összes alkalmazása tekintetében a teljes tartalom mérete. További tárolási lehetőségek érhetők el a [App Service Environment-környezet](../articles/app-service/environment/app-service-web-configure-an-app-service-environment.md#storage)  
<sup>6</sup>ezek előrébb által (a példány mérete és példányok) dedikált példányokon fizikai erőforrásokat.  
<sup>7</sup>Ha egy alkalmazás két példányok alapszintű csomag, 350 egyidejű kapcsolatok az egyes a két példánnyal rendelkezik.  
<sup>8</sup>egyéni végrehajtható fájlok és/vagy parancsprogramok futtatása igény szerint, ütemezve vagy folyamatosan, az App Service háttérfeladatként. A folyamatos WebJobs-végrehajtáshoz folyamatos üzem szükséges. Az ütemezett WebJobs-végrehajtás csak az Azure Scheduler Ingyenes vagy Standard rétegével használható. Webjobs-feladatok, amely képes futni az App Service-példányok száma nem előre definiált korlátozott, de gyakorlati korlátai, mi az alkalmazáskód próbál végezni függenek.   
<sup>9</sup>üzemelő példánya, amely az Azure Traffic Managerrel konfigurálva több példány üzemeltetéséhez feladatátvételhez megadott 99,95 %-os SLA-t.  

