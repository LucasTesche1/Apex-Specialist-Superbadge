global with sharing class WarehouseSyncSchedule implements Schedulable{
    global void execute (SchedulableContext context){
        System.enqueueJob(new WarehouseCalloutService());
    }
}
