# [Service Fabric-dokumentáció](/azure/service-fabric)
# Áttekintés
## [Mi a Service Fabric?](service-fabric-overview.md)

# Gyors útmutatók
## [.NET-alkalmazás létrehozása](service-fabric-quickstart-dotnet.md)

# Oktatóanyagok
## .NET-alkalmazás üzembe helyezése
### [1. .NET-alkalmazás összeállítása](service-fabric-tutorial-create-dotnet-app.md)
### [2. Az alkalmazás telepítése](service-fabric-tutorial-deploy-app-to-party-cluster.md)
### [3. A CI/CD beállítása](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)

## Egy alkalmazás átemelése
### [1. Biztonságos fürt létrehozása az Azure-ban](service-fabric-tutorial-create-cluster-azure-ps.md)
### [2. .NET-alkalmazás üzembe helyezése a Docker Compose használatával](service-fabric-host-app-in-a-container.md)

# Példák
## [Kódminták](https://azure.microsoft.com/en-us/resources/samples/?service=service-fabric)
## [PowerShell](service-fabric-powershell-samples.md)
## [Azure CLI](samples-cli.md)
# Alapelvek
## [A mikroszolgáltatások ismertetése](service-fabric-overview-microservices.md)
## [Nagyméretű kép](service-fabric-content-roadmap.md)
## [Alkalmazáshasználati helyzetek](service-fabric-application-scenarios.md)
## [Minták és forgatókönyvek](service-fabric-patterns-and-scenarios.md)
## [Architektúra](service-fabric-architecture.md)
## [Terminológia](service-fabric-technical-overview.md)

## Alkalmazások és szolgáltatások összeállítása
### Támogatott programozási modellek
#### [Áttekintés](service-fabric-choose-framework.md)
#### Tárolók
##### [Áttekintés](service-fabric-containers-overview.md)
##### [Docker Compose (előzetes verzió)](service-fabric-docker-compose.md)
##### [Erőforrások szabályozása](service-fabric-resource-governance.md)
#### Reliable Services
##### [Áttekintés](service-fabric-reliable-services-introduction.md)
##### [A Reliable Services életciklusa – C#](service-fabric-reliable-services-lifecycle.md)
##### [A Reliable Services életciklusa – Java](service-fabric-reliable-services-lifecycle-java.md)
##### [Reliable Collections](service-fabric-reliable-services-reliable-collections.md)
##### [Reliable Collections – irányelvek és javaslatok](service-fabric-reliable-services-reliable-collections-guidelines.md)
##### [A Reliable Collections használata](service-fabric-work-with-reliable-collections.md)
##### [Tranzakciók és zárolások](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
##### [Reliable Concurrent Queue](service-fabric-reliable-services-reliable-concurrent-queue.md)
##### [Reliable Collections-szerializáció](service-fabric-reliable-services-reliable-collections-serialization.md)
##### [A Reliable State Manager és a Reliable Collection belső elemei](service-fabric-reliable-services-reliable-collections-internals.md)
##### [Haladó szintű használat](service-fabric-reliable-services-advanced-usage.md)

#### Reliable Actors
##### [Áttekintés](service-fabric-reliable-actors-introduction.md)
##### [Architektúra](service-fabric-reliable-actors-platform.md)
##### [Életciklus-kezelés és szemétgyűjtés](service-fabric-reliable-actors-lifecycle.md)
##### [Állapotkezelés](service-fabric-reliable-actors-state-management.md)
##### [Polimorfizmus](service-fabric-reliable-actors-polymorphism.md)
##### [Újbóli belépés](service-fabric-reliable-actors-reentrancy.md)
##### [Típusszerializáció](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

### [Az alkalmazásmodell](service-fabric-application-model.md)
### [Futtatási modell](service-fabric-hosting-model.md)

### Szolgáltatások
#### [Szolgáltatás-erőforrások](service-fabric-service-manifest-resources.md)
#### [Szolgáltatás állapota](service-fabric-concepts-state.md)
#### [Szolgáltatás particionálása](service-fabric-concepts-partitioning.md)
#### [A szolgáltatások rendelkezésre állása](service-fabric-availability-services.md)
#### A szolgáltatások közötti kommunikáció
##### [Áttekintés](service-fabric-connect-and-communicate-with-services.md)
##### [DNS-szolgáltatás](service-fabric-dnsservice.md)
##### [Fordított proxy](service-fabric-reverseproxy.md)
##### [Fordított proxy konfigurálása biztonságos kommunikációhoz](service-fabric-reverseproxy-configure-secure-communication.md)
##### [Fordítottproxy-diagnosztika](service-fabric-reverse-proxy-diagnostics.md)
### [Az alkalmazások méretezhetősége](service-fabric-concepts-scalability.md)
### [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

### [Alkalmazás kapacitásának megtervezése](service-fabric-capacity-planning.md)

## Alkalmazások kezelése
### [Áttekintés](service-fabric-application-lifecycle.md)
### [Az ImageStoreConnectionString beállítás](service-fabric-image-store-connection-string.md)
### Alkalmazás frissítése
#### [Áttekintés](service-fabric-application-upgrade.md)
#### [Konfigurálás](service-fabric-visualstudio-configure-upgrade.md)
#### [Alkalmazásfrissítési paraméterek](service-fabric-application-upgrade-parameters.md)
#### [Adatok szerializálása az alkalmazásfrissítésekben](service-fabric-application-upgrade-data-serialization.md)
#### [Alkalmazásfrissítések – haladó témakörök](service-fabric-application-upgrade-advanced.md)
### [A hibaelemzés áttekintése](service-fabric-testability-overview.md)

## Fürtök létrehozása és felügyelete
### [Áttekintés](service-fabric-deploy-anywhere.md)
### [Service Fabric a Linuxon](service-fabric-linux-overview.md)
### Tervezés és előkészítés
#### [Kapacitástervezés](service-fabric-cluster-capacity.md)
#### [Vészhelyreállítás](service-fabric-disaster-recovery.md)
### [Fürt leírása](service-fabric-cluster-resource-manager-cluster-description.md)
### [Fürtbiztonság](service-fabric-cluster-security.md)
### [Különbségek a Linux és a Windows szolgáltatásai között](service-fabric-linux-windows-differences.md)
### Fürtök az Azure-on
#### [Csomóponttípusok és virtuálisgép-méretezési csoportok](service-fabric-cluster-nodetypes.md)
#### [Fürthálózatminták](service-fabric-patterns-networking.md)
### Fürterőforrás-kezelő
#### [Áttekintés](service-fabric-cluster-resource-manager-introduction.md)
#### [Architektúra](service-fabric-cluster-resource-manager-architecture.md)
#### [Fürt leírása](service-fabric-cluster-resource-manager-cluster-description.md)
#### [Az alkalmazáscsoportok áttekintése](service-fabric-cluster-resource-manager-application-groups.md)
#### [A fürterőforrás-kezelő beállításainak konfigurálása](service-fabric-cluster-resource-manager-configure-services.md)
#### [Erőforrás-felhasználási mérőszámok](service-fabric-cluster-resource-manager-metrics.md)
#### [A szolgáltatási affinitás használata](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
#### [Szolgáltatás-elhelyezési házirendek](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
#### [Fürt kezelése](service-fabric-cluster-resource-manager-management-integration.md)
#### [Fürt töredezettségmentesítése](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
#### [Fürt terhelésének elosztása](service-fabric-cluster-resource-manager-balancing.md)
#### [Szabályozás](service-fabric-cluster-resource-manager-advanced-throttling.md)
#### [Szolgáltatás áthelyezése](service-fabric-cluster-resource-manager-movement-cost.md)

## Megfigyelés és diagnosztika
### [Áttekintés](service-fabric-diagnostics-overview.md)
### [Állapotmodell](service-fabric-health-introduction.md)
### [A Stateful Reliable Services diagnosztikai funkciói](service-fabric-reliable-services-diagnostics.md)
### [Reliable Actors-diagnosztika](service-fabric-reliable-actors-diagnostics.md)

# Útmutatók
## A fejlesztési környezet beállítása
### [Windows](service-fabric-get-started.md)
### [Linux](service-fabric-get-started-linux.md)
### [Mac OS](service-fabric-get-started-mac.md)

## Alkalmazás készítése
### Futtatható vendégszolgáltatás készítése
#### [Node.js-alkalmazás üzemeltetése Windowsban](quickstart-guest-app.md)
#### [Futtatható vendégalkalmazás üzembe helyezése](service-fabric-deploy-existing-app.md)
#### [Több futtatható vendégalkalmazás üzembe helyezése](service-fabric-deploy-multiple-apps.md)
### Tárolószolgáltatás készítése
#### [Tárolóalkalmazás létrehozása Windowshoz](service-fabric-get-started-containers.md)
#### [Tárolóalkalmazás létrehozása Linuxhoz](service-fabric-get-started-containers-linux.md)
#### [Tárolóbiztonság](service-fabric-securing-containers.md)
#### [Docker Compose (előzetes verzió)](service-fabric-docker-compose.md)
#### [Tárolók és szolgáltatások erőforrás-szabályozása](service-fabric-resource-governance.md)
#### [Kötet- és naplózási illesztők](service-fabric-containers-volume-logging-drivers.md)
#### [Tárolókon belüli szolgáltatások](service-fabric-services-inside-containers.md)
#### [Tárolóalapú hálózatkezelés módjai](service-fabric-networking-modes.md)

### Reliable Services-szolgáltatás összeállítása
#### [Áttekintés](service-fabric-reliable-services-introduction.md)
#### Alapelvek
##### [A Reliable Services életciklusa – C#](service-fabric-reliable-services-lifecycle.md)
##### [A Reliable Services életciklusa – Java](service-fabric-reliable-services-lifecycle-java.md)

#### Reliable Collections
##### [Reliable Collections](service-fabric-reliable-services-reliable-collections.md)
##### [Reliable Collections – irányelvek és javaslatok](service-fabric-reliable-services-reliable-collections-guidelines.md)
##### [A Reliable Collections használata](service-fabric-work-with-reliable-collections.md)
##### [Tranzakciók és zárolások](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
##### [Reliable Concurrent Queue](service-fabric-reliable-services-reliable-concurrent-queue.md)
##### [Reliable Collections-szerializáció](service-fabric-reliable-services-reliable-collections-serialization.md)
##### [A Reliable State Manager és a Reliable Collection belső elemei](service-fabric-reliable-services-reliable-collections-internals.md)

#### Bevezetés
##### [C# Windowson](service-fabric-reliable-services-quick-start.md)
##### [Java Linuxon](service-fabric-reliable-services-quick-start-java.md)
##### [C# alkalmazás létrehozása Linuxban](service-fabric-create-your-first-linux-application-with-csharp.md)
#### Kommunikáció a szolgáltatásokkal
##### [Kommunikáció a Reliable Services szolgáltatással](service-fabric-reliable-services-communication.md)

##### [Szolgáltatás távelérésének lehetővé tétele – C#](service-fabric-reliable-services-communication-remoting.md)
##### [Szolgáltatás távelérésének lehetővé tétele – Java](service-fabric-reliable-services-communication-remoting-java.md)
##### [WCF](service-fabric-reliable-services-communication-wcf.md)
##### [Biztonságos kommunikáció – C#](service-fabric-reliable-services-secure-communication.md)
##### [Biztonságos kommunikáció – Java](service-fabric-reliable-services-secure-communication-java.md)

#### [Konfigurálás](service-fabric-reliable-services-configuration.md)
#### [Értesítések küldése](service-fabric-reliable-services-notifications.md)
#### [Biztonsági mentés és visszaállítás](service-fabric-reliable-services-backup-restore.md)

### Reliable Actors-szolgáltatás összeállítása
#### Bevezetés
##### [C# Windowson](service-fabric-reliable-actors-get-started.md)
##### [Java Linuxon](service-fabric-reliable-actors-get-started-java.md)
#### [Értesítések küldése](service-fabric-reliable-actors-events.md)
#### [Időzítők és emlékeztetők beállítása](service-fabric-reliable-actors-timers-reminders.md)
#### [KvsActorStateProvider konfigurálása](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)
#### [Kommunikációs beállítások konfigurálása](service-fabric-reliable-actors-fabrictransportsettings.md)
#### [ReliableDictionaryActorStateProvider konfigurálása](service-fabric-reliable-actors-reliabledictionarystateprovider-configuration.md)

### [Fordított proxy konfigurálása biztonságos kommunikációhoz](service-fabric-reverseproxy-configure-secure-communication.md)

### [ASP.NET Core-szolgáltatás összeállítása](service-fabric-add-a-web-frontend.md)

### Biztonság konfigurálása
#### [Titkos alkalmazáskulcsok kezelése](service-fabric-application-secret-management.md)  
#### [Biztonsági szabályzatok konfigurálása az alkalmazáshoz](service-fabric-application-runas-security.md)

## Munka a Windows fejlesztői környezetében
### [Alkalmazások kezelése a Visual Studióban](service-fabric-manage-application-in-visual-studio.md)
### [Biztonságos kapcsolatok konfigurálása a Visual Studióban](service-fabric-visualstudio-configure-secure-connections.md)
### [Az alkalmazás konfigurálása több környezethez](service-fabric-manage-multiple-environment-app-configuration.md)
### [.NET-szolgáltatás hibakeresése a VS-ben](service-fabric-debugging-your-application.md)
### [Gyakori hibák és kivételek](service-fabric-errors-and-exceptions.md)
### [Helyi monitorozás és diagnosztika](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

## Munka a Linux fejlesztői környezetében
### [Az Eclipse beépülő modul használatának első lépései Java-fejlesztésekhez](service-fabric-get-started-eclipse.md)
### [Java szolgáltatás hibakeresése az Eclipse-ben](service-fabric-debugging-your-application-java.md)
### [Helyi monitorozás és diagnosztika](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

## Integrálás az API Management szolgáltatással
### [Áttekintés](service-fabric-api-management-overview.md)
### [Első lépések](service-fabric-api-management-quick-start.md)

## Áttelepítés a Cloud Servicesből
### [A Cloud Services és a Service Fabric összehasonlítása](service-fabric-cloud-services-migration-differences.md)
### [Migráció a Service Fabricre](service-fabric-cloud-services-migration-worker-role-stateless-service.md)
### [Ajánlott eljárások](/azure/architecture/service-fabric/migrate-from-cloud-services)

## Alkalmazások életciklus-kezelése
### [Alkalmazás becsomagolása](service-fabric-package-apps.md)

### Alkalmazások üzembe helyezése vagy eltávolítása
#### [Alkalmazások telepítése helyi fürtre](service-fabric-get-started-with-a-local-cluster.md)
#### [PowerShell](service-fabric-deploy-remove-applications.md)
#### [Azure CLI 2.0](service-fabric-application-lifecycle-azure-cli-2-0.md)
#### [Visual Studio](service-fabric-publish-app-remote-cluster.md)
#### [FabricClient API-k](service-fabric-deploy-remove-applications-fabricclient.md)

### Alkalmazások frissítése
#### [Magasabb szintű verzióra váltás a PowerShell használatával](service-fabric-application-upgrade-tutorial-powershell.md)
#### [Frissítés a Visual Studio használatával](service-fabric-application-upgrade-tutorial.md)
#### [Alkalmazásfrissítési hibák elhárítása](service-fabric-application-upgrade-troubleshooting.md)

### Alkalmazások és szolgáltatások tesztelése
#### [A szolgáltatások közötti kommunikáció tesztelése](service-fabric-testability-scenarios-service-communication.md)
#### Hibaszimuláció
##### [Szabályozott káosz használata](service-fabric-controlled-chaos.md)
##### [Tesztműveletek használata](service-fabric-testability-actions.md)
##### [Számítási feladatok során](service-fabric-testability-workload-tests.md)
##### [Tesztelési forgatókönyvek használata](service-fabric-testability-scenarios.md)
##### [A csomópontváltó API-k használata](service-fabric-node-transition-apis.md)
#### [Az alkalmazás terheléses tesztelése](service-fabric-vso-load-test.md)

### A folyamatos integráció beállítása
#### [A folyamatos integráció beállítása a VSTS-sel](service-fabric-set-up-continuous-integration.md)
#### [Linuxos Java-alkalmazás üzembe helyezése a Jenkins használatával](service-fabric-cicd-your-linux-java-application-with-jenkins.md)

## Fürtök létrehozása és felügyelete
### Fürtök az Azure-on
#### Létrehozás
##### [Az első saját fürt létrehozása az Azure-on](service-fabric-get-started-azure-cluster.md)
##### [Azure Portal](service-fabric-cluster-creation-via-portal.md)
##### [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
#### Méretezés
##### [Manuálisan](service-fabric-cluster-scale-up-down.md)
##### [Programozott módon](service-fabric-cluster-programmatic-scaling.md)
#### [Frissítés](service-fabric-cluster-upgrade.md)
#### [Hozzáférés-vezérlés beállítása](service-fabric-cluster-security-roles.md)
#### [Konfigurálás](service-fabric-cluster-fabric-settings.md)
#### [Fürttanúsítványok kezelése](service-fabric-cluster-security-update-certs-azure.md)
#### [Törlés](service-fabric-cluster-delete.md)

### Önálló fürtök
#### [Tervezés és felkészülés az üzembe helyezésre](service-fabric-cluster-standalone-deployment-preparation.md)
#### Létrehozás
##### [Az első saját önálló fürt létrehozása](service-fabric-get-started-standalone-cluster.md)
##### [Helyszíni létrehozás](service-fabric-cluster-creation-for-windows-server.md)
##### [Biztonságossá tétel tanúsítványokkal](service-fabric-windows-cluster-x509-security.md)  
##### [Biztonságossá tétel a Windows-rendszerbiztonság használatával](service-fabric-windows-cluster-windows-security.md)
##### [A különálló csomag tartalma](service-fabric-cluster-standalone-package-contents.md)
#### [Méretezés](service-fabric-cluster-windows-server-add-remove-nodes.md)
#### [Hozzáférés-vezérlés beállítása](service-fabric-cluster-security-roles.md)
#### [Konfigurálás](service-fabric-cluster-manifest.md)
#### [Frissítés](service-fabric-cluster-upgrade-windows-server.md)

### [Fürt ábrázolása](service-fabric-visualizing-your-cluster.md)
### [Csatlakozás biztonságos fürthöz](service-fabric-connect-to-secure-cluster.md)

### [Fürt kezelése az XPlat CLI használatával](service-fabric-azure-cli.md)
### [Fürt kezelése az Azure CLI-2.0-parancsokkal](service-fabric-azure-cli-2-0.md)
### [Fürtcsomópontok javítása](service-fabric-patch-orchestration-application.md)

### Fürterőforrások kezelése és összehangolása
#### [A fürterőforrás-kezelő áttekintése](service-fabric-cluster-resource-manager-introduction.md)
#### [A fürterőforrás-kezelő architektúrája](service-fabric-cluster-resource-manager-architecture.md)
#### [Fürt leírása](service-fabric-cluster-resource-manager-cluster-description.md)
#### [Az alkalmazáscsoportok áttekintése](service-fabric-cluster-resource-manager-application-groups.md)
#### [A fürterőforrás-kezelő beállításainak konfigurálása](service-fabric-cluster-resource-manager-configure-services.md)
#### [Erőforrás-felhasználási mérőszámok](service-fabric-cluster-resource-manager-metrics.md)
#### [A szolgáltatási affinitás használata](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
#### [Szolgáltatás-elhelyezési házirendek](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
#### [Fürt kezelése](service-fabric-cluster-resource-manager-management-integration.md)
#### [Fürt töredezettségmentesítése](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
#### [Fürt terhelésének elosztása](service-fabric-cluster-resource-manager-balancing.md)
#### [Szabályozás](service-fabric-cluster-resource-manager-advanced-throttling.md)
#### [Szolgáltatás áthelyezése](service-fabric-cluster-resource-manager-movement-cost.md)

## Megfigyelés és diagnosztika
### [Alkalmazások figyelése és diagnosztikája](service-fabric-diagnostics-overview.md)
### Események létrehozása
#### [Platformszintű események létrehozása](service-fabric-diagnostics-event-generation-infra.md)
##### [Műveleti csatorna](service-fabric-diagnostics-event-generation-operational.md)
##### [A Reliable Services eseményei](service-fabric-reliable-services-diagnostics.md)
##### [A Reliable Actors eseményei](service-fabric-reliable-actors-diagnostics.md)
##### [Teljesítmény-mérőszámok](service-fabric-diagnostics-event-generation-perf.md)
#### [Alkalmazásszintű események létrehozása](service-fabric-diagnostics-event-generation-app.md)
### Az alkalmazás és a fürt állapotának vizsgálata
#### [A Service Fabric állapotának figyelése](service-fabric-health-introduction.md)
#### [Szolgáltatásállapot jelentése és ellenőrzése](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
#### [Egyéni állapotjelentések hozzáadása](service-fabric-report-health.md)
#### [Hibaelhárítás rendszerállapot-jelentések segítségével](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
#### [Állapotjelentések megtekintése](service-fabric-view-entities-aggregated-health.md)
#### [Windows Server-tárolók figyelése](service-fabric-diagnostics-containers-windowsserver.md)
### Események összesítése
#### [Események összesítése az EventFlow segítségével](service-fabric-diagnostics-event-aggregation-eventflow.md)
#### Események összesítése az Azure Diagnostics segítségével
##### [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
##### [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
### Események elemzése
#### [Események elemzése az Application Insights segítségével](service-fabric-diagnostics-event-analysis-appinsights.md)
#### [Események elemzése az OMS szolgáltatással](service-fabric-diagnostics-event-analysis-oms.md)
### [A helyi fürt hibaelhárítása](service-fabric-troubleshoot-local-cluster-setup.md)

# Referencia
## [PowerShell (Azure)](/powershell/module/azurerm.servicefabric/)
## [PowerShell](/powershell/module/servicefabric/?view=azureservicefabricps)
## [Azure CLI](/cli/azure/sf)
## [Java API](/java/api/overview/azure/servicefabric)
## [.NET](/dotnet/api/overview/azure/service-fabric?view=azure-dotnet)
## [REST](/rest/api/servicefabric)

# Erőforrások
## [Azure-ütemterv](https://azure.microsoft.com/roadmap/)
## [Gyakori kérdések](service-fabric-common-questions.md)
## [Képzési terv](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
## [MSDN-fórum](https://social.msdn.microsoft.com/Forums/home?forum=AzureServiceFabric)
## [Díjszabás](https://azure.microsoft.com/pricing/details/service-fabric/)
## [Díjkalkulátor](https://azure.microsoft.com/pricing/calculator/)
## [Mintakód](http://aka.ms/servicefabricsamples)
## [Támogatási lehetőségek](service-fabric-support.md)
## [Szolgáltatási hírek](https://azure.microsoft.com/updates/?product=service-fabric)
## [Videók](https://azure.microsoft.com/documentation/videos/index/?services=service-fabric)
