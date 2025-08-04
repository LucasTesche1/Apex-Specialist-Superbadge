@isTest
public with sharing class WarehouseSyncScheduleTest {
    @isTest static void WarehouseScheduleTest(){        
        String scheduleTime = '00 00 01 * * ?';
        Test.startTest();
        Test.setMock(HttpCalloutMock.class, new WareHouseCalloutServiceMock());
        String jobID = System.schedule('Warehouse Time To Schedule to Test', scheduleTime, new WarehouseSyncSchedule());
        Test.stopTest();
        CronTrigger a = [SELECT Id FROM CronTrigger WHERE NextFireTime > today];
        System.assertEquals(jobID, a.Id, 'Schedule');
    }
}
