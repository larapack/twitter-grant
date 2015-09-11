# twitter-grant
A TwitterGrant for the [OAuth 2.0 Bridge](https://github.com/lucadegasperi/oauth2-server-laravel) for [Laravel](https://github.com/laravel/laravel).

## Install
Require this package using composer.
```
composer require larapack/twitter-grant
```

To enable this grant add the following to the `config/oauth2.php` configuration file.
```
'grant_types' => [
    'twitter' => [
        'class' => '\Larapack\TwitterGrant',
        'callback' => '\App\PasswordVerifier@verifyTwitter',
        'access_token_ttl' => 3600,
        'gather_twitter_user' => true, // wheter or not we should get the $twitterUser variable to the callback
        'client_id' => Config::get('services.twitter.client_id'), // only needed if we should gather the twitter user
        'client_secret' => Config::get('services.twitter.client_secret'), // only needed if we should gather the twitter user
    ]
]
```

Create a class with a verify method where you check if the provided user is a valid one.
```
use App\User;

class PasswordGrantVerifier
{
  // $twitterUser will be null if `get_twitter_user` is not set to true in configuration.
  public function verifyTwitter($twitterToken, $twitterSecret, $twitterUser)
  {
      $credentials = [
        'twitter_user_id' => $twitterUser->id,
      ];

      if (Auth::once($credentials)) {
          return $user->id;
      }

      return false;
  }
}
```

Next add a sample `client` to the `oauth_clients` table.

Finally set up a route to respond to the incoming access token requests.
```
Route::post('oauth/twitter/access_token', function() {
    return Response::json(Authorizer::issueAccessToken());
});
```
