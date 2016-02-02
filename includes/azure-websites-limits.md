 資源| 免費| 共用 (預覽)| 基本| 標準| 高階 (預覽)</th>
---|---|---|---|---|---
 [Web, mobile, or API apps](../services/app-service/) per [App Service plan](web-sites-web-hosting-plan-overview.md)<sup>1</sup>| 10| 100| 無限制<sup>2</sup>| 無限制<sup>2</sup>| 無限制<sup>2</sup>
 [Logic apps](../services/app-service/) per [App Service plan](web-sites-web-hosting-plan-overview.md)</a><sup>1</sup>| 10| 10| 10| 每個核心 20 個| 每個核心 20 個
 [應用程式服務方案](web-sites-web-hosting-plan-overview.md)| 每個區域 1 個| 每個資源群組 10 個| 每個資源群組 10 個| 每個資源群組 10 個| 每個資源群組 10 個
 計算執行個體類型| 共用| 共用| 專用<sup>3</sup>| 專用<sup>3</sup>| 專用<sup>3</sup></p>
 [向外延展](web-sites-scale.md) (執行個體上限)| 1 個共用| 1 個共用| 3 個專用<sup>3</sup>| 10 個專用<sup>3</sup>| 50 個專用<sup>3、 4</sup>
 儲存體<sup>5</sup>| 1 GB<sup>5</sup>| 1 GB<sup>5</sup>| 10 GB<sup>5</sup>| 50 GB<sup>5</sup>| 500 GB<sup>4，5</sup></p>
 CPU 時間 (天)<sup>6</sup>| 60 Minuten| 240 Minuten| 無限制、 付費標準 [率](../pricing/details/app-service/)</a>| 無限制，以標準費率付費| 無限制，以標準費率付費
 記憶體 (1 小時)| 每個 App Service 方案 1024 MB| 每個應用程式 1024 MB| N/A| N/A| N/A
 頻寬| 165 MB| 無限制， [資料傳輸費率](../pricing/details/data-transfers/) 套用| 無限制，套用資料傳輸費率| 無限制，套用資料傳輸費率| 無限制，套用資料傳輸費率
 應用程式架構| 32 位元| 32 位元| 32 位元/64 位元| 32 位元/64 位元| 32 位元/64 位元
 每個執行個體 web 通訊端<sup>7</sup>| 5| 35| 350| 無限| 無限
 並行 [偵錯工具連接](web-sites-dotnet-troubleshoot-visual-studio.md) 每個應用程式| 1| 1| 1| 5| 5
 [搭配 FTP/S 和 SSL 的 azurewebsites.net 子網域](web-sites-configure-ssl-certificate.md)| X| X| X| X| X
 [自訂網域](web-sites-custom-domain-name.md) 支援| | X| X| X| X
 自訂網域 [SSL 支援](web-sites-configure-ssl-certificate.md)| | | 無限| 無限制，包含 5 個 SNI SSL 和 1 個 IP SSL 連接| 無限制，包含 5 個 SNI SSL 和 1 個 IP SSL 連接
 整合式負載平衡器| | X| X| X| X
 [永遠開啟](web-sites-configure.md)| | | X| X| X
 [排定的備份](web-sites-backup.md)| | | | 一天一次| 一次每隔 5 分鐘<sup>8</sup>
 [自動調整大小](web-sites-scale.md)| | | X| X| X
 [WebJobs](web-sites-create-web-jobs.md)<sup>9</sup>| X| X| X| X| X
 [Azure 排程器](../services/scheduler/) 支援| | X| X| X| X
 [端點監視](web-sites-monitor.md)| | | X| X| X
 [預備位置 (預覽)](web-sites-staged-publishing.md)| | | | 5| 20
 每個應用程式的自訂網域</a>| | 500| 500| 500| 500
 SLA| | <p>| 99.9%| 99.95%<sup>10</sup>| 99.95%<sup>10</sup>

<sup>1</sup>應用程式和儲存體配額是以每個應用程式服務方案除非另有指示。  
<sup>2</sup>您可以在這些電腦裝載的應用程式的實際數目取決於應用程式的活動、 機器執行個體和對應的資源使用率的大小。  
<sup>3</sup>專用執行個體可以是不同的大小。 請參閱 [App Service 定價](/pricing/details/app-service/) 如需詳細資訊。 您可透過提出支援要求，來取得額外的執行個體。  
<sup>4</sup>高階層允許最多 50 計算執行個體 (受限於可用性) 和 500 GB 的磁碟空間時使用 App Service 環境，20 可計算執行個體和 250 GB 的儲存體否則。  
<sup>5</sup>儲存體限制是跨所有應用程式中的總內容大小 
相同的應用程式服務方案。 您可透過提出支援要求來提高儲存體限制。  
<sup>6</sup>這些資源會受限於專用執行個體 (執行個體大小和執行個體數目) 上的實體資源。  
<sup>7</sup>基本層兩個執行個體中調整應用程式時，如果您有兩個執行個體的每個 350 個並行連線。  
<sup>8</sup>高階層允許備份間隔下調每隔 5 分鐘，當您使用 App Service 環境，而 50 次，否則每天。  
<sup>9</sup>隨選、 依照排程執行自訂可執行檔和/或指令碼應用程式服務中的背景工作執行個體或連續。 若要連續執行 WebJobs，「永遠開啟」是必要選項。 若是排程 WebJobs，則 Azure 排程器免費或標準版本是必要項目。  
<sup>10</sup>容錯移轉使用多個執行個體使用 Azure 流量管理員設定的部署所提供的 99.95%的 SLA。





