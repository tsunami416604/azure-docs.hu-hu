# Áttekintés
## [Mi a Service Fabric?](service-fabric-overview.md)
## [A mikroszolgáltatások ismertetése](service-fabric-overview-microservices.md)
## [Alkalmazáshasználati helyzetek](service-fabric-application-scenarios.md)
## [Architektúra](service-fabric-architecture.md)

# Első lépések
## A fejlesztési környezet beállítása
### [Windows](service-fabric-get-started.md)
### [Linux](service-fabric-get-started-linux.md)
### [Mac OS](service-fabric-get-started-mac.md)
## Az első alkalmazás létrehozása
### [C# Windowson](service-fabric-create-your-first-application-in-visual-studio.md)
### [Java Linuxon](service-fabric-create-your-first-linux-application-with-java.md)
### [C# Linuxon](service-fabric-create-your-first-linux-application-with-csharp.md)
## [Alkalmazások telepítése helyi fürtre](service-fabric-get-started-with-a-local-cluster.md)

# Útmutató
## Alkalmazás készítése
### Alapvető beállítások
#### [A programozási modell](service-fabric-choose-framework.md)
#### [Az alkalmazásmodell](service-fabric-application-model.md)
#### [A szolgáltatások közötti kommunikáció](service-fabric-connect-and-communicate-with-services.md)
#### [Eszközök](service-fabric-manage-application-in-visual-studio.md)
#### [Hibakeresés](service-fabric-debugging-your-application.md)
#### Megfigyelés és diagnosztika
##### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
##### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
#### [Biztonsági szabályzatok konfigurálása az alkalmazáshoz](service-fabric-application-runas-security.md)
#### [Az alkalmazás konfigurálása több környezethez](service-fabric-manage-multiple-environment-app-configuration.md)

### Reliable Services-alkalmazás
#### [Áttekintés](service-fabric-reliable-services-introduction.md)
#### Bevezetés
##### [C# Windowson](service-fabric-reliable-services-quick-start.md)
##### [Java Linuxon](service-fabric-reliable-services-quick-start-java.md)
#### [Architektúra](service-fabric-reliable-services-platform-architecture.md)
#### [Reliable Collections](service-fabric-reliable-services-reliable-collections.md)
#### [A Reliable Collections használata](service-fabric-work-with-reliable-collections.md)
#### [Konfigurálás](service-fabric-reliable-services-configuration.md)
#### [Értesítések](service-fabric-reliable-services-notifications.md)
#### [Biztonsági mentés és visszaállítás](service-fabric-reliable-services-backup-restore.md)
#### [Kommunikáció a Reliable Services szolgáltatással](service-fabric-reliable-services-communication.md)
##### [ASP.NET](service-fabric-reliable-services-communication-webapi.md)
##### [Szolgáltatás távelérésének lehetővé tétele](service-fabric-reliable-services-communication-remoting.md)
##### [WCF](service-fabric-reliable-services-communication-wcf.md)
##### [Fordított proxy](service-fabric-reverseproxy.md)
#### [Haladó szintű használat](service-fabric-reliable-services-advanced-usage.md)

### Reliable Actors-alkalmazás
#### [Áttekintés](service-fabric-reliable-actors-introduction.md)
#### Első lépések
##### [C# Windowson](service-fabric-reliable-actors-get-started.md)
##### [Java Linuxon](service-fabric-reliable-actors-get-started-java.md)
#### [Architektúra](service-fabric-reliable-actors-platform.md)
#### [Életciklus-kezelés és szemétgyűjtés](service-fabric-reliable-actors-lifecycle.md)
#### [Polimorfizmus](service-fabric-reliable-actors-polymorphism.md)
#### [Újbóli belépés](service-fabric-reliable-actors-reentrancy.md)
#### [Időzítők és emlékeztetők](service-fabric-reliable-actors-timers-reminders.md)
#### [Események](service-fabric-reliable-actors-events.md)
#### [Állapotkezelés](service-fabric-reliable-actors-state-management.md)
#### [Állapotszolgáltató konfigurálása](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)
#### [Típusszerializáció](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

### Futtatható vendégalkalmazás
#### [Futtatható vendégalkalmazás üzembe helyezése](service-fabric-deploy-existing-app.md)
#### [Több futtatható vendégalkalmazás üzembe helyezése](service-fabric-deploy-multiple-apps.md)

### Tárolóalkalmazás
#### [Áttekintés](service-fabric-containers-overview.md)
#### [Windows-tároló üzembe helyezése](service-fabric-deploy-container.md)
#### [Docker-tároló üzembe helyezése](service-fabric-deploy-container-linux.md)

## Áttelepítés a Cloud Servicesből
### [A Cloud Services és a Service Fabric összehasonlítása](service-fabric-cloud-services-migration-differences.md)
### [Migráció a Service Fabricre](service-fabric-cloud-services-migration-worker-role-stateless-service.md)

## Fürtök létrehozása és felügyelete

### Alapvető beállítások
#### [Áttekintés](service-fabric-deploy-anywhere.md)
#### [Fürt leírása](service-fabric-cluster-resource-manager-cluster-description.md)
#### [Kapacitástervezés](service-fabric-cluster-capacity.md)
#### [Fürt ábrázolása](service-fabric-visualizing-your-cluster.md)
#### [Csatlakozás biztonságos fürthöz](service-fabric-connect-to-secure-cluster.md)
#### [Biztonság](service-fabric-cluster-security.md)
#### [Vészhelyreállítás](service-fabric-disaster-recovery.md)

### Fürtök az Azure-on
#### Fürt létrehozása az Azure-ban
##### [Azure Portal](service-fabric-cluster-creation-via-portal.md)
##### [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
#### [Csomóponttípusok és virtuálisgép-méretezési csoportok](service-fabric-cluster-nodetypes.md)
#### [Fürt skálázása](service-fabric-cluster-scale-up-down.md)
#### [Fürt frissítése](service-fabric-cluster-upgrade.md)
#### [Fürt törlése](service-fabric-cluster-delete.md)
#### [Hozzáférés-vezérlés](service-fabric-cluster-security-roles.md)
#### [Fürt konfigurálása](service-fabric-cluster-fabric-settings.md)
#### [Nyilvános fürt ingyenes kipróbálása](http://aka.ms/tryservicefabric)

### Önálló fürtök
#### [Önálló fürt létrehozása](service-fabric-cluster-creation-for-windows-server.md)
#### [Fürt skálázása](service-fabric-cluster-windows-server-add-remove-nodes.md)
#### [Fürt frissítése](service-fabric-cluster-upgrade-windows-server.md)
#### [Fürt biztonságossá tétele](service-fabric-windows-cluster-x509-security.md)
#### [Hozzáférés-vezérlés](service-fabric-cluster-security-roles.md)
#### [Fürt konfigurálása](service-fabric-cluster-manifest.md)

## Fürterőforrások kezelése és összehangolása
### [A fürterőforrás-kezelő áttekintése](service-fabric-cluster-resource-manager-introduction.md)
### [A fürterőforrás-kezelő architektúrája](service-fabric-cluster-resource-manager-architecture.md)
### [Fürt leírása](service-fabric-cluster-resource-manager-cluster-description.md)
### [Az alkalmazáscsoportok áttekintése](service-fabric-cluster-resource-manager-application-groups.md)
### [A fürterőforrás-kezelő beállításainak konfigurálása](service-fabric-cluster-resource-manager-configure-services.md)
### [Erőforrás-felhasználási mérőszámok](service-fabric-cluster-resource-manager-metrics.md)
### [A szolgáltatási affinitás használata](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
### [Szolgáltatás-elhelyezési házirendek](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
### [Fürt kezelése](service-fabric-cluster-resource-manager-management-integration.md)
### [Fürt töredezettségmentesítése](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
### [Fürt terhelésének elosztása](service-fabric-cluster-resource-manager-balancing.md)
### [Szabályozás](service-fabric-cluster-resource-manager-advanced-throttling.md)
### [Szolgáltatás áthelyezése](service-fabric-cluster-resource-manager-movement-cost.md)

## Alkalmazások életciklus-kezelése
### [Áttekintés](service-fabric-application-lifecycle.md)
### [A folyamatos integráció beállítása](service-fabric-set-up-continuous-integration.md)
### Alkalmazások üzembe helyezése vagy eltávolítása
#### [PowerShell](service-fabric-deploy-remove-applications.md)
#### [Visual Studio](service-fabric-publish-app-remote-cluster.md)
### [Az alkalmazásfrissítés áttekintése](service-fabric-application-upgrade.md)
### [Az alkalmazásfrissítés konfigurálása](service-fabric-visualstudio-configure-upgrade.md)
### [Alkalmazásfrissítési paraméterek](service-fabric-application-upgrade-parameters.md)
### Alkalmazás frissítése
#### [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
#### [Visual Studio](service-fabric-application-upgrade-tutorial.md)
### [Alkalmazásfrissítési hibák elhárítása](service-fabric-application-upgrade-troubleshooting.md)
### [Adatok szerializálása az alkalmazásfrissítésekben](service-fabric-application-upgrade-data-serialization.md)
### [Alkalmazásfrissítések – haladó témakörök](service-fabric-application-upgrade-advanced.md)
### [Példa REST-alapú alkalmazás életciklusára](service-fabric-rest-based-application-lifecycle-sample.md)

## Az alkalmazás és a fürt állapotának vizsgálata
### [A Service Fabric állapotának figyelése](service-fabric-health-introduction.md)
### [Szolgáltatásállapot jelentése és ellenőrzése](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
### [Egyéni állapotjelentések hozzáadása](service-fabric-report-health.md)
### [Hibaelhárítás rendszerállapot-jelentések segítségével](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
### [Állapotjelentések megtekintése](service-fabric-view-entities-aggregated-health.md)

## Megfigyelés és diagnosztika
### A szolgáltatások helyi figyelése és diagnosztikája
#### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
#### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
### Azure Diagnostics-naplók
#### [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
#### [Linux](service-fabric-diagnostics-how-to-setup-lad.md)
### [Service Fabric-alkalmazáskövetés](service-fabric-diagnostic-how-to-use-elasticsearch.md)
### [Reliable Actors-diagnosztika](service-fabric-reliable-actors-diagnostics.md)
### [A Stateful Reliable Services diagnosztikai funkciói](service-fabric-reliable-services-diagnostics.md)
### [A helyi fürt hibaelhárítása](service-fabric-troubleshoot-local-cluster-setup.md)
### [Gyakori problémák megoldása](service-fabric-diagnostics-troubleshoot-common-scenarios.md)

## Alkalmazások skálázása
### [A Reliable Services particionálása](service-fabric-concepts-partitioning.md)
### [A szolgáltatások rendelkezésre állása](service-fabric-availability-services.md)
### [Alkalmazások skálázása](service-fabric-concepts-scalability.md)
### [Az alkalmazások kapacitásának tervezése](service-fabric-capacity-planning.md)

## Alkalmazások és szolgáltatások tesztelése
### [A hibaelemzés áttekintése](service-fabric-testability-overview.md)
### [A szolgáltatások közötti kommunikáció tesztelése](service-fabric-testability-scenarios-service-communication.md)
### Hibaszimuláció
#### [Szabályozott káosz használata](service-fabric-controlled-chaos.md)
#### [Tesztműveletek használata](service-fabric-testability-actions.md)
#### [Számítási feladatok során](service-fabric-testability-workload-tests.md)
#### [Adatvesztés meghívásával](service-fabric-use-data-loss-api.md)
#### [Tesztelési helyzetek használata](service-fabric-testability-scenarios.md)
### [Az alkalmazás terheléses tesztelése](service-fabric-vso-load-test.md)

# Referencia
## [Terminológia](service-fabric-technical-overview.md)
## [Reliable Actors](https://go.microsoft.com/fwlink/p/?linkid=833398)
## [Reliable Actors WCF](https://go.microsoft.com/fwlink/p/?linkid=833401)
## [Reliable Services](https://go.microsoft.com/fwlink/p/?linkid=833402)
## [Reliable Services WCF](https://go.microsoft.com/fwlink/p/?linkid=833403)
## [Adatok](https://go.microsoft.com/fwlink/p/?linkid=833404)
## [Adatfelületek](https://go.microsoft.com/fwlink/p/?linkid=833406)
## [Rendszer](https://go.microsoft.com/fwlink/p/?linkid=833407)
## [PowerShell](https://go.microsoft.com/fwlink/p/?linkid=833408)
## [REST API](https://go.microsoft.com/fwlink/p/?LinkID=532910)
## [Java API](https://go.microsoft.com/fwlink/p/?linkid=833410)
## [Mintakód](http://aka.ms/servicefabricsamples)

# Erőforrások
## [Képzési terv](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
## [Szolgáltatási hírek](https://azure.microsoft.com/updates/?product=service-fabric&updatetype=&platform=)
## [MSDN-fórum](https://social.msdn.microsoft.com/Forums/home?forum=AzureServiceFabric)


<!--HONumber=Nov16_HO2-->


