# Architektúra

## Felhő – alapok

### [Rugalmas alkalmazások tervezése az Azure számára](guidance-resiliency-overview.md)
#### [Rugalmassági ellenőrzőlista](guidance-resiliency-checklist.md)
#### [Hibaállapot elemzése](guidance-resiliency-failure-mode-analysis.md)

#### [A Microsoft Azure-re épülő alkalmazások vész-helyreállítása](..\resiliency\resiliency-disaster-recovery-azure-applications.md)
#### [A Microsoft Azure-re épülő alkalmazások vész-helyreállítása és magas rendelkezésre állása](..\resiliency\resiliency-disaster-recovery-high-availability-azure-applications.md)
#### [A Microsoft Azure-re épülő alkalmazások magas rendelkezésre állása](..\resiliency\resiliency-high-availability-azure-applications.md)
#### [Magas rendelkezésre állási ellenőrzőlista](..\resiliency\resiliency-high-availability-checklist.md)
#### [Az Azure-szolgáltatásra jellemző rugalmassággal kapcsolatos segédletek](..\resiliency\resiliency-service-guidance-index.md)
#### [Adatsérülés vagy véletlen törlés utáni helyreállítás](..\resiliency\resiliency-technical-guidance-recovery-data-corruption.md)
#### [Helyi hibák utáni helyreállítás az Azure-ban](..\resiliency\resiliency-technical-guidance-recovery-local-failures.md)
#### [Régióra kiterjedő szolgáltatáskimaradás utáni helyreállítás](..\resiliency\resiliency-technical-guidance-recovery-loss-azure-region.md)
#### [Helyszíni helyreállítás az Azure-ba](..\resiliency\resiliency-technical-guidance-recovery-on-premises-azure.md)
#### [Műszaki útmutató az Azure rugalmasságáról](..\resiliency\resiliency-technical-guidance.md)


## Referenciaarchitektúrák

### Referenciaarchitektúra-számítás
#### [Linuxos virtuális gép futtatása az Azure-ban](guidance-compute-single-vm-linux.md)
#### [Windows-rendszerű virtuális gép futtatása az Azure-ban](guidance-compute-single-vm.md)
#### [Több virtuális gép futtatása az Azure-ban a méretezhetőség és rendelkezésre állás érdekében](guidance-compute-multi-vm.md)
#### [Linuxos virtuális gépek futtatása egy N szintű architektúrához az Azure-ban](guidance-compute-n-tier-vm-linux.md)
#### [Windows-rendszerű virtuális gépek futtatása egy N szintű architektúrához az Azure-ban](guidance-compute-n-tier-vm.md)
#### [Linuxos virtuális gépek futtatása több régióban a magas rendelkezésre állás érdekében](guidance-compute-multiple-datacenters-linux.md)
#### [Windows-rendszerű virtuális gépek futtatása több régióban a magas rendelkezésre állás érdekében](guidance-compute-multiple-datacenters.md)

### [Helyszíni hálózat csatlakoztatása az Azure-hoz](guidance-connecting-your-on-premises-network-to-azure.md)
#### [Hibrid hálózati architektúra megvalósítása az Azure ExpressRoute használatával](guidance-hybrid-network-expressroute.md)
#### [Hibrid hálózati architektúra megvalósítása Azure és helyszíni VPN használatával](guidance-hybrid-network-vpn.md)
#### [Magas rendelkezésre állású hibrid hálózati architektúra megvalósítása](guidance-hybrid-network-expressroute-vpn-failover.md)

### Felhőhatár védelme az Azure-ban
#### [Biztonságos hibrid hálózati architektúra megvalósítása az Azure-ban](guidance-iaas-ra-secure-vnet-hybrid.md)
#### [Szegélyhálózat (DMZ) megvalósítása az Azure és az internet között](guidance-iaas-ra-secure-vnet-dmz.md)

### [Identitáskezelés az Azure-ban](guidance-ra-identity.md)
#### [Az Azure Active Directory megvalósítása](guidance-identity-aad.md)
#### [Az Active Directory Directory Services (ADDS) kiterjesztése az Azure-ra](guidance-identity-adds-extend-domain.md)
#### [Active Directory Directory Services- (ADDS) erőforráserdő létrehozása az Azure-ban](guidance-identity-adds-resource-forest.md)
#### [Active Directory Federation Services (ADFS) megvalósítása az Azure-ban](guidance-identity-adfs.md)

### PaaS-webalkalmazás referenciaarchitektúrája
#### [Azure-referenciaarchitektúra: Alapszintű webalkalmazás](guidance-web-apps-basic.md)
#### [Azure-referenciaarchitektúra: Webalkalmazás magas rendelkezésre állással](guidance-web-apps-multi-region.md)
#### [Méretezhetőség továbbfejlesztése egy webalkalmazásban](guidance-web-apps-scalability.md)


## Tervezési minták felhőkhöz

## A felhőalapú alkalmazások ajánlott eljárásai

### [API-tervezési segédlet](..\best-practices-api-design.md)
### [API-implementálási segédlet](..\best-practices-api-implementation.md)
### [Útmutató az automatikus méretezéshez](..\best-practices-auto-scaling.md)
### [Rendelkezésre állási ellenőrzőlista](..\best-practices-availability-checklist.md)
### [Útmutató a háttérben futó feladatokhoz](..\best-practices-background-jobs.md)
### [Üzletmenet-folytonosság és vészhelyreállítás (BCDR): Azure párosított régiók](..\best-practices-availability-paired-regions.md)
### [Gyorsítótárazási útmutató](..\best-practices-caching.md)
### [Útmutató a tartalomkézbesítési hálózathoz (CDN)](..\best-practices-cdn.md)
### [Adatparticionálási útmutató](..\best-practices-data-partitioning.md)
### [Megfigyelési és diagnosztikai útmutató](..\best-practices-monitoring.md)
### [A Microsoft Cloud Services és a hálózati biztonság](..\best-practices-network-security.md)
### [Minták Azure Resource Manager-sablonok tervezéséhez](..\best-practices-resource-manager-design-templates.md)
### [Azure-erőforrások ajánlott elnevezési szabályai](guidance-naming-conventions.md)
### [Az Azure Resource Manager használata során felmerülő biztonsági szempontok](..\best-practices-resource-manager-security.md)
### [Állapotmegosztás az Azure Resource Manager-sablonokban](..\best-practices-resource-manager-state.md)
### [Általános útmutató az újrapróbálkozási mechanizmushoz](..\best-practices-retry-general.md)
### [Szolgáltatásspecifikus útmutató az újrapróbálkozási mechanizmushoz](..\best-practices-retry-service-specific.md)
### [Méretezési ellenőrzőlista](..\best-practices-scalability-checklist.md)


## Forgatókönyv-útmutatók

### [Elasticsearch az Azure-útmutatóban](guidance-elasticsearch.md)
#### [Elasticsearch futtatása az Azure-ban](guidance-elasticsearch-running-on-azure.md)
#### [Az Elasticsearch adatbeviteli teljesítményének hangolása az Azure-ban](guidance-elasticsearch-tuning-data-ingestion-performance.md)
#### [Az adatösszesítés és a lekérdezési teljesítmény hangolása az Elasticsearch segítségével az Azure-ban](guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md)
#### [Rugalmasság és helyreállítás konfigurálása az Elasticsearch-ön az Azure-ban](guidance-elasticsearch-configuring-resilience-and-recovery.md)
#### [Teljesítménytesztelési környezet létrehozása az Elasticsearch számára az Azure-ban](guidance-elasticsearch-creating-performance-testing-environment.md)
#### [JMeter tesztelési csomag megvalósítása az Elasticsearch számára](guidance-elasticsearch-implementing-jmeter-test-plan.md)
#### [JMeter JUnit-minta telepítése az Elasticsearch teljesítményének tesztelésére](guidance-elasticsearch-deploying-jmeter-junit-sampler.md)
#### [Automatikus Elasticsearch rugalmassági tesztek futtatása](guidance-elasticsearch-running-automated-resilience-tests.md)
#### [Automatikus Elasticsearch teljesítménytesztek futtatása](guidance-elasticsearch-running-automated-performance-tests.md)

### [Több-bérlős alkalmazások identitáskezelése a Microsoft Azure-ban](guidance-multitenant-identity.md)
#### [Bevezetés a több-bérlős alkalmazások identitáskezelésébe a Microsoft Azure-ban](guidance-multitenant-identity-intro.md)
#### [A Tailspin Surveys-alkalmazásról](guidance-multitenant-identity-tailspin.md)
#### [Hitelesítés a több-bérlős alkalmazásokban az Azure AD és az OpenID Connect segítségével](guidance-multitenant-identity-authenticate.md)
#### [A jogcímalapú identitások használata több-bérlős alkalmazásokban](guidance-multitenant-identity-claims.md)
#### [Bejelentkezés és bérlőfelvétel egy több-bérlős alkalmazásban](guidance-multitenant-identity-signup.md)
#### [Alkalmazási szerepkörök több-bérlős alkalmazásokban](guidance-multitenant-identity-app-roles.md)
#### [Szerepkör- és erőforrás-alapú hitelesítés több-bérlős alkalmazásokban](guidance-multitenant-identity-authorize.md)
#### [Egy háttéralkalmazás webes API-jának védelme egy több-bérlős alkalmazásban](guidance-multitenant-identity-web-api.md)
#### [Hozzáférési jogkivonatok gyorsítótárazása egy több-bérlős alkalmazásban](guidance-multitenant-identity-token-cache.md)
#### [Összevonás egy ügyfél AD FS-szolgáltatásával több-bérlős alkalmazások számára az Azure-ban](guidance-multitenant-identity-adfs.md)
#### [Ügyfél-érvényesítő használata az Azure AD-ból származó hozzáférési jogkivonatokért](guidance-multitenant-identity-client-assertion.md)
#### [Azure Key Vault használata az alkalmazás titkainak védelmére](guidance-multitenant-identity-keyvault.md)

#### [Virtuális készülékek üzembe helyezése magas rendelkezésre állásra](guidance-nva-ha.md)


<!--HONumber=Nov16_HO2-->


