# Auth System

## Overview
Implement a new authentication system with OAuth 2.0 support and improved session management.

## Requirements
- OAuth 2.0 / OpenID Connect support
- JWT-based session tokens
- Refresh token rotation
- Multi-factor authentication (TOTP)

## Technical Notes
- Using `passport.js` for OAuth providers
- Redis for session storage
- Rate limiting on auth endpoints

## Links
- [RFC 6749 - OAuth 2.0](https://datatracker.ietf.org/doc/html/rfc6749)
- [Design Doc](/technical/auth-system.md)
