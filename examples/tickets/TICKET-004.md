---
title: Implement digest API endpoints
type: Feature
status: Backlog
created_at: '2025-10-17T23:13:51.881684'
parent: TICKET-002
blocked_by: TICKET-003
estimate: 5
---

## Context

This ticket implements the REST API endpoints for digest operations. This is the core backend service that generates digests, manages user preferences, and integrates with the email delivery system.

- Problem it solves: Provides the backend logic for digest feature
- User impact: Enables digest scheduling and content generation
- Business value: Enables user engagement through digest communication

## Requirements

**Functional requirements**:
- POST /api/digests/preferences - Set user digest preferences
- GET /api/digests/preferences - Get current user's preferences
- POST /api/digests/generate - Generate digest for user(s)
- GET /api/digests/send-history - Get delivery history for current user
- POST /api/digests/unsubscribe - Unsubscribe from digests
- Support scheduling digest generation (admin endpoint for cron)

**Non-functional requirements**:
- API response time < 200ms for preference operations
- Digest generation should process up to 1000 users per batch
- All endpoints require authentication (JWT token)
- Support pagination for historical queries

**Integration points**:
- User authentication (JWT validation)
- Database schema (TICKET-003)
- Email service (for scheduling delivery)

## Acceptance Criteria

### Given-When-Then Examples

Given an authenticated user
When they POST to /api/digests/preferences with frequency=weekly and time=08:00
Then their preferences are saved and digest generation is scheduled

Given a user who has opted in
When the digest generation cron runs
Then a digest is generated for that user and queued for email delivery

### Technical Checkboxes

- [ ] POST /api/digests/preferences validates and saves user preferences
- [ ] GET /api/digests/preferences returns current user's settings
- [ ] Digest generation algorithm selects relevant content from last 7 days
- [ ] Generated digest includes title, activity summary, and action links
- [ ] POST /api/digests/unsubscribe immediately stops digest delivery
- [ ] All endpoints return proper HTTP status codes (200, 400, 401, 500)
- [ ] Error responses include descriptive messages
- [ ] Unit tests cover happy path and error cases (> 85% coverage)
- [ ] Integration tests verify E2E digest workflow
- [ ] API documentation generated and reviewed

## Open Questions

- [Engineering] Should digest generation run synchronously or asynchronously (queue)?
- [Product] What should be included in the digest content (activity types, filters)?
- [Engineering] How do we handle timezone conversion for scheduled digests?
- [Engineering] Should we support draft/preview of digest before sending?

## Dependencies

- **Blocked by**: Database schema design (TICKET-003) - must be complete first
- **Depends on**: User authentication (TICKET-001) - for JWT validation
- **Blocks**: Email template design (future), Digest UI (future)

## Implementation Notes

- Use background job queue (Redis/Celery) for digest generation if time budget allows
- Consider caching user preferences to avoid database hits on every request
- Implement rate limiting to prevent abuse of digest endpoints
- Log all digest operations for debugging and analytics

## Notes

This is a critical component. Ensure comprehensive testing before production deployment. Coordinate with email service integration (TICKET-005 or later).
