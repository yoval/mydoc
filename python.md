# Python Pandas

### 保存`DataFrame`至excel不同的sheet。

```python
import pandas as pd
import numpy as np
import openpyxl

with pd.ExcelWriter(output_filename, engine='openpyxl') as writer:
    df1.to_excel(writer, sheet_name='表1', index=False)
    df2.to_excel(writer, sheet_name='表2', index=False)
    df3.to_excel(writer, sheet_name='表3', index=False)
```

### Pandas 的筛选

```python
import pandas as pd
import numpy as np

mask = merge_df['门店编码'].isin(['ZYD00203', 'ZYD00204', 'ZYD00205', 'ZYD00206'])
merge_df = merge_df.drop(merge_df[mask].index)
```

