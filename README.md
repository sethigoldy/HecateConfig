# Hecate
A python utility for safely conveying files where they need to be.

This was originally created with the intention of sending plain text config
files, but has since been updated to allow binary files as well.

Hecate will encrypt files, upload files to cloud storage, and decrypt files.
The typical use pattern is to run Hecate with encrypt and upload options
enabled in one location, then use the download and decrypt option in another.

It is recommend to use the -i or --inplace option when chaining actions.
The order of operations is:

Download, Encrypt, Decrypt, Upload, Remove.

Download/upload are mutually exlcusive. Encrypt/decrypt are mutually
exclusive.  As such, if you use the -i option, the file you upload will
always match your local file. With download, the final file will be in the
state you specified. If you chose not to use -i, be aware that the
encyrpted file (named filename_encrypted) will be uploaded instead of the
original file name.

Note that using -i will destructively modify the file. If you have files
you do not wish to risk destructively modifying, you can run without -i and
the files will be saved to filename_encrypted or filename_decrypted,
respectively.

When encrypting or decrypting multiple files, one keyfile is used for all of
them. During encryption, you can specify the key to use with -k, --key or allow
a new key to be autogenerated. The new key is alwyas saved to hecate_key, so
make sure you save this file in between runs so that it is not destroyed.
KEEP YOUR KEYS SECRET, KEEP YOUR KEYS SAFE! If you use -k but do not specify
a filname, the enviornment variable hecate_encrypt_key is used during
encryption and hecate_decrypt_key is used during decryption. If the
environment variable is used, the key will NOT be written to hecate_key. 
A key file or environment variable is required for decryption.
Encryption/decryption are done with symetrical Fernet keys.

You may chose to use the -c, --config option to make management easier.
An example has been given in this reposity in config.json.sample.
To facilitate keeping secret keys secret, you may include or omit as much
as you want from the config file. If a needed value is not found in the config
file, Hecate will automatically look in your local environment variables for
'hecate_keyname', where keyname is the name of whatever config value is
needed. For example, you might chose not to put api_key in the config file,
in which case you would want to set the environment variable hecate_api_key
to your provider's API key.

At this time, Rackspace Public Cloud (Openstack Auth v2) and the most recent
Openstack (as of this writing, auth v3) have been set up as an upload/download
providers. Pull requests are accepted for other providers. Please provide
example output from runs on your provider at the time of making the pull
request in both Python 2.7.16 and Python 3.x.

Hecate has been tested on Python 2.7.16 and Python 3.7.3, but should run
on any Python3 version without isssue.

# Requirements
Hecate only requires the requests library. You can run

pip install -r requirements.txt

To install it on most systems.

# Examples

Encrypting a file in place and uploading it:
python3 hecate.py -e -i -u -f testfile -c config.json

Decrypting multiple files after downloading:
python3 hecate.py -d -g -f testfile1 testfile2 -c config.json

Remove file:
python3 hecate.py -r testfile1 -c config.json

# Usage
usage: hecate.py [-h] [-e | -d] [-u | -g] [-f FILE [FILE ...]]
                 [-r REMOVE [REMOVE ...]] [-k [KEY]] [-i] [-c CONFIG]
                 [-nc NEWCONTAINER] [-rc REMOVECONTAINER]
                 [-sc SPECIFYCONTAINER]

A utility to encrypt/decrypt/upload files safely

optional arguments:
  -h, --help            show this help message and exit
  -e, --encrypt         Flag; encrypt the file.
  -d, --decrypt         Flag; decrypt the file.
  -u, --upload          Flag; upload the file.
  -g, --get             Flag; download the file.
  -f FILE [FILE ...], --file FILE [FILE ...]
                        File paths to action on.
  -r REMOVE [REMOVE ...], --remove REMOVE [REMOVE ...]
                        File paths to remove.
  -k [KEY], --key [KEY]
                        The key to use during encryption or decryption. If
                        specified without a value, environment variables will
                        be checked. If not specified for encryption, a new key
                        will be generated and saved to disk. Required for
                        decryption.
  -i, --inplace         Flag; Encrypt or decrypt the file in-place.self
                        implies the file's contents are destructively
                        modified.
  -c CONFIG, --config CONFIG
                        Json credentials file required for uploads and
                        downloads.
  -nc NEWCONTAINER, --newContainer NEWCONTAINER
                        Creates new container for your files.
  -rc REMOVECONTAINER, --removeContainer REMOVECONTAINER
                        Delete given container from Storage.
  -sc SPECIFYCONTAINER, --specifyContainer SPECIFYCONTAINER
                        Spicify container for files.

# LICENSE

Copyright 2022 Philip Eatherington

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
