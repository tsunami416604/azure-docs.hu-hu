
# Áttekintés
## [Mi a Service Fabric?](service-fabric-overview.md)
## [A mikroszolgáltatások ismertetése](service-fabric-overview-microservices.md)
## [Alkalmazáshasználati helyzetek](service-fabric-application-scenarios.md)
## [Minták és forgatókönyvek](service-fabric-patterns-and-scenarios.md)
## [Architektúra](service-fabric-architecture.md)
## [Terminológia](service-fabric-technical-overview.md)
## [Ismertető](service-fabric-content-roadmap.md)

# Bevezetés
## A fejlesztési környezet beállítása
### [Windows](service-fabric-get-started.md)
### [Linux](service-fabric-get-started-linux.md)
### [Mac OS](service-fabric-get-started-mac.md)
## Az első alkalmazás létrehozása
### [C# Windowson](service-fabric-create-your-first-application-in-visual-studio.md)
### [Java Linuxon](service-fabric-create-your-first-linux-application-with-java.md)
### [C# Linuxon](service-fabric-create-your-first-linux-application-with-csharp.md)
## [Alkalmazások telepítése helyi fürtre](service-fabric-get-started-with-a-local-cluster.md)
## [.NET-alkalmazások üzembe helyezése tárolókban](service-fabric-host-app-in-a-container.md)
## [Az első saját fürt létrehozása az Azure-on](service-fabric-get-started-azure-cluster.md)
## [Az első saját önálló fürt létrehozása](service-fabric-get-started-standalone-cluster.md)
## [Az első saját tárolóapp létrehozása](service-fabric-get-started-containers.md)

# Útmutató
## Alkalmazás készítése
  
### Alapelvek
#### [Támogatott programozási modellek](service-fabric-choose-framework.md)
#### [Az alkalmazásmodell](service-fabric-application-model.md)
#### [Futtatási modell](service-fabric-hosting-model.md)
#### [Szolgáltatásjegyzéki erőforrások](service-fabric-service-manifest-resources.md)
#### [Szolgáltatás állapota](service-fabric-concepts-state.md)
#### [Szolgáltatás particionálása](service-fabric-concepts-partitioning.md)
#### [A szolgáltatások rendelkezésre állása](service-fabric-availability-services.md)
#### [Az alkalmazások méretezhetősége](service-fabric-concepts-scalability.md)
#### [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

### [Alkalmazáskapacitás tervezése](service-fabric-capacity-planning.md)

### Futtatható vendégszolgáltatás készítése
#### [Futtatható vendégalkalmazás üzembe helyezése](service-fabric-deploy-existing-app.md)
#### [Több futtatható vendégalkalmazás üzembe helyezése](service-fabric-deploy-multiple-apps.md)

### Tárolószolgáltatás készítése
#### [Áttekintés](service-fabric-containers-overview.md)
#### [Windows-tároló üzembe helyezése](service-fabric-deploy-container.md)
#### [Linux-tároló üzembe helyezése](service-fabric-deploy-container-linux.md)
#### [Docker Compose (előzetes verzió)](service-fabric-docker-compose.md)
#### [Tárolók és szolgáltatások erőforrás-szabályozása](service-fabric-resource-governance.md)

### Reliable Services-szolgáltatás készítése
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

#### A Reliable Services életciklusa
##### [Konfigurálás](service-fabric-reliable-services-configuration.md)
##### [Értesítések küldése](service-fabric-reliable-services-notifications.md)
##### [Biztonsági mentés és visszaállítás](service-fabric-reliable-services-backup-restore.md)

#### Kommunikáció a szolgáltatásokkal
##### [Kommunikáció a Reliable Services szolgáltatással](service-fabric-reliable-services-communication.md)
##### [Szolgáltatás távelérésének lehetővé tétele – C#](service-fabric-reliable-services-communication-remoting.md)
##### [Szolgáltatás távelérésének lehetővé tétele – Java](service-fabric-reliable-services-communication-remoting-java.md)
##### [WCF](service-fabric-reliable-services-communication-wcf.md)
##### [Biztonságos kommunikáció – C#](service-fabric-reliable-services-secure-communication.md)
##### [Biztonságos kommunikáció – Java](service-fabric-reliable-services-secure-communication-java.md)

#### [Haladó szintű használat](service-fabric-reliable-services-advanced-usage.md)

### Reliable Actors-szolgáltatás készítése
#### [Áttekintés](service-fabric-reliable-actors-introduction.md)
#### Alapelvek
#### [Architektúra](service-fabric-reliable-actors-platform.md)
#### [Életciklus-kezelés és szemétgyűjtés](service-fabric-reliable-actors-lifecycle.md)
#### [Állapotkezelés](service-fabric-reliable-actors-state-management.md)
#### [Polimorfizmus](service-fabric-reliable-actors-polymorphism.md)
#### [Újbóli belépés](service-fabric-reliable-actors-reentrancy.md)
#### [Típusszerializáció](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

#### Bevezetés
##### [C# Windowson](service-fabric-reliable-actors-get-started.md)
##### [Java Linuxon](service-fabric-reliable-actors-get-started-java.md)

#### [Értesítések küldése](service-fabric-reliable-actors-events.md) 
#### [Időzítők és emlékeztetők beállítása](service-fabric-reliable-actors-timers-reminders.md)
#### [KvsActorStateProvider konfigurálása](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)
#### [Kommunikációs beállítások konfigurálása](service-fabric-reliable-actors-fabrictransportsettings.md) 
#### [ReliableDictionaryActorStateProvider konfigurálása](service-fabric-reliable-actors-reliabledictionarystateprovider-configuration.md)

### Kommunikáció a szolgáltatásokkal
#### [A szolgáltatások közötti kommunikáció](service-fabric-connect-and-communicate-with-services.md)
#### [DNS-szolgáltatás](service-fabric-dnsservice.md)
#### [Fordított proxy](service-fabric-reverseproxy.md)

### [Webes kezelőfelület hozzáadása](service-fabric-add-a-web-frontend.md)

### IDE-ben végzett munka
#### [Az Eclipse beépülő modul használatának első lépései Java-fejlesztésekhez](service-fabric-get-started-eclipse.md)
#### [Alkalmazások kezelése a Visual Studióban](service-fabric-manage-application-in-visual-studio.md)
#### [Biztonságos kapcsolatok konfigurálása a Visual Studióban](service-fabric-visualstudio-configure-secure-connections.md)
#### [Az alkalmazás konfigurálása több környezethez](service-fabric-manage-multiple-environment-app-configuration.md)

### Biztonság konfigurálása
#### [Titkos alkalmazáskulcsok kezelése](service-fabric-application-secret-management.md)  
#### [Biztonsági szabályzatok konfigurálása az alkalmazáshoz](service-fabric-application-runas-security.md)

### Hibakeresés
#### [C# szolgáltatás hibakeresése a VS-ben](service-fabric-debugging-your-application.md)
#### [Java szolgáltatás hibakeresése az Eclipse-ben](service-fabric-debugging-your-application-java.md)
#### [Gyakori hibák és kivételek](service-fabric-errors-and-exceptions.md)

### Helyi monitorozás és diagnosztika
#### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
#### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

### Áttelepítés a Cloud Servicesből
#### [A Cloud Services és a Service Fabric összehasonlítása](service-fabric-cloud-services-migration-differences.md)
#### [Migráció a Service Fabricre](service-fabric-cloud-services-migration-worker-role-stateless-service.md)
#### [Ajánlott eljárások](/azure/architecture/service-fabric/migrate-from-cloud-services)

## Alkalmazások életciklus-kezelése
### [Áttekintés](service-fabric-application-lifecycle.md)
### [Alkalmazás becsomagolása](service-fabric-package-apps.md)
### [Az ImageStoreConnectionString beállítás ismertetése](service-fabric-image-store-connection-string.md)
### Alkalmazások üzembe helyezése vagy eltávolítása
#### [PowerShell](service-fabric-deploy-remove-applications.md)
#### [Visual Studio](service-fabric-publish-app-remote-cluster.md)
#### [FabricClient API-k](service-fabric-deploy-remove-applications-fabricclient.md)
### Alkalmazás frissítése
#### [Áttekintés](service-fabric-application-upgrade.md)
#### [Az alkalmazásfrissítés konfigurálása](service-fabric-visualstudio-configure-upgrade.md)
#### [Alkalmazásfrissítési paraméterek](service-fabric-application-upgrade-parameters.md)
#### Frissítés
##### [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
##### [Visual Studio](service-fabric-application-upgrade-tutorial.md)
#### [Alkalmazásfrissítési hibák elhárítása](service-fabric-application-upgrade-troubleshooting.md)
#### [Adatok szerializálása az alkalmazásfrissítésekben](service-fabric-application-upgrade-data-serialization.md)
#### [Alkalmazásfrissítések – haladó témakörök](service-fabric-application-upgrade-advanced.md)

### Alkalmazások és szolgáltatások tesztelése
#### [A hibaelemzés áttekintése](service-fabric-testability-overview.md)
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

### [Áttekintés](service-fabric-deploy-anywhere.md)
### Alapelvek
#### [Fürt leírása](service-fabric-cluster-resource-manager-cluster-description.md)
#### [Fürtbiztonság](service-fabric-cluster-security.md)
#### [Különbségek a Linux és a Windows szolgáltatásai között](service-fabric-linux-windows-differences.md)

### Tervezés és előkészítés
#### [Kapacitástervezés](service-fabric-cluster-capacity.md)
#### [Vészhelyreállítás](service-fabric-disaster-recovery.md)

### Fürtök az Azure-on
#### Alapelvek
##### [Csomóponttípusok és virtuálisgép-méretezési csoportok](service-fabric-cluster-nodetypes.md)
##### [Fürthálózatminták](service-fabric-patterns-networking.md)
#### Létrehozás 
##### [Azure Portal](service-fabric-cluster-creation-via-portal.md)
##### [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
##### [Visual Studio és Azure Resource Manager](service-fabric-cluster-creation-via-visual-studio.md)
#### Méretezés 
##### [Manuálisan](service-fabric-cluster-scale-up-down.md)
##### [Programozott módon](service-fabric-cluster-programmatic-scaling.md)
#### [Frissítés](service-fabric-cluster-upgrade.md)
#### [Hozzáférés-vezérlés beállítása](service-fabric-cluster-security-roles.md)
#### [Konfigurálás](service-fabric-cluster-fabric-settings.md)
#### [Fürttanúsítványok kezelése](service-fabric-cluster-security-update-certs-azure.md) 
#### [Törlés](service-fabric-cluster-delete.md)

### Önálló fürtök
#### [A különálló csomag tartalma](service-fabric-cluster-standalone-package-contents.md)
#### [Tervezés és felkészülés az üzembe helyezésre](service-fabric-cluster-standalone-deployment-preparation.md)
#### Létrehozás
##### [Helyszíni létrehozás](service-fabric-cluster-creation-for-windows-server.md)
##### [Létrehozás Azure-beli virtuális gépeken](service-fabric-cluster-creation-with-windows-azure-vms.md)
##### [Biztonságossá tétel tanúsítványokkal](service-fabric-windows-cluster-x509-security.md)  
##### [Biztonságossá tétel a Windows-rendszerbiztonság használatával](service-fabric-windows-cluster-windows-security.md)
#### [Méretezés](service-fabric-cluster-windows-server-add-remove-nodes.md)
#### [Hozzáférés-vezérlés beállítása](service-fabric-cluster-security-roles.md)
#### [Konfigurálás](service-fabric-cluster-manifest.md)
#### [Frissítés](service-fabric-cluster-upgrade-windows-server.md) 

### [Fürt ábrázolása](service-fabric-visualizing-your-cluster.md)
### [Csatlakozás biztonságos fürthöz](service-fabric-connect-to-secure-cluster.md)

### [Fürt kezelése az Azure CLI-vel](service-fabric-azure-cli.md)
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

### [Csatlakozás biztonságos fürthöz](service-fabric-connect-to-secure-cluster.md)

## Az alkalmazás és a fürt állapotának vizsgálata
### [A Service Fabric állapotának figyelése](service-fabric-health-introduction.md)
### [Szolgáltatásállapot jelentése és ellenőrzése](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
### [Egyéni állapotjelentések hozzáadása](service-fabric-report-health.md)
### [Hibaelhárítás rendszerállapot-jelentések segítségével](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
### [Állapotjelentések megtekintése](service-fabric-view-entities-aggregated-health.md)

## Megfigyelés és diagnosztika
### [Alkalmazások figyelése és diagnosztikája](service-fabric-diagnostics-overview.md)
### A szolgáltatások helyi figyelése és diagnosztikája
#### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
#### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
### Azure Diagnostics-naplók
#### [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
#### [Linux](service-fabric-diagnostics-how-to-setup-lad.md)
### [Naplók lekérdezése egy szolgáltatási folyamatból](service-fabric-diagnostic-collect-logs-without-an-agent.md)
### [Windows Server-tárolók figyelése](service-fabric-diagnostics-containers-windowsserver.md)
### [A Stateful Reliable Services diagnosztikai funkciói](service-fabric-reliable-services-diagnostics.md)
### [Reliable Actors-diagnosztika](service-fabric-reliable-actors-diagnostics.md)
### [A helyi fürt hibaelhárítása](service-fabric-troubleshoot-local-cluster-setup.md)

# Referencia
## [PowerShell (Azure)](/powershell/module/azurerm.servicefabric/)
## [PowerShell](/powershell/azure/overview?view=azureservicefabricps)
## [Java API](/java/api/)
## [.NET](/dotnet/api/)
## [REST](/rest/api/servicefabric)

# Erőforrások
## [Gyakori kérdések a Service Fabrickel kapcsolatban](service-fabric-common-questions.md)
## [A Service Fabric támogatási lehetőségei](service-fabric-support.md)
## [Mintakód](http://aka.ms/servicefabricsamples)
## [Képzési terv](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
## [Díjszabás](https://azure.microsoft.com/pricing/details/service-fabric/)
## [Szolgáltatási hírek](https://azure.microsoft.com/updates/?product=service-fabric)
## [MSDN-fórum](https://social.msdn.microsoft.com/Forums/home?forum=AzureServiceFabric)
## [Videók](https://azure.microsoft.com/documentation/videos/index/?services=service-fabric)
