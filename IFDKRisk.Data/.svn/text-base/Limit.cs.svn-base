using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using CommonDBUtil;
using System.Data.SqlClient;

namespace IFDKRisk.Data
{
    public static class Limit
    {
        public static int InsertBondLimit(int? counterpartyID, int? counterpartyGroupID, double limitValue, Currency cur,
            bool considerRevRepo, DateTime startDate)
        {
            if (counterpartyID == null && counterpartyGroupID == null)
                throw new ArgumentException("counterpartyID == null && counterpartyGroupID == null");

            if (counterpartyID != null && counterpartyGroupID != null)
                throw new ArgumentException("counterpartyID != null && counterpartyGroupID != null");

            if (limitValue <= 0)
                throw new ArgumentException("limitValue <= 0");

            string script = @"
                declare @limitID int;
                set @limitID = (select max(ID)+1 from t_Cpty_Limit);

                declare @fullName nvarchar(300);
                declare @shortName nvarchar(300);

                if (@cptyID is not null)
                    select @fullName = Client_Name, @shortName = Client_ShortName from t_Client where ID = @cptyID;
                else
                    select @fullName = NameCptyGroup, @shortName = SNameCptyGroup from t_CptyGroup where ID = @cptyGroupID;

                insert into t_Cpty_Limit(ID, MasterLimitType, Limit_Type, Limit_Value, Limit_Currency, Limit_Unit, Limit_Date_Start, InstrumentType_ID,
                    Cpty_ID, CtptyGroup_ID, Cpty_ShortName, Cpty_Name, PortfolioGroup_ID)
                values(@limitID, 'counterparty', 'credit', @limitValue, @limitCurrency, 'currency', @startDate, @instrumentType_ID,
                    @cptyID, @cptyGroupID, @shortName, @fullName, null)";
            SqlCommand cmd = DBHelper.CreateCommand(RiskSettings.ConnStr, script,
                DBHelper.CreateParameter("@limitValue", limitValue),
                DBHelper.CreateParameter("@limitCurrency", cur.ToString().ToUpper()),
                DBHelper.CreateParameter("@startDate", startDate),
                DBHelper.CreateParameter("@instrumentType_ID", considerRevRepo ? -1 : (int?)null),
                DBHelper.CreateParameter("@cptyID", counterpartyID), DBHelper.CreateParameter("@cptyGroupID", counterpartyGroupID));
            DBHelper.ExecuteNonQuery(cmd);
            return 0;
        }
    }
}
