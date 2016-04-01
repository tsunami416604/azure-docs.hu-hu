<properties
   pageTitle="Azure 備份限制資料表"
   description="描述 Azure 備份的系統限制。"
   services="backup"
   documentationCenter="NA"
   authors="Jim-Parker"
   manager="jwhit"
   editor="" />
<tags
   ms.service="backup"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="10/05/2015"
   ms.author="trinadhk";"jimpark"; "aashishr" />


下列限制適用於 Azure 備份。

| 限制識別碼 | 預設限制 |
|---|---|
|可針對每個保存庫註冊的伺服器/電腦數目|Windows Server/用戶端 /SCDPM 50 <br/> IaaS Vm 的 200|
|在 Azure 保存庫儲存體中所儲存資料的資料來源大小|最大 54400 GB<sup>1</sup>|
|可以在每個 Azure 訂用帳戶中建立的備份保存庫的數目|25|
|每日可以排程的備份次數|每日的 Windows Server/用戶端 3 <br/> SCDPM 每天 2 <br/> 一次，即一天|
|連接到 Azure 虛擬機器進行備份的資料磁碟|16|

- <sup>1</sup>54400 GB 上限並不適用於 IaaS VM 備份。



