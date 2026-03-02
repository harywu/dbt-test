# Python Style Guide

## 1. Core Philosophy
## This guide is based onPEP 8 but adapted for data processing workflows.
* Readability: Prioritize clear logic over clever one-liners.
* Data-Centric: Accommodate longer lines for complex data structures.
* Cleanliness: Code should be self-explanatory without inline comments.

## 2. Formatting Rules
# Strings
* Double Quotes Only: Always use double quotes (") for strings.
  * Correct: name = "Alice"
  * Incorrect: name = 'Alice'

⠀Line Length
* Limit: 120 characters.
* Reason: To accommodate data manipulation logic and modern screen widths.

⠀Control Flow
* No **break** or **continue**: Avoid these keywords in loops.
* Alternative: Restructure logic, use list comprehensions, or slice collections/dataframes instead.

⠀Comments
* Zero Comments: Do not include comments in the final code unless specifically requested.

⠀3. Naming Conventions
* Booleans: Variable names in conditions must be prefixed with is_ or has_.
  * Example: is_valid, has_data, is_processed.
* General: Follow standard PEP 8 (snake_case for variables/functions, CamelCase for classes).

## 3. Example
"""Module for user authentication."""

import hashlib
import logging
import os

from companyx.db import user_database

LOGGER = logging.getLogger(__name__)

def hash_password(password):
    salt = os.urandom(16)
    salted_password = salt + password.encode("utf-8")
    hashed_password = hashlib.sha256(salted_password).hexdigest()
    return f"{salt.hex()}:{hashed_password}"

def authenticate_user(username, password):
    try:
        user = user_database.get_user(username)
        is_user_missing = user is None
        
        if is_user_missing:
            LOGGER.warning("Authentication failed: User not found - %s", username)
            return False

        stored_hash = user.password_hash
        salt, hashed_password = stored_hash.split(":")
        salted_password = bytes.fromhex(salt) + password.encode("utf-8")
        calculated_hash = hashlib.sha256(salted_password).hexdigest()

        is_password_correct = calculated_hash == hashed_password

        if is_password_correct:
            LOGGER.info("User authenticated successfully - %s", username)
            return True
        
        LOGGER.warning("Authentication failed: Incorrect password - %s", username)
        return False

    except Exception as e:
        LOGGER.error("An error occurred during authentication: %s", e)
        return False
