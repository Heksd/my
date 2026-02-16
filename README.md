Первое задание
  SELECT CONCAT( rel.familyName, ' ', rel.givenName, ' ', rel.middleName) AS [ФИО] ,rel.birthDate AS [Дата рождения] ,dep.contactRelationship AS [Родственная связь]
FROM HPPersonGeneric AS emp
JOIN HPPersonDependant AS dep 
    ON emp.sysId = dep.HPPersonGenericSysId
JOIN HPPersonGeneric AS rel 
    ON dep.HPRelatedPersonSysId = rel.sysId
WHERE emp.personId = 'test';
