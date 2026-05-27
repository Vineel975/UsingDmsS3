SELECT TOP 5
    cd.ClaimID,
    cd.Slno,
    cd.ProviderID,
    cd.MOUID
FROM Claimsdetails cd WITH(NOLOCK)
WHERE cd.ProviderID = 156751
  AND ISNULL(cd.Deleted, 0) = 0
ORDER BY cd.ClaimID DESC;
