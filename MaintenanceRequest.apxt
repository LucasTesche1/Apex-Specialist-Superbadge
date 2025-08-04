trigger MaintenanceRequest on Case (before update, after update) {
    //Gatilho de para lançar função quando sistema notar atualização ou inserção dentro de Case
    if(Trigger.isUpdate && Trigger.isAfter){
        MaintenanceRequestHelper.updateWorkOrders(Trigger.New, Trigger.OldMap);
    }
}
