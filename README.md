ParamOrder  ParameterName                                                                                                                    DataType                                                                                                                         has_default_value default_value
----------- -------------------------------------------------------------------------------------------------------------------------------- -------------------------------------------------------------------------------------------------------------------------------- ----------------- ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
1           @ProviderID                                                                                                                      int                                                                                                                              0                 NULL
2           @MOUID                                                                                                                           varchar                                                                                                                          0                 NULL
3           @TariffDocId                                                                                                                     int                                                                                                                              0                 NULL
4           @Type                                                                                                                            varchar                                                                                                                          0                 NULL


Text
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
CREATE PROCEDURE [dbo].[Usp_TariffUploadDoc_FillDetails](@ProviderID int,@MOUID varchar(max) ,@TariffDocId Int,@Type varchar(10)='Mapped' )    
as      
begin      
set nocount ON   
 

 If @TariffDocId > 0 
	Begin
		Select mp.ProviderId  ,FileName,SystemFileName,case when OMp.id is not null then 'yes' else 'no'end IsOldDoc 
		from ProviderTariffDocs (nolock) Doc Inner Join 
		ProviderTariff_Map (nolock) Mp On Doc.Id=Mp.DocumentId
		Left join (select * from ProviderTariff_Map (nolock) where mouid= 0) OMp On doc.id=omp.documentid 
		Where Mp.Id=@TariffDocId
	End
	
Else if @ProviderID > 0 and @MOUID != ''and @MOUID != '-1' and @Type = 'NotMapped'
	Begin
	Select Mp.Id FileId,Mp.MOUID,Mp.Documentid
		 , Row_Number() over(order by Mp.MOUID Desc,TariffDoc.ID Desc) SlNo
		 ,FileName, MU.Name as UserName,TariffDoc.CreatedDateTime UpdateDate
		 ,SystemFileName into #temp
		 from  ProviderTariffDocs (nolock) TariffDoc 
		 Join ProviderTariff_Map Mp On TariffDoc.Id=Mp.DocumentID
		 JOIN Lnk_UserRegions (NOLOCK) LU ON LU.ID = Mp.CREATEDUSERREGIONID
		 JOIN Mst_Users (NOLOCK) MU ON MU.ID= LU.USERID
		 where  Mp.ProviderID=@ProviderID and Mp.Status=1 
		 and TariffDoc.Status=1 
		 and MOUID IN (SELECT items FROM dbo.Split(@MOUID, ','))

		
	Select max(Mp.Id) as FileId,0 MOUID,Mp.Documentid
		 , Row_Number() over(order by Mp.Documentid Desc) SlNo
		 ,FileName,TariffDoc.CreatedDateTime UpdateDate
		 ,SystemFileName 
		 from  ProviderTariffDocs (nolock) TariffDoc 
		 Join ProviderTariff_Map Mp On TariffDoc.Id=Mp.DocumentID
		 JOIN Lnk_UserRegions (NOLOCK) LU ON LU.ID = Mp.CREATEDUSERREGIONID
		 JOIN Mst_Users (NOLOCK) MU ON MU.ID= LU.USERID
		 where  Mp.ProviderID=@ProviderID --and Mp.Status=1 
		 and TariffDoc.Status=1 
		 and MOUID NOT IN (SELECT items FROM dbo.Split(@MOUID, ','))
		 and Mp.Documentid not in (select documentid from #temp)
		 Group by Mp.Documentid,FileName,TariffDoc.CreatedDateTime,systemfilename
		order by  UpdateDate  desc
		drop table #temp
	End

Else if @ProviderID > 0 and @MOUID != ''and @MOUID != '-1'
	Begin
		 Select Mp.Id FileId,Mp.MOUID
		 , Row_Number() over(order by Mp.MOUID Desc,TariffDoc.ID Desc) SlNo
		 ,FileName, MU.Name as UserName,TariffDoc.CreatedDateTime UpdateDate
		 ,SystemFileName 
		 from  ProviderTariffDocs (nolock) TariffDoc 
		 Join ProviderTariff_Map Mp On TariffDoc.Id=Mp.DocumentID
		 JOIN Lnk_UserRegions (NOLOCK) LU ON LU.ID = Mp.CREATEDUSERREGIONID
		 JOIN Mst_Users (NOLOCK) MU ON MU.ID= LU.USERID
		 where  Mp.ProviderID=@ProviderID and Mp.Status=1 
		 and TariffDoc.Status=1 
		 and MOUID IN (SELECT items FROM dbo.Split(@MOUID, ','))
		order by  UpdateDate  desc
	End
Else
	Begin
		--Select Row_Number() over(order by FileName asc,Id Asc) SlNo ,
		--Id FileId,FileName,SystemFileName,CreatedDateTime UpdateDate from ProviderTariffDocs (nolock)PDocs
		--Where Status=1  order by  CreatedDateTime   desc

		--SELECT Row_Number() over(order by FileName asc,PDOCS.ID  Asc) SlNo ,
		--MAX(PDOCS.ID) FileId,FileName,SystemFileName,PDOCS.CREATEDDATETIME UpdateDate,MAX(MP.ID) TariffDocId
		--FROM PROVIDERTARIFFDOCS (NOLOCK) PDOCS
		--INNER JOIN PROVIDERTARIFF_MAP MP ON PDOCS.ID = MP.DOCUMENTID
		--WHERE PDOCS.STATUS=1 AND MP.STATUS = 1 and ProviderID=@ProviderID 
		--GROUP BY FILENAME,SYSTEMFILENAME,PDOCS.CREATEDDATETIME ,PDOCS.id
		--order by  UpdateDate desc
		SELECT Row_Number() over(order by FileName asc,PDOCS.ID  Asc) SlNo ,
		MAX(PDOCS.ID) FileId,FileName,SystemFileName,PDOCS.CREATEDDATETIME UpdateDate,MAX(MP.ID) TariffDocId,MU.NAME UserName
		FROM PROVIDERTARIFFDOCS (NOLOCK) PDOCS
		JOIN PROVIDERTARIFF_MAP (NOLOCK) MP ON PDOCS.ID = MP.DOCUMENTID
		JOIN Lnk_UserRegions (NOLOCK) LU ON LU.ID = PDOCS.CREATEDUSERREGIONID
		JOIN Mst_Users (NOLOCK) MU ON MU.ID= LU.USERID
		
		WHERE PDOCS.STATUS=1 and ProviderID=@ProviderID 
		GROUP BY FILENAME,SYSTEMFILENAME,PDOCS.CREATEDDATETIME ,PDOCS.id,MU.NAME
		order by  UpdateDate desc


	End
     
End






Completion time: 2026-05-27T18:09:10.5158214+05:30
