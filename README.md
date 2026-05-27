SELECT
    Mp.ProviderId,
    Doc.FileName,
    Doc.SystemFileName,
    CASE WHEN OMp.id IS NOT NULL THEN 'yes' ELSE 'no' END AS IsOldDoc
FROM ProviderTariffDocs Doc WITH(NOLOCK)
INNER JOIN ProviderTariff_Map Mp WITH(NOLOCK) ON Doc.Id = Mp.DocumentId
LEFT JOIN (SELECT * FROM ProviderTariff_Map WITH(NOLOCK) WHERE mouid = 0) OMp
       ON Doc.id = OMp.documentid
WHERE Mp.Id = 95176;
