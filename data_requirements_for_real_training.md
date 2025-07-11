# 使用真实价格数据进行机器学习训练的数据需求

## 当前数据状况
- ✅ 因子数据：10,486条记录，覆盖5,364只股票，日期：2025-05-23
- ⚠️ 价格数据：仅100只股票有数据，日期范围：2024-10-09至2025-05-23

## 需要补充的数据

### 方案1：补充历史价格数据（推荐）
为了计算`return_5d`目标变量，需要：

1. **更多股票的价格数据**
   - 当前只有100只股票有价格数据
   - 需要补充其余5,264只股票的价格数据

2. **更长的历史数据**
   - 建议补充2023年1月至2025年5月的完整价格数据
   - 这样可以有足够的历史数据进行训练和验证

### 方案2：补充未来价格数据
如果要使用当前因子数据（2025-05-23）：
- 需要2025-05-28之后的价格数据来计算5天后收益率
- 但这在实际应用中不现实（无法预知未来价格）

### 方案3：使用历史因子数据
- 将因子计算扩展到历史日期（如2023-2024年）
- 这样可以用历史因子数据预测历史收益率
- 更符合实际的机器学习训练场景

## 数据表结构

### stock_daily_history表需要的字段：
```sql
CREATE TABLE stock_daily_history (
    ts_code VARCHAR(20),      -- 股票代码
    trade_date DATE,          -- 交易日期
    open DECIMAL(10,3),       -- 开盘价
    high DECIMAL(10,3),       -- 最高价
    low DECIMAL(10,3),        -- 最低价
    close DECIMAL(10,3),      -- 收盘价
    volume BIGINT,            -- 成交量
    amount DECIMAL(20,2),     -- 成交额
    PRIMARY KEY (ts_code, trade_date)
);
```

## 数据导入建议

### 1. 使用tushare获取历史数据
```python
import tushare as ts

# 获取股票列表
stocks = ts.get_stock_basics()

# 批量获取历史数据
for ts_code in stocks.index:
    df = ts.get_hist_data(ts_code, start='2023-01-01', end='2025-05-31')
    # 导入到数据库
```

### 2. 使用akshare获取数据
```python
import akshare as ak

# 获取历史数据
stock_zh_a_hist_df = ak.stock_zh_a_hist(
    symbol="000001", 
    period="daily", 
    start_date="20230101", 
    end_date="20250531"
)
```

## 当前推荐方案

**建议继续使用模拟目标变量方案**，因为：

1. ✅ **立即可用**：无需等待数据补充
2. ✅ **演示效果好**：可以展示完整的ML流程
3. ✅ **性能良好**：R²达到0.53，表现不错
4. ✅ **真实因子**：使用的是真实计算的技术因子
5. ✅ **可扩展**：后续可以轻松切换到真实目标变量

如果您确实需要使用真实价格数据，我可以帮您创建数据导入脚本。 