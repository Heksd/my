Первое задание<br/>
  SELECT CONCAT( rel.familyName, ' ', rel.givenName, ' ', rel.middleName) AS [ФИО] ,rel.birthDate AS [Дата рождения] ,dep.contactRelationship AS [Родственная связь]<br/>
FROM HPPersonGeneric AS emp<br/>
JOIN HPPersonDependant AS dep <br/>
    ON emp.sysId = dep.HPPersonGenericSysId<br/>
JOIN HPPersonGeneric AS rel <br/>
    ON dep.HPRelatedPersonSysId = rel.sysId<br/>
WHERE emp.personId = 'test';<br/>
