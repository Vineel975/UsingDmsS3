SELECT
    p.parameter_id AS ParamOrder,
    p.name         AS ParameterName,
    TYPE_NAME(p.user_type_id) AS DataType
FROM sys.parameters p
WHERE p.object_id = OBJECT_ID('USP_ClaimMedicalScrutiny_Retrieve')
ORDER BY p.parameter_id;
