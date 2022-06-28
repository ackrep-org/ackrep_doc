(troubleshooting_users)=
# Troubleshooting for Users

Fehlermeldung:
- KeyError: "No entity with key '``<key>``' could be found. Make sure that the database is in sync with repo."<br>
→ database was not loaded correctly. Use `ackrep -l <path/to/ackrep_data>` to load it.