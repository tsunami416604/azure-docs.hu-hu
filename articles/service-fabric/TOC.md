# [Service Fabric-dokumentáció](/azure/service-fabric)
# Áttekintés
## [Mi a Service Fabric?](service-fabric-overview.md)

# Gyors útmutatók
## [.NET-alkalmazás létrehozása](service-fabric-quickstart-dotnet.md)
## [Linux-alapú tárolóalkalmazás üzembe helyezése](service-fabric-quickstart-containers-linux.md)
## [Windows-alapú tárolóalkalmazás üzembe helyezése](service-fabric-quickstart-containers.md)
## Java-gyorsútmutatók
### [Spring Boot-alkalmazás üzembe helyezése](service-fabric-quickstart-java-spring-boot.md)
### [Reliable Services-alkalmazás üzembe helyezése](service-fabric-quickstart-java-reliable-services.md)

# Oktatóanyagok
## .NET-alkalmazás üzembe helyezése
### [1. .NET-alkalmazás összeállítása](service-fabric-tutorial-create-dotnet-app.md)
### [2. Az alkalmazás telepítése](service-fabric-tutorial-deploy-app-to-party-cluster.md)
### [3. A CI/CD beállítása](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
### [4. Monitorozás és diagnosztika](service-fabric-tutorial-monitoring-aspnet.md)

## Meglévő .NET-alkalmazás tárolóba helyezése
### [1 – .NET-alkalmazás üzembe helyezése a Docker Compose használatával](service-fabric-host-app-in-a-container.md)
### [2 – A tároló figyelése](service-fabric-tutorial-monitoring-wincontainers.md)

## Linux rendszerű tárolóalkalmazás létrehozása
### [1 – Tárolórendszerképek létrehozása](service-fabric-tutorial-create-container-images.md)
### [2 – Csomagolás és tárolók üzembe helyezése](service-fabric-tutorial-package-containers.md)
### [3 – Feladatátadás és skálázás](service-fabric-tutorial-containers-failover.md)

## Fürt létrehozása és felügyelete
### 1 – Fürt létrehozása az Azure-ban
#### [1a – Windows rendszerű fürt létrehozása](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
#### [1b – Linux rendszerű fürt létrehozása](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
### [2 – A fürt átméretezése](service-fabric-tutorial-scale-cluster.md)
### [3 – A fürt futtatókörnyezetének frissítése](service-fabric-tutorial-upgrade-cluster.md)
### [4 – Az API Management üzembe helyezése a Service Fabrickel](service-fabric-tutorial-deploy-api-management.md)

# Példák
## [Kódminták](https://azure.microsoft.com/resources/samples/?service=service-fabric)
## [Azure PowerShell](service-fabric-powershell-samples.md)
## [Service Fabric parancssori felület](samples-cli.md)

# Alapelvek
## [A mikroszolgáltatások ismertetése](service-fabric-overview-microservices.md)
## [Nagyméretű kép](service-fabric-content-roadmap.md)
## [Alkalmazáshasználati helyzetek](service-fabric-application-scenarios.md)
## [Minták és forgatókönyvek](service-fabric-patterns-and-scenarios.md)
## [Architektúra](service-fabric-architecture.md)
## [Terminológia](service-fabric-technical-overview.md)

## [Támogatott programozási modellek](service-fabric-choose-framework.md)
### [Tárolók](service-fabric-containers-overview.md)
#### [Docker Compose (előzetes verzió)](service-fabric-docker-compose.md)
#### [Erőforrások szabályozása](service-fabric-resource-governance.md)
### [Reliable Services](service-fabric-reliable-services-introduction.md)
#### [A Reliable Services életciklusa – C#](service-fabric-reliable-services-lifecycle.md)
#### [A Reliable Services életciklusa – Java](service-fabric-reliable-services-lifecycle-java.md)
#### [Reliable Collections](service-fabric-reliable-services-reliable-collections.md)
#### [Reliable Collections – irányelvek és javaslatok](service-fabric-reliable-services-reliable-collections-guidelines.md)
#### [A Reliable Collections használata](service-fabric-work-with-reliable-collections.md)
#### [Tranzakciók és zárolások](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
#### [Reliable Concurrent Queue](service-fabric-reliable-services-reliable-concurrent-queue.md)
#### [Reliable Collections-szerializáció](service-fabric-reliable-services-reliable-collections-serialization.md)
#### [A Reliable State Manager és a Reliable Collection belső elemei](service-fabric-reliable-services-reliable-collections-internals.md)
#### [Haladó szintű használat](service-fabric-reliable-services-advanced-usage.md)

### [Reliable Actors](service-fabric-reliable-actors-introduction.md)
#### [Architektúra](service-fabric-reliable-actors-platform.md)
#### [Életciklus-kezelés és szemétgyűjtés](service-fabric-reliable-actors-lifecycle.md)
#### [Állapotkezelés](service-fabric-reliable-actors-state-management.md)
#### [Polimorfizmus](service-fabric-reliable-actors-polymorphism.md)
#### [Újbóli belépés](service-fabric-reliable-actors-reentrancy.md)
#### [Típusszerializáció](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

## Alkalmazások és szolgáltatások
### [Az alkalmazásmodell](service-fabric-application-model.md)
### [Alkalmazás- és szolgáltatásjegyzékek](service-fabric-application-and-service-manifests.md)
### [Futtatási modell](service-fabric-hosting-model.md)

### [Szolgáltatás állapota](service-fabric-concepts-state.md)
### [Szolgáltatás particionálása](service-fabric-concepts-partitioning.md)
### [Az alkalmazások méretezhetősége](service-fabric-concepts-scalability.md)
### [A szolgáltatások rendelkezésre állása](service-fabric-availability-services.md)
### [Replika és példány életciklusa](service-fabric-concepts-replica-lifecycle.md)
### [Újrakonfigurálás](service-fabric-concepts-reconfiguration.md)

### [A szolgáltatások közötti kommunikáció](service-fabric-connect-and-communicate-with-services.md)
#### [DNS-szolgáltatás](service-fabric-dnsservice.md)
#### [Fordított proxy](service-fabric-reverseproxy.md)
#### [Fordított proxy konfigurálása biztonságos kommunikációhoz](service-fabric-reverseproxy-configure-secure-communication.md)
#### [Fordítottproxy-diagnosztika](service-fabric-reverse-proxy-diagnostics.md)
#### [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

### [Alkalmazás-életciklus](service-fabric-application-lifecycle.md)
#### [Alkalmazásfrissítés](service-fabric-application-upgrade.md)
##### [Konfigurálás](service-fabric-visualstudio-configure-upgrade.md)
##### [Alkalmazásfrissítési paraméterek](service-fabric-application-upgrade-parameters.md)
##### [Adatok szerializálása az alkalmazásfrissítésekben](service-fabric-application-upgrade-data-serialization.md)
##### [Alkalmazásfrissítések – haladó témakörök](service-fabric-application-upgrade-advanced.md)
#### [Alkalmazások tesztelése hibaelemzéssel](service-fabric-testability-overview.md)
#### [Az ImageStoreConnectionString beállítás](service-fabric-image-store-connection-string.md)

### [Szolgáltatás-erőforrások](service-fabric-service-manifest-resources.md)

## [Fürtök](service-fabric-deploy-anywhere.md)
### [Fürtbiztonság](service-fabric-cluster-security.md)
### [Különbségek a Linux és a Windows szolgáltatásai között](service-fabric-linux-windows-differences.md)
### Fürtök az Azure-on
#### [Csomóponttípusok és virtuálisgép-méretezési csoportok](service-fabric-cluster-nodetypes.md)
#### [Fürthálózatminták](service-fabric-patterns-networking.md)
### [Fürterőforrás-kezelő](service-fabric-cluster-resource-manager-introduction.md)
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

## Monitorozás és diagnosztika
### [Alkalmazások figyelése és diagnosztikája](service-fabric-diagnostics-overview.md)
### Események létrehozása
#### [Platformszintű események létrehozása](service-fabric-diagnostics-event-generation-infra.md)
##### [Műveleti csatorna](service-fabric-diagnostics-event-generation-operational.md)
##### [A Reliable Services eseményei](service-fabric-reliable-services-diagnostics.md)
##### [A Reliable Actors eseményei](service-fabric-reliable-actors-diagnostics.md)
##### [Teljesítmény-mérőszámok](service-fabric-diagnostics-event-generation-perf.md)
##### [Megfigyelő szolgáltatás távelérésének lehetővé tétele](service-fabric-reliable-serviceremoting-diagnostics.md)
#### [Alkalmazásszintű események létrehozása](service-fabric-diagnostics-event-generation-app.md)
### Az alkalmazás és a fürt állapotának vizsgálata
#### [A Service Fabric állapotának figyelése](service-fabric-health-introduction.md)
#### [Szolgáltatásállapot jelentése és ellenőrzése](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
#### [Egyéni állapotjelentések hozzáadása](service-fabric-report-health.md)
#### [Hibaelhárítás rendszerállapot-jelentések segítségével](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
#### [Állapotjelentések megtekintése](service-fabric-view-entities-aggregated-health.md)
### Események összesítése
#### [Események összesítése az EventFlow segítségével](service-fabric-diagnostics-event-aggregation-eventflow.md)
#### Események összesítése az Azure Diagnostics segítségével
##### [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
##### [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
### Események elemzése
#### [Események elemzése az Application Insights segítségével](service-fabric-diagnostics-event-analysis-appinsights.md)
#### [Események elemzése az OMS szolgáltatással](service-fabric-diagnostics-event-analysis-oms.md)
### [A helyi fürt hibaelhárítása](service-fabric-troubleshoot-local-cluster-setup.md)

## [Integráció az API Management szolgáltatással](service-fabric-api-management-overview.md)

# Útmutatók
## A fejlesztési környezet beállítása
### [Windows](service-fabric-get-started.md)
### [Linux](service-fabric-get-started-linux.md)
### [Mac OS](service-fabric-get-started-mac.md)
### [A Service Fabric parancssori felület beállítása](service-fabric-cli.md)

## Tervezés és előkészítés
### [Fürtkapacitás tervezése](service-fabric-cluster-capacity.md)
### [Önálló fürtkonfiguráció tervezése](service-fabric-cluster-standalone-deployment-preparation.md)
### [Felkészülés vészhelyreállításra](service-fabric-disaster-recovery.md)
### [Alkalmazás kapacitásának megtervezése](service-fabric-capacity-planning.md)

## Első alkalmazás létrehozása...
### [C# alkalmazás a Visual Studióban](service-fabric-create-your-first-application-in-visual-studio.md)
### [Windows-alapú tárolóalkalmazás](service-fabric-get-started-containers.md)
### [Linux-alapú tárolóalkalmazás](service-fabric-get-started-containers-linux.md)
### [C# Reliable Services-alkalmazás Windows rendszeren](service-fabric-reliable-services-quick-start.md)
### [Java Reliable Services-alkalmazás Linux rendszeren](service-fabric-reliable-services-quick-start-java.md)
### [C# Reliable Services-alkalmazás Linux rendszeren](service-fabric-create-your-first-linux-application-with-csharp.md)
### [C# Reliable Actors-alkalmazás Windows rendszeren](service-fabric-reliable-actors-get-started.md)
### [Java Reliable Actors-alkalmazás Linux rendszeren](service-fabric-create-your-first-linux-application-with-java.md)
### [Futtatható vendégalkalmazás Windows rendszeren](quickstart-guest-app.md)
### [Önálló fürt](service-fabric-get-started-standalone-cluster.md)

## Alkalmazás készítése

### Futtatható vendégszolgáltatás készítése
#### [Futtatható vendégalkalmazás üzembe helyezése](service-fabric-deploy-existing-app.md)
#### [Több futtatható vendégalkalmazás üzembe helyezése](service-fabric-deploy-multiple-apps.md)
### Tárolószolgáltatás készítése
#### [Tárolóbiztonság](service-fabric-securing-containers.md)
#### [Docker Compose (előzetes verzió)](service-fabric-docker-compose.md)
#### [Tárolók és szolgáltatások erőforrás-szabályozása](service-fabric-resource-governance.md)
#### [Kötet- és naplózási illesztők](service-fabric-containers-volume-logging-drivers.md)
#### [Tárolókon belüli szolgáltatások](service-fabric-services-inside-containers.md)
#### [Tárolóalapú hálózatkezelés módjai](service-fabric-networking-modes.md)

### Reliable Services-szolgáltatás összeállítása
#### Reliable Collections
##### [A Reliable Collections használata](service-fabric-work-with-reliable-collections.md)
##### [Reliable Concurrent Queue](service-fabric-reliable-services-reliable-concurrent-queue.md)
##### [Reliable Collections-szerializáció](service-fabric-reliable-services-reliable-collections-serialization.md)

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
#### [Értesítések küldése](service-fabric-reliable-actors-events.md)
#### [Időzítők és emlékeztetők beállítása](service-fabric-reliable-actors-timers-reminders.md)
#### [KvsActorStateProvider konfigurálása](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)
#### [Kommunikációs beállítások konfigurálása](service-fabric-reliable-actors-fabrictransportsettings.md)
#### [ReliableDictionaryActorStateProvider konfigurálása](service-fabric-reliable-actors-reliabledictionarystateprovider-configuration.md)

### [Régi Java-alkalmazás migrálása a Maven támogatására](service-fabric-migrate-old-javaapp-to-use-maven.md)

### [Fordított proxy konfigurálása biztonságos kommunikációhoz](service-fabric-reverseproxy-configure-secure-communication.md)

### [ASP.NET Core-szolgáltatás összeállítása](service-fabric-add-a-web-frontend.md)

### Biztonság konfigurálása
#### [Titkos alkalmazáskulcsok kezelése](service-fabric-application-secret-management.md)  
#### [Biztonsági szabályzatok konfigurálása az alkalmazáshoz](service-fabric-application-runas-security.md)

## Munka a Windows/VS fejlesztői környezetében
### [Alkalmazások kezelése a Visual Studióban](service-fabric-manage-application-in-visual-studio.md)
### [Biztonságos kapcsolatok konfigurálása a Visual Studióban](service-fabric-visualstudio-configure-secure-connections.md)
### [Az alkalmazás konfigurálása több környezethez](service-fabric-manage-multiple-environment-app-configuration.md)
### [.NET-szolgáltatás hibakeresése a VS-ben](service-fabric-debugging-your-application.md)
### [Gyakori hibák és kivételek](service-fabric-errors-and-exceptions.md)
### [Helyi monitorozás és diagnosztika](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
### [Linux-fürt beállítás Windowson](service-fabric-local-linux-cluster-windows.md)

## Munka a Linux/Eclipse fejlesztői környezetében
### [Az Eclipse beépülő modul használatának első lépései Java-fejlesztésekhez](service-fabric-get-started-eclipse.md)
### [Java szolgáltatás hibakeresése az Eclipse-ben](service-fabric-debugging-your-application-java.md)
### [Helyi monitorozás és diagnosztika](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

## Áttelepítés a Cloud Servicesből
### [A Cloud Services és a Service Fabric összehasonlítása](service-fabric-cloud-services-migration-differences.md)
### [Migráció a Service Fabricre](service-fabric-cloud-services-migration-worker-role-stateless-service.md)
### [Ajánlott eljárások](/azure/architecture/service-fabric/migrate-from-cloud-services)

## Alkalmazások életciklus-kezelése
### [Alkalmazás becsomagolása](service-fabric-package-apps.md)

### Alkalmazások üzembe helyezése vagy eltávolítása
#### [Alkalmazások telepítése helyi fürtre](service-fabric-get-started-with-a-local-cluster.md)
#### [Azure Resource Manager](service-fabric-application-arm-resource.md)
#### [Azure PowerShell](service-fabric-deploy-remove-applications.md)
#### [Service Fabric parancssori felület](service-fabric-application-lifecycle-sfctl.md)
#### [FabricClient API-k](service-fabric-deploy-remove-applications-fabricclient.md)

### Alkalmazások frissítése
#### [Frissítés az Azure PowerShell-lel](service-fabric-application-upgrade-tutorial-powershell.md)
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

### A folyamatos integráció beállítása
#### [A folyamatos integráció beállítása a VSTS-sel](service-fabric-set-up-continuous-integration.md)
#### [Linuxos alkalmazások üzembe helyezése a Jenkins használatával](service-fabric-cicd-your-linux-applications-with-jenkins.md)

## Fürtök létrehozása és felügyelete
### Fürtök az Azure-on
#### Létrehozás
##### [Azure Portal](service-fabric-cluster-creation-via-portal.md)
##### [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
#### Méretezés
##### [Manuálisan](service-fabric-cluster-scale-up-down.md)
##### [Programozott módon](service-fabric-cluster-programmatic-scaling.md)
#### [Frissítés](service-fabric-cluster-upgrade.md)
#### [Hozzáférés-vezérlés beállítása](service-fabric-cluster-security-roles.md)
#### [Konfigurálás](service-fabric-cluster-fabric-settings.md)
#### [Port megnyitása a terheléselosztóban](create-load-balancer-rule.md)
#### [Fürttanúsítványok kezelése](service-fabric-cluster-security-update-certs-azure.md)
#### [Törlés](service-fabric-cluster-delete.md)

### Önálló fürtök
#### Létrehozás
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
### [Fürtcsomópontok javítása](service-fabric-patch-orchestration-application.md)

## Megfigyelés és diagnosztika
### OMS
#### [OMS Log Analytics beállítása](service-fabric-diagnostics-oms-setup.md)
#### [Az OMS-ügynök hozzáadása](service-fabric-diagnostics-oms-agent.md)
#### [Tárolók figyelése](service-fabric-diagnostics-oms-containers.md)
### Teljesítményfigyelés
#### [Teljesítményfigyelés WAD használatával](service-fabric-diagnostics-perf-wad.md)

# Referencia
## [Azure PowerShell](/powershell/module/azurerm.servicefabric/)
## [PowerShell](/powershell/module/servicefabric/?view=azureservicefabricps)
## [Azure CLI (az sf)](/cli/azure/sf)
## [Service Fabric parancssori felület (sfctl)](service-fabric-sfctl.md)
### [sfctl-alkalmazás](service-fabric-sfctl-application.md)
### [sfctl-káosz](service-fabric-sfctl-chaos.md)
### [sfctl-fürt](service-fabric-sfctl-cluster.md)
### [sfctl-összeállítás](service-fabric-sfctl-compose.md)
### [sfctl értéke](service-fabric-sfctl-is.md)
### [sfctl-csomópont](service-fabric-sfctl-node.md)
### [sfctl-partíció](service-fabric-sfctl-partition.md)
### [sfctl-replika](service-fabric-sfctl-replica.md)
### [sfctl rpm](service-fabric-sfctl-rpm.md)
### [sfctl-szolgáltatás](service-fabric-sfctl-service.md)
### [sfctl-tároló](service-fabric-sfctl-store.md)
## [Java API](/java/api/overview/azure/servicefabric)
## [.NET](/dotnet/api/overview/azure/service-fabric?view=azure-dotnet)
## [REST](/rest/api/servicefabric)
## [Szolgáltatásmodell XML-sémája](service-fabric-service-model-schema.md)

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