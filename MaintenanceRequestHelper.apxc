public with sharing class MaintenanceRequestHelper {
    
    public static void updateworkorders(List<Case> updWorkOrders, Map<Id,Case> nonUpdCaseMap) {     
        //Cria lista de Ids que condizem com as condições
        Set<Id> validIds = new Set<Id>();  
        //Itera sobre os pedidos
        for (Case c : updWorkOrders){  
            //Verifica se o status original é not closed e o atual é closed
            if(nonUpdCaseMap.get(c.Id).Status != 'Closed' && c.Status == 'Closed')
            {
            //Se sim, verifica se o Tipo do pedido é Repair ou Routine Maintenance     
                if(c.Type == 'Repair' || c.Type == 'Routine Maintenance'){
                    //Adiciona os Ids desses Casos para nossa Lista
                    validIds.add(c.Id);
                }
            }
        }
        // Se a lista não estiver vazia, faça a query pra pegar o que é necessário para o pedido
        if(!validIds.isEmpty()){
            List<Case> newCases = new List<Case>();
            // Query para puxar as variáveis necessárias
            Map<Id,Case> closedCasesM = new Map<Id,Case>([
                SELECT Id, Vehicle__c, Equipment__c, Equipment__r.Maintenance_Cycle__c, (SELECT Id, Equipment__c , Quantity__c FROM Equipment_Maintenance_Items__r)
                FROM Case WHERE Id IN :validIds
            ]);            
            //Map para armazenar os ciclos de manutenção válidos
            Map<Id,Decimal> maintenanceCycles = new Map<Id,Decimal>();                      
            AggregateResult[] results = [SELECT Maintenance_Request__c, MIN(Equipment__r.Maintenance_Cycle__c)cycle FROM Equipment_Maintenance_Item__c WHERE Maintenance_Request__c IN :ValidIds GROUP BY Maintenance_Request__c];
            //Loop que alimenta o map
            for (AggregateResult ar : results){
                maintenanceCycles.put((Id) ar.get('Maintenance_Request__c'), (Decimal) ar.get('cycle'));
            }            
            //Loop para criar nova rotina de manutenção com os dados certos
            for (Case cc : closedCasesM.values()){
                Case nc = new Case (
                	ParentId = cc.Id,
                    Status = 'New',
                    Subject = 'Routine Maintenance',
                    Type = 'Routine Maintenance',
                    Vehicle__c = cc.Vehicle__c,
                    Equipment__c = cc.Equipment__c,
                    Origin = 'Web',
                    Date_Reported__c = Date.Today()                
                );
                //Atribui o valor da data ao valor correto esperado da data de entrega
                if (maintenanceCycles.containskey(cc.Id)){
                    nc.Date_Due__c = Date.today().addDays((Integer) maintenanceCycles.get(cc.Id));
                }else{
                    nc.Date_Due__c = Date.today().addDays((Integer) cc.Equipment__r.maintenance_Cycle__c);
                }
                //Insere novo case a lista
                newCases.add(nc);
            }
            //Insere a lista ao objeto
            insert newCases;
            List<Equipment_Maintenance_Item__c> clonedWPs = new List<Equipment_Maintenance_Item__c>();
            for (Case nc: newCases){
                for (Equipment_Maintenance_Item__c wp : closedCasesM.get(nc.ParentId).Equipment_Maintenance_Items__r){
                    Equipment_Maintenance_Item__c wpClone = wp.Clone();
                    wpClone.Maintenance_Request__c = nc.Id;
                    ClonedWPs.add(WpClone);                    
                }                
            }
            insert ClonedWPs;
        }
        
    }        
    
}
