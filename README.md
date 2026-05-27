SELECT TOP 5
    c.ID            AS ClaimID,
    c.ProviderID,
    cd.Slno
FROM Claims c WITH(NOLOCK)
INNER JOIN Claimsdetails cd WITH(NOLOCK)
    ON cd.ClaimID = c.ID
WHERE c.ProviderID = 156751
  AND ISNULL(c.Deleted, 0)  = 0
  AND ISNULL(cd.Deleted, 0) = 0
ORDER BY c.ID DESC;
