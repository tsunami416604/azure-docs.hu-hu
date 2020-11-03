---
title: Azure arc-kompatibilis adatszolgáltatások – kibocsátási megjegyzések
description: Legújabb kibocsátási megjegyzések
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 10/29/2020
ms.topic: conceptual
ms.openlocfilehash: e7312ffd4d55f0403359f8aad2d0a8433a716f77
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280366"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>Kibocsátási megjegyzések – az Azure arc-kompatibilis adatszolgáltatások (előzetes verzió)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="october-2020"></a>2020. október 

### <a name="breaking-changes"></a>Kompatibilitástörő változások

Ez a kiadás a következő feltörési változásokat mutatja be: 

* A PostgreSQL-hez tartozó egyéni erőforrás-definíciós (CRD) fájlok helyére a `shards` rendszer átnevezi a kifejezést `workers` . Ez a kifejezés ( `workers` ) megfelel a parancssori paraméter nevének.

* `azdata arc postgres server delete` a rendszer megerősítést kér a postgres-példány törlése előtt.  `--force`A paranccsal kihagyhatja a kérdést.

### <a name="additional-changes"></a>További módosítások

* Új opcionális paraméter lett hozzáadva a `azdata arc postgres server create` híváshoz `--volume-claim mounts` . Az érték a mennyiségi jogcím-csatlakoztatások vesszővel tagolt listája. A mennyiségi jogcím csatlakoztatása a kötet típusa és a PVC neve pár. A most már csak a kötet típusa engedélyezhető `backup` .  A PostgreSQL-ben, ha a kötet típusa `backup` , a PVC csatlakoztatva van `/mnt/db-backups` .  Ez lehetővé teszi a biztonsági másolatok megosztását a postgres-példányok között, így egy postgres-példány biztonsági mentése egy másikban is visszaállítható.

* A PostgresSQL egyéni erőforrás-definícióinak új rövid neve: 

  * `pg11` 

  * `pg12`

* A telemetria feltöltése a következők bármelyikével biztosít felhasználót:

   * Az Azure-ba feltöltött pontok száma

     vagy 

   * Ha az Azure-ba nem töltöttek be adatbevitelt, a rendszer kérni fogja, hogy próbálkozzon újra.

* `azdata arc dc debug copy-logs` most is beolvassa a `/var/opt/controller/log` mappát, és gyűjti a postgres-naplókat.

*   A biztonsági mentés során a postgres létrehozásakor és visszaállításakor működő kijelző megjelenítése.

* `azdata arc postrgres backup list` a mostantól tartalmazza a biztonsági másolatok méretére vonatkozó információkat.

* Az SQL felügyelt példányának admin Name tulajdonsága a Azure Portal áttekintés paneljének jobb oldali oszlopához lett hozzáadva.



## <a name="september-2020"></a>2020. szeptember

A nyilvános előzetes verzióban elérhető az Azure arc-kompatibilis adatszolgáltatások. Az arc-kompatibilis adatszolgáltatások lehetővé teszik az adatszolgáltatások bárhol történő kezelését.

- Felügyelt SQL-példány
- PostgreSQL nagy kapacitású

Útmutatás: [Mi az az Azure arc-kompatibilis adatszolgáltatás?](overview.md)

## <a name="known-limitations-and-issues"></a>Ismert korlátozások és problémák

- Az SQL felügyelt példányainak neve nem lehet hosszabb 13 karakternél.
- Nincs helyben történő frissítés az Azure arc-adatkezelő vagy adatbázis-példányok számára.
- Az Arc-kompatibilis adatszolgáltatások tárolórendszerképei nem rendelkeznek aláírással.  Lehet, hogy konfigurálnia kell a Kubernetes-csomópontokat az aláírás nélküli tárolórendszerképek lekérésére.  Ha például a Docker-t tároló futtatókörnyezetként használja, beállíthatja a DOCKER_CONTENT_TRUST = 0 környezeti változót, és újraindulhat.  Más tároló-futtatókörnyezetek hasonló beállításokkal rendelkeznek, ilyen például az [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration).
- Nem hozható létre az Azure arc használatára képes SQL felügyelt példányok vagy PostgreSQL nagy kapacitású-kiszolgálócsoportok a Azure Portal.
- Most, ha az NFS-t használja, az `allowRunAsRoot` `true` Azure arc-adatkezelő létrehozása előtt be kell állítania a telepítési profil fájljában.
- Csak az SQL és a PostgreSQL bejelentkezési hitelesítés.  Azure Active Directory vagy Active Directory nem támogatott.
- A OpenShift adatvezérlőjének létrehozásához nyugodt biztonsági korlátozások szükségesek.  További információt a dokumentációban találhat.
- A PostgresSQL nagy kapacitású- _feldolgozó csomópontok_ számának méretezése nem támogatott.
- Ha az Azure Kubernetes Service Engine (ak motor) szolgáltatást használja az Azure arc-adatkezelővel és az adatbázis-példányokkal Azure Stack hubhoz, az újabb verzióra való frissítés nem támogatott. A Kubernetes-fürt frissítése előtt távolítsa el az Azure arc-adatkezelőt és az összes adatbázis-példányt.
- Az előzetes verzió nem támogatja a postgres 11-es verziójú motor biztonsági mentését és visszaállítását. A szolgáltatás csak a 12-es verziójú postgres biztonsági mentését és visszaállítását támogatja.
- Az Azure Kubernetes szolgáltatás (ak), a [több rendelkezésre állási zónára](../../aks/availability-zones.md) kiterjedő fürtök jelenleg nem támogatottak az Azure arc-kompatibilis adatszolgáltatások esetében. Ha el szeretné kerülni ezt a problémát, akkor amikor az AK-fürtöt Azure Portalban hozza létre, ha olyan régiót választ ki, ahol elérhetők zónák, törölje az összes zónát a kijelölés vezérlőelemből. Lásd a következő képet:

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="Törölje az egyes zónák jelölőnégyzeteit a none érték megadásához.":::

## <a name="next-steps"></a>Következő lépések
  
> **Csak szeretné kipróbálni a dolgokat?**  
> Gyorsan elsajátíthatja az [Azure arc Jumpstart](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) az Azure Kubernetes Service (ak), az AWS rugalmas Kubernetes szolgáltatás (EKS), a Google Cloud Kubernetes Engine (GKE) vagy egy Azure-beli virtuális gépen.

[Az ügyféleszközök telepítése](install-client-tools.md)

[Hozza létre az Azure arc-adatkezelőt](create-data-controller.md) (először telepítenie kell az ügyféleszközök telepítését)

[Azure SQL felügyelt példány létrehozása az Azure arc](create-sql-managed-instance.md) szolgáltatásban (először létre kell hoznia egy Azure arc-adatkezelőt)

[Hozzon létre egy Azure Database for PostgreSQL nagy kapacitású-kiszolgáló csoportot az Azure-ív](create-postgresql-hyperscale-server-group.md) (először egy Azure arc-adatkezelőt kell létrehozni)
