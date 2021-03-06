---
title: Laravel Passport integration
extends: _layouts.documentation
section: content
---

# Laravel Passport

> If you just want to write an SPA, but don't need an API for some other use (e.g. mobile app), you can avoid a lot of the complexity of writing SPAs by using [Inertia.js](https://inertiajs.com/).

To use Passport inside the tenant part of your application, you may do the following.

- Add this to the `register` method in your `AppServiceProvider`:

    ```php
    Passport::ignoreMigrations();
    Passport::routes(null, ['middleware' => [
        // You can make this simpler by creating a tenancy route group
        InitializeTenancyByDomain::class,
        PreventAccessFromCentralDomains::class,
    ]]);
    ```

- `php artisan vendor:publish --tag=passport-migrations` & move to `database/migrations/tenant/` directory

## **Shared keys**

If you want to use the same keypair for all tenants, do the following.

- Don't use `passport:install`, use just `passport:keys`. The install command creates keys & two clients. Instead of creating clients centrally, create `Client`s manually in your [tenant database seeder]({{ $page->link('configuration#seeder-params') }}).

## **Tenant-specific keys**

If you want to use a unique keypair for each tenant, do the following. (Note: The security benefit of doing this isn't probably that big, since you're likely already using the same `APP_KEY` for all tenants.)

There are multiple ways you can store & load tenant keys, but the most straightforward way is to store the keys in the on the tenant model and load them into the `passport` configuration using the **[Tenant Config]({{ $page->link('features/tenant-config') }})** feature:

- Uncomment the `TenantConfig` line in your `tenancy.features` config
- Configure the mapping as follows:

    ```php
    [
        'passport_public_key' => 'passport.public_key',
        'passport_private_key' => 'passport.private_key',
    ],
    ```

And again, you need to create clients in your tenant database seeding process.

## Using Passport in both the central & tenant app

![Passport for both central & tenant app](/assets/images/passport_universal.png)

And make sure you enable the *Universal Routes* feature.