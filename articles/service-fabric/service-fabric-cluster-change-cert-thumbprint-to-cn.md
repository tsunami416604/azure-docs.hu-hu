---
title: Fürt frissítése a tanúsítvány köznapi nevének használatára
description: Megtudhatja, hogyan alakíthat át egy Service Fabric-fürtöt az ujjlenyomat-alapú deklarációból a köznapi nevekre.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 224798565921593d3c91dfcc187efa71a71b1fdd
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368060"
---
# <a name="convert-cluster-certificates-from-thumbprint-based-declarations-to-common-names"></a>Fürtözött tanúsítványok konvertálása az ujjlenyomat-alapú deklarációk és köznapi nevek között
A tanúsítvány aláírása (az "ujjlenyomat" néven ismert) egyedi, ami azt jelenti, hogy az ujjlenyomattal deklarált fürtcsomópont a tanúsítvány egy adott példányára hivatkozik. Ez lehetővé teszi a tanúsítványok átváltását és kezelését, általánosságban nehéz és explicit módon: minden változáshoz a fürt és a mögöttes számítástechnikai gazdagépek frissítésének előkészítésére van szükség. A Service Fabric-fürt tanúsítvány-deklarációinak ujjlenyomat alapján történő átalakítása a tanúsítvány tulajdonosának köznapi neve alapján leegyszerűsíti a felügyeletet – különösen, ha a tanúsítvány a továbbiakban nem szükséges a fürt frissítéséhez. Ez a cikk azt ismerteti, hogyan alakíthat át egy meglévő fürtöt általános név alapú deklarációra állásidő nélkül.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="moving-to-certificate-authority-ca-signed-certificates"></a>Áthelyezés a hitelesítésszolgáltatótól (CA) – aláírt tanúsítványok
Annak a fürtnek a biztonsága, amelynek a tanúsítványa ujjlenyomattal van ellátva, az a tény, hogy lehetetlen, vagy a számítási módon nem valósítható meg egy olyan tanúsítvány hamisítása, amely ugyanazzal az aláírással rendelkezik, mint egy másik. Ebben az esetben a tanúsítvány bebizonyította kevésbé fontos, ezért az önaláírt tanúsítványok megfelelőek. Ezzel szemben a közös név által deklarált tanúsítványokkal rendelkező fürtök biztonsága a tanúsítványt kiállító nyilvános kulcsú infrastruktúra szolgáltatástól (PKI) származik, és olyan szempontokat is magában foglal, mint például a tanúsítási eljárások, függetlenül attól, hogy az operatív biztonságot naplózzák-e, és sok más. Emiatt a PKI megválasztása fontos, a kiállítók (hitelesítésszolgáltató vagy CA) meghitt ismerete szükséges, és az önaláírt tanúsítványok alapvetően értéktelenek. A köznapi név (CN) által deklarált tanúsítvány általában akkor minősül érvényesnek, ha a lánca sikeresen felépíthető, a tulajdonosnak a várt CN-elemet kell használnia, és a kiállító (a lánc közvetlen vagy magasabb szintjének) megbízhatónak tekinti az ellenőrzést végző ügynök. Service Fabric támogatja a tanúsítványoknak a CN által "implicit" kiállítóval való deklarálása (a láncnak egy megbízhatósági kapcsolati horgonyban kell végződnie), vagy az ujjlenyomattal deklarált kibocsátók ("kiállítói rögzítés"); További részletekért tekintse meg ezt a  [cikket](cluster-security-certificates.md#common-name-based-certificate-validation-declarations) . Ha a fürtöt egy olyan önaláírt tanúsítvánnyal szeretné átalakítani, amely az ujjlenyomatot a köznapi név értékre állította be, akkor a cél, a CA által aláírt tanúsítványt először ujjlenyomattal kell bevezetni a fürtbe. csak ezután lehetséges a TP-ből a CN-re való átalakítás.

Tesztelési célból az önaláírt tanúsítványokat a CN deklarálhatja, a kibocsátót a saját ujjlenyomatára rögzítheti. biztonsági szempontból ez közel azonos a tanúsítvány TP-vel való deklarálása esetén. Vegye figyelembe azonban, hogy az ilyen jellegű sikeres átalakítás nem garantálja a TP-ről a CN-re való sikeres átalakítást a CA által aláírt tanúsítvánnyal. Ezért javasoljuk, hogy tesztelje a konverziót egy megfelelő, HITELESÍTÉSSZOLGÁLTATÓ által aláírt tanúsítvánnyal (az ingyenes lehetőségek léteznek).

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Töltse fel a tanúsítványt, és telepítse a méretezési csoportba
Az Azure-ban a tanúsítványok beszerzéséhez és üzembe helyezéséhez ajánlott módszer a Azure Key Vault szolgáltatás és annak eszközei. A fürtbe tartozó virtuálisgép-méretezési csoportok minden csomópontja számára ki kell építeni a tanúsítványnak megfelelő tanúsítványt. További részletekért tekintse [meg a virtuális gépek méretezési csoportjain található titkokat](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-securely-ship-a-certificate-to-the-vm) . Fontos, hogy a fürt összes csomópont-típusának módosítása előtt a jelenlegi és a célként megadott fürtcsomópontok is telepítve legyenek a virtuális gépeken. A tanúsítvány kiállításának a Service Fabric-csomópontra való kiépítési menetét [itt](cluster-security-certificate-management.md#the-journey-of-a-certificate)részletesen tárgyaljuk.

## <a name="bring-cluster-to-an-optimal-starting-state"></a>Fürt optimális indítási állapotba hozása
Tanúsítvány-nyilatkozat konvertálása ujjlenyomat alapján a köznapi név alapú hatásokra:

- A fürt minden csomópontja megkeresi és megjeleníti a hitelesítő adatait más csomópontok számára
- Hogyan érvényesíti az egyes csomópontok hitelesítő adatait biztonságos kapcsolat létesítése után  

A folytatás előtt tekintse át [mindkét konfiguráció megjelenítési és ellenőrzési szabályait](cluster-security-certificates.md#certificate-configuration-rules) . Az ujjlenyomatok köznapi nevének átalakításakor a legfontosabb szempont, hogy a frissített és még nem frissített csomópontok (azaz a különböző frissítési tartományokhoz tartozó csomópontok) a frissítés során bármikor képesek legyenek sikeres kölcsönös hitelesítés végrehajtására. Ennek eléréséhez ajánlott a cél/cél tanúsítvány ujjlenyomattal való deklarálása egy kezdeti frissítés során, és egy későbbi, a köznapi névre való áttérés befejezése. Ha a fürt már egy javasolt indítási állapotban van, akkor ez a szakasz kihagyható.

Egy átalakításhoz több érvényes indítási állapot van; a Invariant az, hogy a fürt már használja a cél tanúsítványt (az ujjlenyomattal deklarálva) a frissítés elején a köznapi névre. A `GoalCert` következőket vesszük `OldCert1` figyelembe `OldCert2` :

#### <a name="valid-starting-states"></a>Érvényes indítási állapotok
- `Thumbprint: GoalCert, ThumbprintSecondary: None`
- `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, ahol `GoalCert` a későbbi, `NotAfter` mint `OldCert1`
- `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, ahol `GoalCert` a későbbi, `NotAfter` mint `OldCert1`

Ha a fürt nem szerepel a fent ismertetett érvényes állapotok valamelyikében, tekintse meg a jelen cikk végén található, az állam megvalósításáról szóló függeléket.

## <a name="select-the-desired-common-name-based-certificate-validation-scheme"></a>Válassza ki a kívánt common-name-alapú tanúsítvány-ellenőrzési sémát
Az előzőekben leírtak szerint a Service Fabric támogatja a tanúsítványok deklarálása a CN-ban implicit megbízhatósági kapcsolattal vagy explicit módon a kiállítói ujjlenyomatai megfelelnek. További részletekért tekintse meg [ezt a cikket](cluster-security-certificates.md#common-name-based-certificate-validation-declarations) , és győződjön meg arról, hogy jól ismeri a különbségeket, és hogy milyen következményekkel jár a mechanizmus kiválasztása. Ennek a különbségnek/döntésnek a meghatározása a következő paraméter értéke alapján történik: az üres érték a `certificateIssuerThumbprintList` megbízható legfelső szintű hitelesítésszolgáltatóra (megbízhatósági kapcsolatra) támaszkodik, míg a ujjlenyomatai megfelelnek-készlet korlátozza a fürtözött tanúsítványok engedélyezett közvetlen kibocsátóit.

   > [!NOTE]
   > A "certificateIssuerThumbprint" mező lehetővé teszi a tulajdonos köznapi neve által deklarált tanúsítványok várt közvetlen kiállítók megadását. Az elfogadható értékek egy vagy több vesszővel tagolt SHA1-ujjlenyomatai megfelelnek. Vegye figyelembe, hogy ez a tanúsítvány érvényesítésének megerősítése – ha nincsenek megadva a kiállítók, vagy a lista üres, akkor a rendszer a tanúsítványt akkor fogadja el hitelesítésre, ha a lánca felépíthető, és a validator által megbízhatónak tartott legfelső szinttel végződik. Ha meg van adva egy vagy több kiállító ujjlenyomatai megfelelnek, a rendszer elfogadja a tanúsítványt, ha a közvetlen kiállítójának a láncból kinyert ujjlenyomata megegyezik az ebben a mezőben megadott értékekkel, függetlenül attól, hogy a gyökér megbízható-e. Vegye figyelembe, hogy a nyilvános kulcsokra épülő infrastruktúra a tanúsítványoknak egy adott tárgyhoz való aláírására különböző hitelesítésszolgáltatók ("kiállítók") is használhatók, ezért fontos, hogy az adott tárgyhoz tartozó összes várható kiállítói ujjlenyomatai megfelelnek meg lehessen adni. Más szóval a tanúsítvány megújítása nem garantált, hogy ugyanazt a kiállítót írja alá, mint a megújított tanúsítvány.
   >
   > A kibocsátó meghatározása ajánlott eljárásnak minősül; Ha kihagyja, továbbra is működni fog – a megbízható legfelső szintű tanúsítványok esetében – ez a viselkedés korlátozásokkal jár, és a közeljövőben fokozatosan elvégezhető. Azt is vegye figyelembe, hogy az Azure-ban üzembe helyezett fürtök, valamint a privát PKI által kiadott és a tulajdonos által deklarált X509-tanúsítványokkal védettek, az Azure Service Fabric szolgáltatás nem tudja érvényesíteni (a fürtök közötti kommunikációhoz), ha a PKI tanúsítvány-házirendje nem felderíthető, elérhető és hozzáférhető. 

## <a name="update-the-clusters-azure-resource-management-arm-template-and-deploy"></a>A fürt Azure Resource Management-(ARM-) sablonjának frissítése és üzembe helyezése
Az Azure Service Fabric-fürtök ARM-sablonokkal való kezelése ajánlott. a JSON-összetevőket is használó alternatíva a [Azure erőforrás-kezelő (előzetes verzió)](https://resources.azure.com). Jelenleg nem érhető el megfelelő felület a Azure Portal. Ha egy meglévő fürthöz tartozó eredeti sablon nem érhető el, akkor a Azure Portal a fürtöt tartalmazó erőforráscsoporthoz való navigálással egyenértékű sablont lehet beolvasni, majd kiválasztja a **sablon exportálása** lehetőséget az **Automation** bal oldali menüjéből, majd a kívánt erőforrások kiválasztásával. a virtuálisgép-méretezési csoportnak és a fürt erőforrásainak legalább a exportálását el kell vinni. A létrehozott sablon is letölthető. Megjegyzés: Ez a sablon a teljes üzembe helyezése előtt változhat, és előfordulhat, hogy nem felel meg pontosan az eredetinek – a fürterőforrás aktuális állapotát tükrözi.

A szükséges módosítások a következők:
    - a Service Fabric csomópont-bővítmény definíciójának frissítése (a virtuális gép erőforrása alatt); Ha a fürt több csomópont-típust határoz meg, akkor frissítenie kell a megfelelő virtuálisgép-méretezési csoport definícióját.
    - a fürterőforrás-definíció frissítése

A részletes példák alább találhatók.

### <a name="updating-the-virtual-machine-scale-set-resources"></a>A virtuálisgép-méretezési csoport erőforrás (ok) frissítése
Forrás
```json
"virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            ...
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            ...
                            "certificate": {
                                "thumbprint": "[parameters('certificateThumbprint')]",
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        ...
                    }
                },
```
Művelet
```json
"virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            ...
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            ...
                            "certificate": {
                                "commonNames": [
                                    "[parameters('certificateCommonName')]"
                                ],
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        ...
                    }
                },
```

### <a name="updating-the-cluster-resource"></a>A fürterőforrás frissítése
A **Microsoft. ServiceFabric/Clusters** erőforrásban adjon hozzá egy **certificateCommonNames** tulajdonságot egy **commonNames** -beállítással, és távolítsa el a **tanúsítvány** tulajdonságot (az összes beállítását):

Forrás
```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            ...
        ],
        "properties": {
            "addonFeatures": [
                ...
            ],
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
```
Művelet
```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            ...
        ],
        "properties": {
            "addonFeatures": [
                ...
            ],
            "certificateCommonNames": {
                "commonNames": [
                    {
                        "certificateCommonName": "[parameters('certificateCommonName')]",
                        "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprintList')]"
                    }
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
```

További információ: Service Fabric- [fürt üzembe helyezése, amely az ujjlenyomat helyett a tanúsítvány köznapi nevét használja.](./service-fabric-create-cluster-using-cert-cn.md)

## <a name="deploy-the-updated-template"></a>A frissített sablon üzembe helyezése
A módosítások végrehajtása után telepítse újra a frissített sablont.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="appendix-achieve-a-valid-starting-state-for-converting-a-cluster-to-cn-based-certificate-declarations"></a>Függelék: a fürtnek a CN-alapú tanúsítványok deklarációjában való átalakításához szükséges érvényes indítási állapot elérése

| Indítás állapota | 1. frissítés | 2. frissítés |
| :--- | :--- | :--- |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` és `GoalCert` egy későbbi `NotAfter` dátummal rendelkezik, mint `OldCert1` | `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` és `OldCert1` egy későbbi `NotAfter` dátummal rendelkezik, mint `GoalCert` | `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1` | `Thumbprint: GoalCert, ThumbprintSecondary: None` |
| `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, ahol `OldCert1` egy későbbi `NotAfter` dátummal rendelkezik, mint `GoalCert` | Frissítés erre `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, ahol `OldCert1` egy későbbi `NotAfter` dátummal rendelkezik, mint `GoalCert` | Frissítés erre `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: OldCert2` | Az egyik `OldCert1` vagy az `OldCert2` állapot eltávolítása `Thumbprint: OldCertx, ThumbprintSecondary: None` | Folytatás az új kezdő állapotból |

A frissítések bármelyikének végrehajtásával kapcsolatos útmutatásért tekintse meg [ezt a dokumentumot](service-fabric-cluster-security-update-certs-azure.md).


## <a name="next-steps"></a>Következő lépések
* További információ a [fürt biztonságáról](service-fabric-cluster-security.md).
* Megtudhatja, hogyan válthat át [egy fürtözött tanúsítványt köznapi név szerint](service-fabric-cluster-rollover-cert-cn.md)
* Megtudhatja, hogyan [konfigurálhat egy fürtöt az érintéses autorolloverhez](cluster-security-certificate-management.md)

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
