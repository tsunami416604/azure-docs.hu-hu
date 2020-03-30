---
title: Bevezetés a tárolócsoportokba
description: Ismerje meg az Azure Container Instances tárolócsoportokat, az életciklust és erőforrásokat, például a tárolást és a hálózatot megosztó példányok gyűjteményét
ms.topic: article
ms.date: 11/01/2019
ms.custom: mvc
ms.openlocfilehash: 73781418321c3932bf3e0190b646dcd3bb178195
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247214"
---
# <a name="container-groups-in-azure-container-instances"></a>Tárolócsoportok az Azure Container Instancesben

Az Azure Container Instances legfelső szintű erőforrása a *tárolócsoport.* Ez a cikk ismerteti, milyen tárolócsoportok és az általuk lehetővé tett forgatókönyvek típusai.

## <a name="what-is-a-container-group"></a>Mi az a tárolócsoport?

A tárolócsoport tárolók gyűjteménye, amely ugyanazon a gazdagépen lesz ütemezve. A tárolócsoporttárolók egy életciklust, erőforrásokat, helyi hálózatot és tárolóköteteket osztanak meg. Ez hasonló a koncepció, hogy egy *pod* [Kubernetes][kubernetes-pod].

Az alábbi ábra egy több tárolót tartalmazó tárolócsoportot mutat be:

![Tárolócsoportok diagramja][container-groups-example]

Ez a példa tárolócsoport:

* Egyetlen gazdagépen van ütemezve.
* DNS-névcímke van hozzárendelve.
* Egyetlen nyilvános IP-címet, egyetlen elérhető porttal teszi elérhetővé.
* Két tartályból áll. Az egyik tároló a 80-as porton, a másik az 5000-es porton figyel.
* Két Azure-fájlmegosztást tartalmaz kötetcsatolóként, és minden tároló helyileg csatlakoztatja az egyik megosztást.

> [!NOTE]
> A többtárolós csoportok jelenleg csak Linux-tárolókat támogatnak. A Windows-tárolók esetében az Azure Container Instances csak egyetlen tárolópéldány telepítését támogatja. Miközben azon dolgozunk, hogy az összes funkciót a Windows konténerek, megtalálja a jelenlegi platform különbségek a szolgáltatás [áttekintése](container-instances-overview.md#linux-and-windows-containers).

## <a name="deployment"></a>Környezet

A többtárolós csoport telepítésének két gyakori módja: [Erőforrás-kezelő sablon][resource-manager template] vagy [YAML-fájl][yaml-file]használata. A Resource Manager-sablon akkor ajánlott, ha további Azure-szolgáltatás-erőforrásokat (például egy [Azure Files-megosztást)][azure-files]kell üzembe helyeznie a tárolópéldányok üzembe helyezésekor. A YAML-formátum tömörebb jellege miatt a YAML-fájl akkor ajánlott, ha a központi telepítés csak tárolópéldányokat tartalmaz. A beállítható tulajdonságokról az [Erőforrás-kezelő sablonhivatkozásvagy](/azure/templates/microsoft.containerinstance/containergroups) a [YAML referenciadokumentációjában](container-instances-reference-yaml.md) olvashat.

A tárolócsoport konfigurációjának megőrzése érdekében exportálhatja a konfigurációt egy YAML-fájlba az Azure CLI parancs [az a container export][az-container-export]parancsával. Az exportálás lehetővé teszi a tárolócsoport-konfigurációk "kódként" történő verzióvezérlésben való tárolását. Vagy használja az exportált fájlt kiindulási pontként, amikor új konfigurációt fejleszt a YAML-ben.



## <a name="resource-allocation"></a>Erőforrás-elosztás

Az Azure Container Instances erőforrásokat, például processzorokat, memóriát és opcionálisan [GPU-kat][gpus] (előzetes verzió) foglal le egy többtárolós csoportszámára a csoport példányainak [erőforrás-kérelmeinek][resource-requests] hozzáadásával. A CPU-erőforrások példaként, ha két tárolópéldányt hoz létre, amelyek mindegyike 1 PROCESSZORt kér, majd a tárolócsoport 2 processzort foglal le.

### <a name="resource-usage-by-container-instances"></a>Erőforrás-használat tárolópéldányok szerint

A csoport minden tárolópéldánya le van foglalva az erőforrás-kérelemben megadott erőforrásokkal. Azonban a csoport ban egy tárolópéldány által használt maximális erőforrások eltérőek lehetnek, ha konfigurálja a választható [erőforrás-korlát][resource-limits] tulajdonságát. A tárolópéldány erőforrás-korlátjának nagyobbnak vagy egyenlőnek kell lennie a kötelező [erőforrás-kérelem][resource-requests] tulajdonsággal.

* Ha nem ad meg erőforráskorlátot, a tárolópéldány maximális erőforrás-felhasználása megegyezik az erőforráskérelemmel.

* Ha egy tárolópéldányhoz korlátot ad meg, a példány maximális használata nagyobb lehet, mint a kérés, a beállított korlátig. Ennek megfelelően a csoport más tárolópéldányainak erőforrás-felhasználása csökkenhet. A tárolópéldányhoz beállítható maximális erőforráskorlát a csoporthoz rendelt összes erőforrás.
    
Például egy csoportban két tárolópéldányok minden kérő 1 CPU, az egyik tárolók futtatása a számítási feladatok, amely több processzort igényel, mint a másik.

Ebben a forgatókönyvben 2 processzoros erőforráskorlátot állíthat be a tárolópéldányhoz. Ez a konfiguráció lehetővé teszi, hogy a tárolópéldány legfeljebb a teljes 2 processzort használjon, ha rendelkezésre áll.

### <a name="minimum-and-maximum-allocation"></a>Minimális és maximális allokáció

* **Legalább** 1 PROCESSZOR és 1 GB memória lefoglalása egy tárolócsoporthoz. A csoporton belüli egyes tárolópéldányok 1-nél kevesebb processzorral és 1 GB memóriával is kiépíthetők. 

* A **tárolócsoport ban** lévő maximális erőforrásokért tekintse meg az Azure Container Instances [erőforrás-elérhetőségét][region-availability] a központi telepítési régióban.

## <a name="networking"></a>Hálózat

A tárolócsoportok megoszthatják a külső ip-címet, az adott IP-cím egy vagy több portját, valamint egy teljesen minősített tartománynévvel (FQDN) rendelkező DNS-címkét. Ahhoz, hogy a külső ügyfelek elérhessenek egy tárolót a csoporton belül, fel kell tennie a portot az IP-címen és a tárolóból. Mivel a csoporton belüli tárolók egy portnévtérrel rendelkeznek, a portleképezés nem támogatott. A tárolócsoport IP-címe és az FQDN a tárolócsoport törlésekor felszabadul. 

Egy tárolócsoporton belül a tárolópéldányok elérhetik egymást a localhost-on keresztül bármely porton, még akkor is, ha ezek a portok nincsenek elérhetővé a csoport IP-címén vagy a tárolón kívül.

Szükség esetén üzembe helyezheti a tárolócsoportokat egy [Azure virtuális hálózatba,][virtual-network] hogy a tárolók biztonságosan kommunikálhassanak a virtuális hálózat más erőforrásaival.

## <a name="storage"></a>Storage

Megadhatja a tárolócsoporton belül csatlakoztatni kívánt külső köteteket. A támogatott kötetek a következők:
* [Azure fájlmegosztás][azure-files]
* [Titkos][secret]
* [Üres könyvtár][empty-directory]
* [Klónozott git tárhét][volume-gitrepo]

Ezeket a köteteket a csoport egyes tárolóin belüli adott elérési utakra képezheti. 

## <a name="common-scenarios"></a>Gyakori forgatókönyvek

A többtárolós csoportok akkor hasznosak, ha egyetlen funkcionális feladatot kis számú tárolórendszerképre szeretne osztani. Ezeket a képeket ezután különböző csapatok kézbesíthetik, és külön erőforrás-követelményekkel rendelkeznek.

A felhasználás példaa a következők lehet:

* Egy webalkalmazást kiszolgáló tároló és egy tároló, amely leveszi a legújabb tartalmat a forrásvezérlőből.
* Egy alkalmazástároló és egy naplózási tároló. A naplózási tároló gyűjti a naplókat és a metrikák kimenetét a fő alkalmazás, és írja azokat a hosszú távú tárolásra.
* Egy alkalmazástároló és egy figyelési tároló. A figyelési tároló rendszeres időközönként kéri az alkalmazást annak érdekében, hogy megfelelően fut, és megfelelően válaszol, és riasztást küld, ha nem.
* Előtér-tároló és háttértároló. Előfordulhat, hogy az előtér egy webalkalmazást szolgál ki, és a háttérrendszer egy szolgáltatást futtat az adatok lekéréséhez. 

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan helyezhet üzembe többtárolós tárolócsoportot egy Azure Resource Manager-sablonnal:

> [!div class="nextstepaction"]
> [Tárolócsoport telepítése][resource-manager template]

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/pod/

<!-- LINKS - Internal -->
[resource-manager template]: container-instances-multi-container-group.md
[yaml-file]: container-instances-multi-container-yaml.md
[region-availability]: container-instances-region-availability.md
[resource-requests]: /rest/api/container-instances/containergroups/createorupdate#resourcerequests
[resource-limits]: /rest/api/container-instances/containergroups/createorupdate#resourcelimits
[resource-requirements]: /rest/api/container-instances/containergroups/createorupdate#resourcerequirements
[azure-files]: container-instances-volume-azure-files.md
[virtual-network]: container-instances-vnet.md
[secret]: container-instances-volume-secret.md
[volume-gitrepo]: container-instances-volume-gitrepo.md
[gpus]: container-instances-gpu.md
[empty-directory]: container-instances-volume-emptydir.md
[az-container-export]: /cli/azure/container#az-container-export
