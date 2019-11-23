---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11/21/2019
ms.openlocfilehash: f583796fc353852ef3898e28fa96524e08cfb4ad
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74414480"
---
When you commit to an Azure reserved VM instance you can save money. The reservation discount is applied automatically to the number of running virtual machines that match the reservation scope and attributes. You don't need to assign a reservation to a virtual machine to get the discounts. A reserved instance purchase covers only the compute part of your VM usage. For Windows VMs, the usage meter is split into two separate meters. There's a compute meter, which is same as the Linux meter, and a Windows IP meter. The charges that you see when you make the purchase are only for the compute costs. Charges don't include Windows software costs. For more information about software costs, see [Software costs not included with Azure Reserved VM Instances](../articles/billing/billing-reserved-instance-windows-software-costs.md).

## <a name="determine-the-right-vm-size-before-you-buy"></a>Determine the right VM size before you buy

Before you buy a reservation, you should determine the size of the VM that you need. The following sections will help you determine the right VM size.

### <a name="use-reservation-recommendations"></a>Use reservation recommendations

You can use reservation recommendations to help determine the reservations you should purchase.

- Purchase recommendations and recommended quantity are show when you purchase a VM reserved instance in the Azure portal.
- Azure Advisor provides purchase recommendations for individual subscriptions.  
- You can use the APIs to get purchase recommendations for both shared scope and single subscription scope. For more information, see [Reserved instance purchase recommendation APIs for enterprise customers](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- For Enterprise Agreement (EA) and Microsoft Customer Agreement (MCA) customers, purchase recommendations for shared and single subscription scopes are available with the [Azure Consumption Insights Power BI content pack](/power-bi/service-connect-to-azure-consumption-insights).

### <a name="services-that-get-vm-reservation-discounts"></a>A virtuálisgép-foglalási kedvezményekre jogosult szolgáltatások

A virtuálisgép-foglalások a több szolgáltatás általi virtuálisgép-használatra is alkalmazhatók, nem csupán a virtuális gépek üzemelő példányaira. A foglalási kedvezményekre jogosult erőforrások a példány méretének rugalmassági beállításától függően változnak.

#### <a name="instance-size-flexibility-setting"></a>Példány méretrugalmassági beállítása

A példány méretrugalmassági beállítása határozza meg, hogy mely szolgáltatásokra vonatkozik a fenntartott példányokhoz kapcsolódó kedvezmény.

Ha a *ConsumedService* értéke `Microsoft.Compute`, a foglalási kedvezmények automatikusan érvényesülnek minden megfelelő virtuálisgép-használat esetében, attól függetlenül, hogy a beállítás be vagy ki van-e kapcsolva. Ebből kifolyólag ellenőrizze a használati adatait a *ConsumedService* értékének megtekintéséhez. Néhány példa:

- Virtual machines (Virtuális gépek)
- Virtual Machine Scale Sets
- Container Service
- Azure Batch-üzemelőpéldányok (felhasználói előfizetési módban)
- Azure Kubernetes Service (AKS)
- Service Fabric

Ha a *ConsumedService* az alábbiak egyike, a foglalási kedvezmények automatikusan érvényesülnek minden megfelelő virtuálisgép-használat esetében, attól függetlenül, hogy a beállítás be vagy ki van-e kapcsolva:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

Ellenőrizze a *ConsumedService* értékét a használati adatokban annak megállapításához, hogy a használatra alkalmazhatók-e a foglalási kedvezmények.

A példányok méretrugalmasságára vonatkozó további információkért tekintse meg [a Reserved VM Instances virtuális gépeinek méretrugalmasságát](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md) ismertető cikket.

### <a name="analyze-your-usage-information"></a>Analyze your usage information
Analyze your usage information to help determine which reservations you should purchase.

Usage data is available in the usage file and APIs. Use them together to determine which reservation to purchase. Check for VM instances that have high usage on daily basis to determine the quantity of reservations to purchase.

Avoid the `Meter` subcategory and `Product` fields in usage data. They don't distinguish between VM sizes that use premium storage. If you use these fields to determine the VM size for reservation purchase, you may buy the wrong size. Then you won't get the reservation discount you expect. Instead, refer to the `AdditionalInfo` field in your usage file or usage API to determine the correct VM size.

### <a name="purchase-restriction-considerations"></a>Purchase restriction considerations

Reserved VM Instances are available for most VM sizes with some exceptions. Reservation discounts don't apply for the following VMs:

- **VM series** - A-series, Av2-series, or G-series.

- **Preview or Promo VMs** - Any VM-series or size that is in preview or uses promotional meter.

- **Clouds** - Reservations aren't available for purchase in Germany or China regions.

- **Insufficient quota** - A reservation that is scoped to a single subscription must have vCPU quota available in the subscription for the new RI. For example, if the target subscription has a quota limit of 10 vCPUs for D-Series, then you can't buy a reservation for 11 Standard_D1 instances. The quota check for reservations includes the VMs already deployed in the subscription. For example, if the subscription has a quota of 10 vCPUs for D-Series and has two standard_D1 instances deployed, then you can buy a reservation for 10 standard_D1 instances in this subscription. You can [create quote increase request](../articles/azure-supportability/resource-manager-core-quotas-request.md) to resolve this issue.

- **Capacity restrictions** - In rare circumstances, Azure limits the purchase of new reservations for subset of VM sizes, because of low capacity in a region.

## <a name="buy-a-reserved-vm-instance"></a>Buy a Reserved VM Instance

You can buy a reserved VM instance in the [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D). A foglalásért fizethet [előre vagy havi részletekben](../articles/billing/billing-monthly-payments-reservations.md).
These requirements apply to buying a reserved VM instance:

- You must be in an Owner role for at least one EA subscription or a subscription with a pay-as-you-go rate.
- For EA subscriptions, the **Add Reserved Instances** option must be enabled in the [EA portal](https://ea.azure.com/). Or, if that setting is disabled, you must be an EA Admin for the subscription.
- For the Cloud Solution Provider (CSP) program, only the admin agents or sales agents can buy reservations.

To buy an instance:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
1. Válassza a **Minden szolgáltatás** > **Reservations** lehetőséget.
1. Select **Add** to purchase a new reservation and then click **Virtual machine**.
1. Töltse ki a kötelező mezőket. A foglalási kedvezményre a kiválasztott attribútumoknak megfelelő, futó virtuálisgép-példányok jogosultak. A kedvezményt megkapó virtuálisgép-példányok tényleges száma a kiválasztott hatókörtől és mennyiségtől függ.

If you have an EA agreement, you can use the **Add more option** to quickly add additional instances. The option isn't available for other subscription types.


| Mező      | Leírás|
|------------|--------------|
|Előfizetés|The subscription used to pay for the reservation. The payment method on the subscription is charged the costs for the reservation. The subscription type must be an enterprise agreement (offer numbers: MS-AZR-0017P or MS-AZR-0148P) or Microsoft Customer Agreement or an individual subscription with pay-as-you-go rates (offer numbers: MS-AZR-0003P or MS-AZR-0023P). The charges are deducted from the monetary commitment balance, if available, or charged as overage. For a subscription with pay-as-you-go rates, the charges are billed to the credit card or invoice payment method on the subscription.|    
|Hatókör       |The reservation’s scope can cover one subscription or multiple subscriptions (shared scope). If you select: <ul><li>**Egyetlen erőforráscsoport hatókör** – A foglalási kedvezményt csak a kiválasztott erőforráscsoportban található egyező erőforrásokra alkalmazza.</li><li>**Egy előfizetésre kiterjedő hatókör** – A foglalási kedvezményt a kiválasztott előfizetésben található, egyező erőforrásokra alkalmazza.</li><li>**Megosztott hatókör** – A foglalási kedvezményt a számlázási környezet jogosult előfizetéseiben található, egyező erőforrásokra alkalmazza. For EA customers, the billing context is the enrollment. A használatalapú díjas, egyéni előfizetések esetében a számlázási hatókör a fiókadminisztrátor által létrehozott, jogosult előfizetéseket foglalja magában.</li></ul>|
|Region (Régió)    |The Azure region that’s covered by the reservation.|    
|Virtuális gép mérete     |The size of the VM instances.|
|Optimize for     |VM instance size flexibility is selected by default. Click **Advanced settings** to change the instance size flexibility value to apply the reservation discount to other VMs in the same [VM size group](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md). A kapacitás prioritása kiemelt fontosságúként kezeli az adatközpont kapacitását az üzemelő példányok számára. It offers additional confidence in your ability to launch the VM instances when you need them. Capacity priority is only available when the reservation scope is single subscription. |
|Időtartam        |One year or three years.|
|Mennyiség    |The number of instances being purchased within the reservation. The quantity is the number of running VM instances that can get the billing discount. For example, if you are running 10 Standard_D2 VMs in the East US, then you would specify quantity as 10 to maximize the benefit for all running VMs. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>Usage data and reservation utilization

A használati adatok ára nulla azon használat esetében, amelyre érvényes a foglalási kedvezmény. You can see which VM instance received the reservation discount for each reservation.

For more information about how reservation discounts appear in usage data, see [Understand Azure reservation usage for your Enterprise enrollment](../articles/billing/billing-understand-reserved-instance-usage-ea.md) if you are an EA customer. If you have an individual subscription, see [Understand Azure reservation usage for your Pay-As-You-Go subscription](../articles/billing/billing-understand-reserved-instance-usage.md).

## <a name="change-a-reservation-after-purchase"></a>Change a reservation after purchase

A vásárlás után a következő típusú módosításokat hajthatja végre a foglalásokon:

- Foglalás hatókörének frissítése
- Instance size flexibility (if applicable)
- Ownership

You can also split a reservation into smaller chunks and merge already split reservations. None of the changes cause a new commercial transaction or change the end date of the reservation.

You can't make the following types of changes after purchase, directly:

- An existing reservation’s region
- SKU (Cikkszám)
- Mennyiség
- Időtartam

However, you can *exchange* a reservation if you want to make changes.

## <a name="cancel-exchange-or-refund-reservations"></a>Foglalások lemondása, cseréje vagy visszatérítése

Bizonyos korlátozásokkal lehetősége van a foglalások lemondására, cseréjére és visszatérítésére. További információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](../articles/billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Következő lépések

- A foglalások kezelésének megismerése érdekében tekintse meg [Az Azure Reservations kezelése](../articles/billing/billing-manage-reserved-vm-instance.md) szakaszt.
- Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:
    - [Mi az az Azure Reservations?](../articles/billing/billing-save-compute-costs-reservations.md)
    - [A Reservations kezelése az Azure-ban](../articles/billing/billing-manage-reserved-vm-instance.md)
    - [A foglalási kedvezmény alkalmazásának ismertetése](../articles/billing/billing-understand-vm-reservation-charges.md)
    - [A foglalási használat ismertetése használatalapú fizetéses előfizetés esetén](../articles/billing/billing-understand-reserved-instance-usage.md)
    - [A foglalási kihasználtság ismertetése vállalati regisztrációnál](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
    - [A Reservations díjában nem szereplő Windows-szoftverköltségek](../articles/billing/billing-reserved-instance-windows-software-costs.md)
    - [Azure Reservations a Partner Center felhőszolgáltatói (CSP) programjában](https://docs.microsoft.com/partner-center/azure-reservations)
