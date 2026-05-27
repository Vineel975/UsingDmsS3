-- Find providers that have at least one PDF tariff file uploaded
SELECT TOP 10
    Mp.ProviderID,
    COUNT(*) AS FileCount
FROM ProviderTariffDocs Doc WITH(NOLOCK)
INNER JOIN ProviderTariff_Map Mp WITH(NOLOCK) ON Doc.Id = Mp.DocumentId
WHERE Doc.Status = 1
  AND Mp.Status  = 1
  AND Doc.FileName LIKE '%.pdf'
GROUP BY Mp.ProviderID
ORDER BY FileCount DESC;
