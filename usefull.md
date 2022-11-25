#### create empry df with column names
df = pd.DataFrame(columns=['A','B','C','D','E','F','G'])

#### adding dict to frame
df_coin_signals.append(dict, ignore_index = True)
#### adding a row
df.loc[-1] = [2, 3, 4]  
df.index = df.index + 1  # shifting index
df = df.sort_index()  # sorting by index
