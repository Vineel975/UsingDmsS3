SELECT
    p.parameter_id AS ParamOrder,
    p.name         AS ParameterName,
    TYPE_NAME(p.user_type_id) AS DataType,
    p.has_default_value,
    p.default_value
FROM sys.parameters p
WHERE p.object_id = OBJECT_ID('Usp_TariffUploadDoc_FillDetails')
ORDER BY p.parameter_id;

EXEC sp_helptext 'Usp_TariffUploadDoc_FillDetails';
