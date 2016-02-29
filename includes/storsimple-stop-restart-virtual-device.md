#### 停止和啟動虛擬裝置
若要停止虛擬裝置，請按一下其名稱，以及 [ **關機**。 當虛擬裝置正在關閉時，其狀態會是 **停止**。 虛擬裝置停止之後，其狀態會是 **已停止**。

請使用下列 Cmdlet 來停止和啟動虛擬裝置。

`Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`


`Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`
    
#### 重新啟動虛擬機器

當虛擬裝置正在執行，而且您想要重新啟動它時，按一下其名稱，然後按一下 **重新啟動**。 當虛擬裝置重新啟動時，其狀態會是 **正在重新啟動**。 準備好要使用虛擬裝置時，其狀態會是 **執行**。

請使用下列 Cmdlet 來重新啟動虛擬裝置。

`Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`





