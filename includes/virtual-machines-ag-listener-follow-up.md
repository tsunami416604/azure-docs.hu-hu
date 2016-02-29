建立可用性群組接聽程式之後，可能需要調整 **RegisterAllProvidersIP** 和 **HostRecordTTL** 叢集接聽程式資源參數。  這些參數可縮短容錯移轉可能導致連線逾時之後的重新連線時間。 如需有關這些參數，以及範例程式碼的詳細資訊，請參閱 [建立或設定可用性群組接聽程式](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover)。

