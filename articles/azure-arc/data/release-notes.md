---
title: Azure arc-kompatibilis adatszolgáltatások – kibocsátási megjegyzések
description: Legújabb kibocsátási megjegyzések
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: 3c20bbd3ab02cd1eccd00e2d36c14eebf2f63205
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92360307"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>Kibocsátási megjegyzések – az Azure arc-kompatibilis adatszolgáltatások (előzetes verzió)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="september-2020"></a>2020. szeptember

A nyilvános előzetes verzióban elérhető az Azure arc-kompatibilis adatszolgáltatások. Az arc-kompatibilis adatszolgáltatások lehetővé teszik az adatszolgáltatások bárhol történő kezelését.

- SQL Managed Instance
- PostgreSQL nagy kapacitású

Útmutatás: [Mi az az Azure arc-kompatibilis adatszolgáltatás?](overview.md)

### <a name="known-issues"></a>Ismert problémák

Erre a kiadásra a következő problémák érvényesek:

* **PostgreSQL nagy kapacitású-kiszolgálócsoport törlése**: Ha módosította a kiszolgálócsoport vagy-példány konfigurációját, várja meg a szerkesztési művelet befejeződését, mielőtt törölné egy PostgreSQL nagy kapacitású-csoportot.

* ** `azdata notebook run` sikertelen lehet**: a probléma megoldásához futtasson `azdata notebook run` egy Python virtuális környezetben. Ez a probléma az SQL felügyelt példányának vagy PostgreSQL nagy kapacitású-kiszolgáló csoportjának a Azure Data Studio Deployment Wizard használatával történő létrehozásának sikertelen kísérlete esetén is megnyilvánul. Ebben az esetben nyissa meg a jegyzetfüzetet, és kattintson az **összes futtatása** gombra a jegyzetfüzet tetején.

## <a name="next-steps"></a>Következő lépések

> **Csak szeretné kipróbálni a dolgokat?**  
> Gyorsan elsajátíthatja az [Azure arc Jumpstart](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) az Azure Kubernetes Service (ak), az AWS rugalmas Kubernetes szolgáltatás (EKS), a Google Cloud Kubernetes Engine (GKE) vagy egy Azure-beli virtuális gépen.

[Az ügyféleszközök telepítése](install-client-tools.md)

[Hozza létre az Azure arc-adatkezelőt](create-data-controller.md) (először telepítenie kell az ügyféleszközök telepítését)

[Azure SQL felügyelt példány létrehozása az Azure arc](create-sql-managed-instance.md) szolgáltatásban (először létre kell hoznia egy Azure arc-adatkezelőt)

[Hozzon létre egy Azure Database for PostgreSQL nagy kapacitású-kiszolgáló csoportot az Azure-ív](create-postgresql-hyperscale-server-group.md) (először egy Azure arc-adatkezelőt kell létrehozni)

## <a name="known-limitations-and-issues"></a>Ismert korlátozások és problémák

- Az SQL felügyelt példányainak neve nem lehet hosszabb 13 karakternél.
- Nincs helyben történő frissítés az Azure arc-adatkezelő vagy adatbázis-példányok számára.
- Az Arc-kompatibilis adatszolgáltatások tárolórendszerképei nem rendelkeznek aláírással.  Lehet, hogy konfigurálnia kell a Kubernetes-csomópontokat az aláírás nélküli tárolórendszerképek lekérésére.  Ha például a Docker-t tároló futtatókörnyezetként használja, beállíthatja a DOCKER_CONTENT_TRUST = 0 környezeti változót, és újraindulhat.  Más tároló-futtatókörnyezetek hasonló beállításokkal rendelkeznek, ilyen például az [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration).
- Nem hozható létre az Azure arc használatára képes SQL felügyelt példányok vagy PostgreSQL nagy kapacitású-kiszolgálócsoportok a Azure Portal.
- Most, ha az NFS-t használja, az Azure arc-adatkezelő létrehozása előtt be kell állítania a allowRunAsRoot True értékre a telepítési profil fájlban.
- Csak az SQL és a PostgreSQL bejelentkezési hitelesítés.  Azure Active Directory vagy Active Directory nem támogatott.
- A OpenShift adatvezérlőjének létrehozásához nyugodt biztonsági korlátozások szükségesek.  További információt a dokumentációban találhat.
- A postgres nagy kapacitású- _feldolgozó csomópontok_ számának méretezése nem támogatott.
- Ha az Azure Kubernetes Service Engine (ak motor) szolgáltatást használja az Azure arc-adatkezelővel és az adatbázis-példányokkal Azure Stack hubhoz, az újabb verzióra való frissítés nem támogatott. A Kubernetes-fürt frissítése előtt távolítsa el az Azure arc-adatkezelőt és az összes adatbázis-példányt.
- Az előzetes verzió nem támogatja a postgres 11-es verziójú motor biztonsági mentését és visszaállítását. A szolgáltatás csak a 12-es verziójú postgres biztonsági mentését és visszaállítását támogatja.
- Az Azure Kubernetes szolgáltatás (ak), a [több rendelkezésre állási zónára](../../aks/availability-zones.md) kiterjedő fürtök jelenleg nem támogatottak az Azure arc-kompatibilis adatszolgáltatások esetében. Ha el szeretné kerülni ezt a problémát, akkor amikor az AK-fürtöt Azure Portalban hozza létre, ha olyan régiót választ ki, ahol elérhetők zónák, törölje az összes zónát a kijelölés vezérlőelemből. Lásd a következő képet:

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="Törölje az egyes zónák jelölőnégyzeteit a none érték megadásához.":::

  
