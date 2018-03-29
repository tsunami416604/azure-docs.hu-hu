---
title: Egy Azure Service Fabric-szolgáltatás futtatásához a rendszer és a helyi biztonsági fiókok |} Microsoft Docs
description: Útmutató a Service Fabric-alkalmazás futtatása a rendszer és a helyi biztonsági fiókok.  Rendszerbiztonsági létrehozása, és a szolgáltatások biztonságosan futtatásához futtató házirend alkalmazása.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: mfussell
ms.openlocfilehash: 62917a1d342158ec2114a9204ee1ca9e447284fa
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>A szolgáltatás futtatásához egy helyi felhasználói fiók vagy helyi rendszer fiókként
Azure Service Fabric használatával biztonságossá teheti a különböző felhasználói fiókok a fürtben futó alkalmazás számára. Alapértelmezés szerint a Service Fabric alkalmazások futnak, a fiók, amely alatt futó a Fabric.exe folyamatban. A Service Fabric is lehetővé teszi a helyi felhasználói fiók vagy helyi rendszerfiók, mert ahhoz a RunAs házirend belül az alkalmazásjegyzék-alkalmazások futtatására. Támogatott helyi rendszer fiók típusok **LocalUser**, **NetworkService**, **LocalService**, és **LocalSystem**.

Is határozza meg, és hozzon létre felhasználói csoportokat, hogy egy vagy több felhasználó is adható hozzá mindegyik csoport felügyelete együtt. Ez akkor hasznos, ha több felhasználó különböző belépési pontot, és bizonyos közös jogosultságok, amelyek elérhetők a csoportok szintjén van szükségük.

> [!NOTE] 
> Ha RunAs házirend vonatkozik egy szolgáltatás, és a szolgáltatás jegyzékfájl deklarálja a HTTP protokoll végpont erőforrások, meg kell adnia egy **SecurityAccessPolicy**.  További információkért lásd: [rendelje hozzá a HTTP és HTTPS-végpont egy biztonsági házirend](service-fabric-assign-policy-to-endpoint.md). 
>

## <a name="create-local-user-groups"></a>Helyi felhasználói csoportok létrehozása
Adja meg, és hozzon létre felhasználói csoportokat, amelyek lehetővé teszik egy vagy több felhasználót a csoporthoz lehet hozzáadni. Ez akkor hasznos, ha több felhasználó különböző belépési pontot, és bizonyos közös jogosultságok, amelyek elérhetők a csoportok szintjén van szükségük. A következő példa bemutatja a csoport **LocalAdminGroup** , amely rendszergazdai jogosultságokkal rendelkezik. Két felhasználók Customer1 és Customer2, a helyi csoport tagjai válnak, az alkalmazás jegyzékének példában:

```xml
<Principals>
 <Groups>
   <Group Name="LocalAdminGroup">
     <Membership>
       <SystemGroup Name="Administrators"/>
     </Membership>
   </Group>
 </Groups>
  <Users>
     <User Name="Customer1">
        <MemberOf>
           <Group NameRef="LocalAdminGroup" />
        </MemberOf>
     </User>
    <User Name="Customer2">
      <MemberOf>
        <Group NameRef="LocalAdminGroup" />
      </MemberOf>
    </User>
  </Users>
</Principals>
```

## <a name="create-local-users"></a>Helyi felhasználók létrehozása
Létrehozhat egy helyi felhasználót, amelyek segítségével biztonságossá tétele az alkalmazásban egy szolgáltatás. Ha egy **LocalUser** fióktípus szakaszban van megadva a rendszerbiztonsági tagoknak az alkalmazás jegyzékének Service Fabric helyi felhasználói fiókokat hoz gépekre, ahol az alkalmazás központi telepítése. Alapértelmezés szerint ezek a fiókok nem rendelkeznek (például az alkalmazás manifest ügynökök Customer3) az alkalmazás jegyzékében megadottakkal azonos néven. Ehelyett dinamikusan jön létre, és véletlenszerű jelszót.

```xml
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
```

Ha egy alkalmazás megköveteli, hogy a felhasználói fiókkal és jelszóval (például az NTLM-hitelesítés engedélyezése) összes gépen azonos, be kell-e a fürtjegyzékben **NTLMAuthenticationEnabled** igaz értékre. A fürtjegyzékben is meg kell adnia egy **NTLMAuthenticationPasswordSecret** használt összes számítógépével ugyanazt a jelszót létrehozni.

```xml
<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
```

## <a name="assign-policies-to-the-service-code-packages"></a>A szolgáltatás kód csomagok házirendek hozzárendelése
A **RunAsPolicy** szakasz egy **ServiceManifestImport** a rendszerbiztonsági tagok szakaszában a kódcsomag futtatásához használt fiókot adja meg. A felhasználói fiókok a rendszerbiztonsági tagok szakaszban kód csomagok a szolgáltatás-jegyzékfájlból is társítja. Ez a beállítás vagy a fő belépési pontok adhat meg, vagy megadhat `All` egyaránt vonatkozik. A következő példa bemutatja a különböző szabályzatok:

```xml
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
```

Ha **EntryPointType** nincs megadva, az alapértelmezett érték az `EntryPointType=”Main”`. Adja meg **SetupEntryPoint** akkor különösen akkor hasznos, ha bizonyos magas szintű jogosultságokat igénylő telepítési műveletekhez a rendszerfiók futtatja. További információkért lásd: [szolgáltatás indítási parancsfájlt futtató helyi felhasználói vagy rendszer fiók](service-fabric-run-script-at-service-startup.md). A tényleges kódját egy alacsonyabb szintű szolgáltatásfiók alatt futhatnak.

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>Minden szolgáltatás kód csomagok egy alapértelmezett házirend alkalmazása
Használja a **DefaultRunAsPolicy** szakaszban adja meg az összes kód alapértelmezett felhasználói fiók csomagok, amelyeket nem kell egy adott **RunAsPolicy** definiálva. Ha a szolgáltatás egy alkalmazás által használt jegyzékfájlban megadott kód csomagokat a legtöbb kell futni ugyanahhoz a felhasználóhoz, az alkalmazás csak definiálhat egy alapértelmezett RunAs házirendet, hogy felhasználói fiókkal. Az alábbi példa azt jelenti, hogy ha a kódcsomag nem rendelkezik egy **RunAsPolicy** megadva, a kódcsomag kell futnia a **MyDefaultAccount** rendszerbiztonsági tagok szakaszában megadott.

```xml
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>További lépések
* [Az alkalmazásmodell ismertetése](service-fabric-application-model.md)
* [Adja meg a szolgáltatás jegyzék erőforrások](service-fabric-service-manifest-resources.md)
* [Alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
