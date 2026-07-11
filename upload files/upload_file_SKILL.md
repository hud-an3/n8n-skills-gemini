# Skill: Upload File to Google Drive

## Purpose
Upload the file provided in this workflow run to the correct Google Drive folder, resolving the destination folder based on what the user specified in their prompt.

## When to use this skill
Trigger this skill whenever a file needs to be saved/uploaded to Google Drive as part of the workflow.

## Inputs
- `file` (binary, required): The file to upload.
- `folder_name` (string, optional): Extracted from the user's prompt if they named a destination folder (e.g. "upload this to Invoices", "save it in the Client-X folder").
- `default_folder` (string, constant): `default_folder` — the fallback Drive folder used when no folder is named in the prompt.

## Steps
1. Parse the user's prompt for a folder name.
2. **If a folder name is specified:**
   a. Search Google Drive for a folder matching that name and get it's ID using the Search files and folders in Google Drive tool.
   c. If the required folder exists, get the required folder's ID so that Upload file in Google Drive can use it as the upload destination. 
   b. Use the folder ID as the upload destination.
   c. If it does not exist, create a new folder with that exact name, then use the newly created folder's ID as the destination.
3. **If no folder name is specified in the prompt (file only):**
   a. Use `default_folder` as the upload destination. Do not create a new folder in this case — `default_folder` is assumed to already exist.
4. Upload `file` to the resolved destination folder.
5. Confirm success and report where the file ended up.

## Output format
```
Uploaded: <file_name>
Destination folder: <folder_name or "default_folder">
Folder status: <"existing" | "newly created" | "default">
Link: <Drive file/folder link, if available>
```

## Edge cases
- If multiple folders in Drive match the specified name (duplicates), use the first exact-name match and note the ambiguity in the output rather than failing silently.
- If folder creation fails (permissions, quota, etc.), report the failure clearly — do not fall back to `default_folder` without saying so, since that could put a file somewhere the user didn't ask for.
- If `file` is missing/empty when this skill runs, do not attempt an upload — return an error stating no file was found to upload.
- Folder name matching should be case-insensitive but should preserve the user's exact casing when creating a new folder.
