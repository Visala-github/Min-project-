import pandas as pd
import argparse
import sys

def parse_arguments():
    parser = argparse.ArgumentParser(description='Mini-Pandas/SQL Query System')
    parser.add_argument('csv_file', type=str, help='Path to the CSV file')
    parser.add_argument('--operation', type=str, required=True,
                        choices=['select', 'filter', 'groupby', 'aggregate', 'sort'],
                        help='Operation to perform')
    parser.add_argument('--columns', type=str, help='Comma-separated list of columns')
    parser.add_argument('--filter', type=str, help='Filter condition, e.g., "age>30"')
    parser.add_argument('--groupby', type=str, help='Column to group by')
    parser.add_argument('--agg', type=str, help='Aggregation function, e.g., "sum", "mean"')
    parser.add_argument('--sortby', type=str, help='Column to sort by')
    parser.add_argument('--ascending', action='store_true', help='Sort ascending (default: False)')
    return parser.parse_args()

def select_columns(df, columns):
    cols = [col.strip() for col in columns.split(',')]
    return df[cols]

def filter_rows(df, condition):
    return df.query(condition)

def groupby_aggregate(df, group_col, agg_func):
    return df.groupby(group_col).agg(agg_func)

def sort_df(df, sort_col, ascending):
    return df.sort_values(by=sort_col, ascending=ascending)

def main():
    args = parse_arguments()
    try:
        df = pd.read_csv(args.csv_file)
    except Exception as e:
        print(f"Error reading CSV: {e}")
        sys.exit(1)

    result = df

    if args.operation == 'select' and args.columns:
        result = select_columns(result, args.columns)
    if args.operation == 'filter' and args.filter:
        result = filter_rows(result, args.filter)
    if args.operation == 'groupby' and args.groupby and args.agg:
        result = groupby_aggregate(result, args.groupby, args.agg)
    if args.operation == 'aggregate' and args.agg:
        result = result.agg(args.agg)
    if args.operation == 'sort' and args.sortby:
        result = sort_df(result, args.sortby, args.ascending)

    print(result)

if __name__ == "__main__":
    main()
    
