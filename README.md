-- Find providers that have at least one tariff document, with file names
SELECT TOP 20
    Mp.ProviderID,
    Doc.FileName,
    Doc.SystemFileName,
    Doc.CreatedDateTime,
    Doc.Status      AS DocStatus,
    Mp.Status       AS MapStatus,
    Mp.MOUID,
    CASE WHEN OMp.id IS NOT NULL THEN 'yes' ELSE 'no' END AS IsOldDoc
FROM ProviderTariffDocs Doc WITH(NOLOCK)
INNER JOIN ProviderTariff_Map Mp WITH(NOLOCK) ON Doc.Id = Mp.DocumentId
LEFT JOIN (SELECT * FROM ProviderTariff_Map WITH(NOLOCK) WHERE mouid = 0) OMp
       ON Doc.id = OMp.documentid
WHERE Doc.Status = 1
  AND Mp.Status  = 1
  AND Doc.FileName LIKE '%.pdf'
ORDER BY Doc.CreatedDateTime DESC;


