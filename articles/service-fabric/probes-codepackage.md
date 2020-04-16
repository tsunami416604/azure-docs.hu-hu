---
title: Azure Service Fabric-mintavételek
description: Hogyan modellezze a Liveness-mintavételt az Azure Service Fabricben alkalmazás- és szolgáltatásjegyzékfájlok használatával.
ms.topic: conceptual
ms.date: 3/12/2020
ms.openlocfilehash: 38f3888a29bf505b723d40bc7cd08fb0c7e29eff
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431214"
---
# <a name="liveness-probe"></a>Élesség szonda
A 7.1 Service Fabric-től kezdve támogatja a Liveness Probe [mechanizmust a tárolóba kötött][containers-introduction-link] alkalmazásokhoz. A Liveness Probe segít bejelenteni a tárolóba rendezett alkalmazás élőségét, és ha nem válaszolnak időben, az újraindítást eredményez.
Ez a cikk áttekintést nyújt arról, hogyan definiálhat egy Liveness-mintavételt a jegyzékfájlokon keresztül.

Mielőtt folytatná ezt a cikket, javasoljuk, hogy ismerkedjen meg a [Service Fabric alkalmazásmodell][application-model-link] és a [Service Fabric üzemeltetési modell.][hosting-model-link]

> [!NOTE]
> A Liveness Probe csak NAT hálózati módban lévő tárolók esetén támogatott.

## <a name="semantics"></a>Szemantika
Tárolónként csak 1 Liveness Probe-ot adhat meg, és az alábbi mezőkkel szabályozhatja a működését:

* `initialDelaySeconds`: A kezdeti késleltetés másodpercben a mintavétel végrehajtásának megkezdéséhez a tároló indítása után. A támogatott érték int. Az alapértelmezett érték 0. A minimum 0.

* `timeoutSeconds`: Időszak másodpercben, amely után úgy véljük, szonda sikertelen, ha még nem fejeződött be sikeresen. A támogatott érték int. Az alapértelmezett érték 1. A minimum 1.

* `periodSeconds`: Időszak másodpercben, hogy meghatározza, milyen gyakran szondázunk. A támogatott érték int. Az alapértelmezett érték 10. A minimum 1.

* `failureThreshold`: Ahibaküszöb elérése után a tároló újraindul. A támogatott érték int. Az alapértelmezett érték 3. A minimum 1.

* `successThreshold`: A hiba, a mintavétel sikeresnek kell tekinteni, hogy sikeresnek kell lennie a SuccessThreshold. A támogatott érték int. Az alapértelmezett érték 1. A minimum 1.

Lesz legb.000 szonda konténer egy pillanat alatt az idő. Ha a szonda nem fejeződik be **timeoutSeconds** tartjuk várakozás, és számít, hogy a **failureThreshold**. 

Emellett a ServiceFabric a következő [mintavételi állapotjelentéseket][health-introduction-link] veti fel a DeployedServicePackage szolgáltatáson:

* `Ok`: Ha a szonda sikeres **a successThreshold,** akkor jelentjük az egészségügyi ok.

* `Error`: Ha a mintavételi hibaCount == **failureThreshold**, a tároló újraindítása előtt jelentjük hiba.

* `Warning`: 
    1. Ha a mintavétel sikertelen, és a failureCount < **failureThreshold** jelentjük figyelmeztetés. Ez az állapotjelentés addig marad, amíg a failureCount el nem éri **a failureThreshold** vagy **successThreshold küszöbértéket.**
    2. A siker utáni hiba, még mindig jelentés Figyelmeztetés, de a frissített egymást követő siker.

## <a name="specifying-liveness-probe"></a>Élességérzékelő megadása

Az ApplicationManifest.xml fájlban a ServiceManifestImport csoportban adhat meg mintavételt:

A szonda a következők közül választhat:

1. HTTP
2. TCP
3. Exec 

## <a name="http-probe"></a>HTTP-szonda

HTTP-mintavétel esetén a Service Fabric http-kérelmet küld a megadott portnak és elérési útnak. A 200 vagy annál kisebb visszatérési kód sikeres.

Íme egy példa a HttpGet szonda megadására:

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <HttpGet Path="/" Port="8081" Scheme="http">
              <HttpHeader Name="Foo" Value="Val"/>
              <HttpHeader Name="Bar" Value="val1"/>
            </HttpGet>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

A HttpGet mintavétel további, beállítható tulajdonságokkal rendelkezik:

* `path`: A HTTP-kérelem elérésének elérési útja.

* `port`: A szondák eléréséhez. A távolság 1 és 65535 között van. Kötelező.

* `scheme`: Kódcsomaghoz való csatlakozásra szolgáló séma. Ha HTTPS értékre van állítva, a tanúsítvány-ellenőrzés kimarad. Alapértelmezés szerint HTTP

* `httpHeader`: A kérelemben beállítandó fejlécek. Ezek közül több is megadható.

* `host`: Host IP csatlakozni.

## <a name="tcp-probe"></a>TCP-mintavétel

A TCP-mintavétel, Service Fabric megpróbálja megnyitni a szoftvercsatorna a tárolón a megadott porttal. Ha kapcsolatot tud létesíteni, a szonda sikeresnek minősül. Íme egy példa a TCP-szoftvercsatornát használó mintavétel megadására:

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <TcpSocket Port="8081"/>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

## <a name="exec-probe"></a>Exec szonda

Ez a mintavétel kiad egy exec a tárolóba, és várja meg a parancs befejezését.

> [!NOTE]
> Exec parancs vesz egy vesszővel elválasztó karakterláncot. A következő parancs a példában működni fog a Linux-tároló.
> Ha windows container-t próbál, használja a <Command>cmd</Command>

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <Exec>
              <Command>ping,-c,2,localhost</Command>
            </Exec>
          </Probe>        
       </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

## <a name="next-steps"></a>További lépések
A kapcsolódó információkat az alábbi cikkekben talál.
* [Szolgáltatás fabric és tárolók.][containers-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[health-introduction-link]: service-fabric-health-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md

