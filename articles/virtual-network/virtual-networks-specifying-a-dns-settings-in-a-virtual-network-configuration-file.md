<properties 
   pageTitle="指定虛擬網路組態檔中的 DNS 設定 | Microsoft Azure"
   description="如何使用虛擬網路組態檔變更 DNS 伺服器設定"
   services="virtual-network"
   documentationCenter="na"
   authors="joaoma"
   manager="jdial"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/25/2015"
   ms.author="joaoma" />

# 指定虛擬網路組態檔中的 DNS 設定

網路組態檔有兩個項目可讓您指定網域名稱系統 (DNS) 設定: **DnsServers** 和 **DnsServerRef**。 您可以藉由指定其 IP 位址加入 DNS 伺服器清單，並參考名稱加入 **DnsServers** 項目。 然後您可以使用 **DnsServerRef** 項目來指定用於虛擬網路內的不同網站的 DNS 伺服器項目，從 DnsServers 項目。

>[AZURE.IMPORTANT] 如需如何設定網路設定檔資訊，請參閱 [虛擬網路使用網路組態檔設定](virtual-networks-using-network-configuration-file.md)。 如需網路組態檔中包含每個項目資訊，請參閱 [Azure 虛擬網路組態結構描述](https://msdn.microsoft.com/library/azure/jj157100.aspx)。

網路組態檔可能包含下列項目。 每個項目的標題會連結至提供項目值設定之其他相關資訊的網頁。

[Dns 項目](http://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

>[AZURE.WARNING]  **名稱** 屬性中 **DnsServer** 項目做為參考 **DnsServerRef** 項目。 它不代表 DNS 伺服器的主機名稱。 每個 **DnsServer** 屬性值必須是唯一的整個 Microsoft Azure 訂用帳戶

[Virtual Network Sites 項目](http://go.microsoft.com/fwlink/?LinkId=248093)

    <DnsServersRef>
      <DnsServerRef name="ID1" />
      <DnsServerRef name="ID2" />
      <DnsServerRef name="ID3" />
    </DnsServersRef>

>[AZURE.NOTE] 若要指定此設定，Virtual Network Sites 項目，它必須先前定義的 DNS 項目中。 DnsServerRef *名稱* dnsserver DNS 項目中指定的名稱值必須參考 Virtual Network Sites 項目 *名稱*。

## 後續步驟

[使用網路組態檔設定虛擬網路](virtual-networks-using-network-configuration-file.md)

[Azure 虛擬網路組態結構描述](http://go.microsoft.com/fwlink/?LinkId=248093)

[Azure 服務組態結構描述](https://msdn.microsoft.com/library/windowsazure/ee758710)

