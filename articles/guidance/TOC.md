# Architektúra

## Felhő – alapok

### [Rugalmas alkalmazások tervezése](guidance-resiliency-overview.md)
#### [Rugalmassági ellenőrzőlista](guidance-resiliency-checklist.md)
#### [Hibaállapot elemzése](guidance-resiliency-failure-mode-analysis.md)
#### [Vészhelyreállítás](..\resiliency\resiliency-disaster-recovery-azure-applications.md)
#### [Vészhelyreállítás és magas rendelkezésre állás](..\resiliency\resiliency-disaster-recovery-high-availability-azure-applications.md)
#### [Magas rendelkezésre állás](..\resiliency\resiliency-high-availability-azure-applications.md)
#### [Magas rendelkezésre állási ellenőrzőlista](..\resiliency\resiliency-high-availability-checklist.md)
#### [Az Azure-szolgáltatásra jellemző rugalmassággal kapcsolatos segédletek](..\resiliency\resiliency-service-guidance-index.md)
#### [Adatsérülés vagy véletlen törlés utáni helyreállítás](..\resiliency\resiliency-technical-guidance-recovery-data-corruption.md)
#### [Helyi hibák utáni helyreállítás](..\resiliency\resiliency-technical-guidance-recovery-local-failures.md)
#### [Régióra kiterjedő szolgáltatáskimaradás utáni helyreállítás](..\resiliency\resiliency-technical-guidance-recovery-loss-azure-region.md)
#### [Helyszíni helyreállítás az Azure-ba](..\resiliency\resiliency-technical-guidance-recovery-on-premises-azure.md)
#### [Műszaki útmutató az Azure rugalmasságáról](..\resiliency\resiliency-technical-guidance.md)


## Referenciaarchitektúrák

### [Virtuális gépek számítási feladatainak futtatása az Azure-ban](guidance-ra-compute.md)
#### [Linux rendszerű virtuális gép futtatása az Azure-ban](guidance-compute-single-vm-linux.md)
#### [Windows rendszerű virtuális gép futtatása az Azure-ban](guidance-compute-single-vm.md)
#### [Több virtuális gép futtatása az Azure-ban a méretezhetőség és rendelkezésre állás érdekében](guidance-compute-multi-vm.md)
#### [Linux rendszerű virtuális gépek futtatása egy N szintű architektúrához](guidance-compute-n-tier-vm-linux.md)
#### [Windows rendszerű virtuális gépek futtatása egy N szintű architektúrához](guidance-compute-n-tier-vm.md)
#### [Linux rendszerű virtuális gépek futtatása több régióban a magas rendelkezésre állás érdekében](guidance-compute-multiple-datacenters-linux.md)
#### [Windows rendszerű virtuális gépek futtatása több régióban a magas rendelkezésre állás érdekében](guidance-compute-multiple-datacenters.md)

### [Helyszíni hálózat csatlakoztatása az Azure-hoz](guidance-ra-hybrid-networking.md)
#### [Hibrid hálózati architektúra az Azure ExpressRoute használatával](guidance-hybrid-network-expressroute.md)
#### [Hibrid hálózati architektúra az Azure és helyszíni VPN használatával](guidance-hybrid-network-vpn.md)
#### [Magas rendelkezésre állású hibrid hálózati architektúra](guidance-hybrid-network-expressroute-vpn-failover.md)

### [Felhőhatár védelme az Azure-ban](guidance-ra-network-security.md)
#### [Biztonságos hibrid hálózati architektúra az Azure-ban](guidance-iaas-ra-secure-vnet-hybrid.md)
#### [Szegélyhálózat (DMZ) az Azure és az internet között](guidance-iaas-ra-secure-vnet-dmz.md)

### [Identitáskezelés az Azure-ban](guidance-ra-identity.md)
#### [Az Azure Active Directory implementálása](guidance-identity-aad.md)
#### [Az ADDS kiterjesztése az Azure-ra](guidance-identity-adds-extend-domain.md)
#### [ADDS-erőforráserdő létrehozása az Azure-ban](guidance-identity-adds-resource-forest.md)
#### [ADFS implementálása az Azure-ban](guidance-identity-adfs.md)

### [Az Azure App Service webalkalmazás-architektúrái](guidance-ra-app-service.md)
#### [Alapszintű webalkalmazás](guidance-web-apps-basic.md)
#### [Magas rendelkezésre állású webalkalmazás](guidance-web-apps-multi-region.md)
#### [Méretezhetőség továbbfejlesztése egy webalkalmazásban](guidance-web-apps-scalability.md)


## A felhőalapú alkalmazások ajánlott eljárásai

### [API-tervezési segédlet](..\best-practices-api-design.md)
### [API-implementálási segédlet](..\best-practices-api-implementation.md)
### [Útmutató az automatikus méretezéshez](..\best-practices-auto-scaling.md)
### [Rendelkezésre állási ellenőrzőlista](..\best-practices-availability-checklist.md)
### [Útmutató a háttérben futó feladatokhoz](..\best-practices-background-jobs.md)
### [Azure – párosított régiók](..\best-practices-availability-paired-regions.md)
### [Gyorsítótárazási útmutató](..\best-practices-caching.md)
### [Útmutató a Content Delivery Networkhöz](..\best-practices-cdn.md)
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
#### [Adatfeldolgozási teljesítmény finomhangolása](guidance-elasticsearch-tuning-data-ingestion-performance.md)
#### [Adatok összesítésének és a lekérdezés teljesítményének finomhangolása](guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md)
#### [Rugalmasság és helyreállítás konfigurálása](guidance-elasticsearch-configuring-resilience-and-recovery.md)
#### [Teljesítménytesztelési környezet létrehozása](guidance-elasticsearch-creating-performance-testing-environment.md)
#### [JMeter tesztelési terv implementálása](guidance-elasticsearch-implementing-jmeter-test-plan.md)
#### [JMeter JUnit-minta üzembe helyezése](guidance-elasticsearch-deploying-jmeter-junit-sampler.md)
#### [Az automatizált rugalmassági tesztek futtatása](guidance-elasticsearch-running-automated-resilience-tests.md)
#### [Az automatizált teljesítménytesztek futtatása](guidance-elasticsearch-running-automated-performance-tests.md)

### [Több-bérlős alkalmazások identitáskezelése](guidance-multitenant-identity.md)
#### [Áttekintés](guidance-multitenant-identity-intro.md)
#### [A Tailspin Surveys-alkalmazás](guidance-multitenant-identity-tailspin.md)
#### [Hitelesítés az Azure AD és az OpenID Connect segítségével](guidance-multitenant-identity-authenticate.md)
#### [Jogcímalapú identitások](guidance-multitenant-identity-claims.md)
#### [Regisztráció és a bérlők felvétele](guidance-multitenant-identity-signup.md)
#### [Alkalmazási szerepkörök](guidance-multitenant-identity-app-roles.md)
#### [Szerepkör- és erőforrás-alapú hitelesítés](guidance-multitenant-identity-authorize.md)
#### [A háttéralkalmazás webes API-jának biztonságossá tétele](guidance-multitenant-identity-web-api.md)
#### [Gyorsítótár hozzáférési jogkivonatai](guidance-multitenant-identity-token-cache.md)
#### [Összevonás egy ügyfél AD FS-ével](guidance-multitenant-identity-adfs.md)
#### [Ügyfél-érvényesítő használata a hozzáférési jogkivonatokért](guidance-multitenant-identity-client-assertion.md)
#### [Azure Key Vault használata az alkalmazás titkainak védelmére](guidance-multitenant-identity-keyvault.md)
#### [Virtuális készülékek üzembe helyezése magas rendelkezésre állásra](guidance-nva-ha.md)


<!--HONumber=Nov16_HO4-->


