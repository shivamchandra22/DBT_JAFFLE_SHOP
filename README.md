## Testing dbt project: `DBT_jaffle_shop`

`jaffle_shop` is a fictional ecommerce store. This dbt project transforms raw data from an app database into a customers and orders model ready for analytics.

### Prerequisite for DBT
1. Python must be installed in the Machine, dbt-core is compatible with Python versions 3.7, 3.8, and 3.9
2. Coding can be done in various IDE's like VS Code
 

### Commandline Running the project 
To get up and running with this project:
1. Ensure your profile is setup correctly from the command line:
```bash
$ dbt debug
```

2. To compile the code wrriten
```bash
$ dbt compile
```

3. Run the models:
```bash
$ dbt run
```

4. Test the output of the models with the existing auto mated test case implemented
```bash
$ dbt test
```

5. Generate documentation for the project:
```bash
$ dbt docs generate
```

6. View the documentation for the project:
```bash
$ dbt docs serve
```
