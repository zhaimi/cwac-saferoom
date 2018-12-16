# CWAC-SafeRoom `1.0.0-alpha1`

If you have already integrated [CWAC-SafeRoom](https://github.com/commonsguy/cwac-saferoom),
you may be interested in experimenting with `1.0.0-alpha1`.

## What's Different?

Primarily, `1.0.0-alpha1` uses [SQLCipher for Android 4.0.0](https://www.zetetic.net/blog/2018/11/30/sqlcipher-400-release/),
the latest-and-greatest release of SQLCipher for Android.

Part of what we get from that is an expanded `SQLiteDatabase` API, one that more
closely resembles the modern framework implementation. That, in turn, allows
SafeRoom to fully implement the support database API &mdash; previous releases
had some gaps (ones that Room, fortunately, tended not to use).

Also, there are a few new utility options in `1.0.0-alpha1`, in part to deal with
changes in SQLCipher for Android.

## Installation

There are two versions of this library, for AndroidX and for the older Android Support Library.

If you cannot use SSL, use `http://repo.commonsware.com` for the repository URL.

### AndroidX

```groovy
repositories {
    maven {
        url "https://s3.amazonaws.com/repo.commonsware.com"
    }
}

dependencies {
    implementation "com.commonsware.cwac:saferoom.x:1.0.0-alpha1"
}
```

### Android Support Library

```groovy
repositories {
    maven {
        url "https://s3.amazonaws.com/repo.commonsware.com"
    }
}

dependencies {
    implementation "com.commonsware.cwac:saferoom:1.0.0-alpha1"
}
```

## Usage

If you have not done so already, follow the instructions in
[the main project `README`](https://github.com/commonsguy/cwac-saferoom).
For new apps starting fresh with `1.0.0-alpha1` and that were not using
SQLCipher for Android 3.5.9 or older, you should be in good shape.

If, however, you have been using SQLCipher for Android 3.5.9, with or without
SafeRoom, Zetetic changed the file format. This requires us to do a bit of extra
work, for existing databases in the older format.

### Keeping the Old Format

Perhaps you have a strong need to keep the database in the older format, for
whatever reason. In that case, pass `SafeHelperFactory.POST_KEY_SQL_V3` as the
second parameter to either the `SafeHelperFactory` constructor or `fromUser()`
static method:

```java
SafeHelperFactory factory=
  SafeHelperFactory.fromUser(new SpannableStringBuilder(passphraseField.getText()),
    SafeHelperFactory.POST_KEY_SQL_V3);
```

This will open the database using the following `PRAGMA` options:

```sql
PRAGMA cipher_page_size = 1024;
PRAGMA kdf_iter = 64000;
PRAGMA cipher_hmac_algorithm = HMAC_SHA1;
PRAGMA cipher_kdf_algorithm = PBKDF2_HMAC_SHA1;
```

### Migrating to the New Format

If you wish to convert to the newer, more secure settings, the *first time* that
you open the existing database, pass `SafeHelperFactory.POST_KEY_SQL_MIGRATE`
as the second parameter to the `SafeHelperFactory` constructor or `fromUser()`
static method:

```java
SafeHelperFactory factory=
  SafeHelperFactory.fromUser(new SpannableStringBuilder(passphraseField.getText()),
    SafeHelperFactory.POST_KEY_SQL_MIGRATE);
```

This will convert the existing database in place. The second and subsequent times
that you work with the database, you can (and should) skip this parameter, as the
database will have already been migrated.

## Demo

The `demo/` module shows the use of SafeRoom, albeit via direct use of the support
database API, not via Room. The sample stores the passphrase in the
`AndroidKeyStore` and therefore requires a secure keyguard (PIN, passphrase,
biometric, etc.).

## Questions

If you run into problems with `1.0.0-alpha1` that you're pretty sure is a bug,
please post an [issue](https://github.com/commonsguy/cwac-saferoom/issues).
Be certain to include complete steps for reproducing the issue.
The [contribution guidelines](CONTRIBUTING.md)
provide some suggestions for how to create a bug report that will get
the problem fixed the fastest.

Otherwise, please ask your questions either:

- In [the CWAC category](https://community.commonsware.com/c/cwac) of
[the CommonsWare Community](https://community.commonsware.com/), or

- On [Stack Overflow](http://stackoverflow.com/questions/ask), tagged with
`commonsware-cwac` and `android`
