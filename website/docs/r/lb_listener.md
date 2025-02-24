---
subcategory: "ELB (Elastic Load Balancing)"
layout: "aws"
page_title: "aws_lb_listener"
description: |-
  Manages a listener for a load balancer.
---

# Resource: aws_lb_listener

Manages a listener for a load balancer.

~> **Note** `aws_alb_listener` is known as `aws_lb_listener`. The functionality is identical.

## Example Usage

### Forward Action

```terraform
resource "aws_lb" "front_end" {
  # ...
}

resource "aws_lb_target_group" "front_end" {
  # ...
}

resource "aws_lb_listener" "front_end" {
  load_balancer_arn = aws_lb.front_end.arn
  port              = "443"
  protocol          = "HTTPS"
  ssl_policy        = "ELBSecurityPolicy-2016-08"
  certificate_arn   = "arn:aws:iam::187416307283:server-certificate/test_cert_rab3wuqwgja25ct3n4jdj2tzu4"

  default_action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.front_end.arn
  }
}
```

To an NLB:

```hcl
resource "aws_lb_listener" "front_end" {
  load_balancer_arn = aws_lb.front_end.arn
  port              = "443"
  protocol          = "TLS"
  certificate_arn   = "arn:aws:iam::187416307283:server-certificate/test_cert_rab3wuqwgja25ct3n4jdj2tzu4"
  alpn_policy       = "HTTP2Preferred"

  default_action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.front_end.arn
  }
}
```

### Redirect Action

```terraform
resource "aws_lb" "front_end" {
  # ...
}

resource "aws_lb_listener" "front_end" {
  load_balancer_arn = aws_lb.front_end.arn
  port              = "80"
  protocol          = "HTTP"

  default_action {
    type = "redirect"

    redirect {
      port        = "443"
      protocol    = "HTTPS"
      status_code = "HTTP_301"
    }
  }
}
```

### Fixed-response Action

```terraform
resource "aws_lb" "front_end" {
  # ...
}

resource "aws_lb_listener" "front_end" {
  load_balancer_arn = aws_lb.front_end.arn
  port              = "80"
  protocol          = "HTTP"

  default_action {
    type = "fixed-response"

    fixed_response {
      content_type = "text/plain"
      message_body = "Fixed response content"
      status_code  = "200"
    }
  }
}
```

### Authenticate-cognito Action

```terraform
resource "aws_lb" "front_end" {
  # ...
}

resource "aws_lb_target_group" "front_end" {
  # ...
}

resource "aws_cognito_user_pool" "pool" {
  # ...
}

resource "aws_cognito_user_pool_client" "client" {
  # ...
}

resource "aws_cognito_user_pool_domain" "domain" {
  # ...
}

resource "aws_lb_listener" "front_end" {
  load_balancer_arn = aws_lb.front_end.arn
  port              = "80"
  protocol          = "HTTP"

  default_action {
    type = "authenticate-cognito"

    authenticate_cognito {
      user_pool_arn       = aws_cognito_user_pool.pool.arn
      user_pool_client_id = aws_cognito_user_pool_client.client.id
      user_pool_domain    = aws_cognito_user_pool_domain.domain.domain
    }
  }

  default_action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.front_end.arn
  }
}
```

### Authenticate-OIDC Action

```terraform
resource "aws_lb" "front_end" {
  # ...
}

resource "aws_lb_target_group" "front_end" {
  # ...
}

resource "aws_lb_listener" "front_end" {
  load_balancer_arn = aws_lb.front_end.arn
  port              = "80"
  protocol          = "HTTP"

  default_action {
    type = "authenticate-oidc"

    authenticate_oidc {
      authorization_endpoint = "https://example.com/authorization_endpoint"
      client_id              = "client_id"
      client_secret          = "client_secret"
      issuer                 = "https://example.com"
      token_endpoint         = "https://example.com/token_endpoint"
      user_info_endpoint     = "https://example.com/user_info_endpoint"
    }
  }

  default_action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.front_end.arn
  }
}
```

### Gateway Load Balancer Listener

```terraform
resource "aws_lb" "example" {
  load_balancer_type = "gateway"
  name               = "example"

  subnet_mapping {
    subnet_id = aws_subnet.example.id
  }
}

resource "aws_lb_target_group" "example" {
  name     = "example"
  port     = 6081
  protocol = "GENEVE"
  vpc_id   = aws_vpc.example.id

  health_check {
    port     = 80
    protocol = "HTTP"
  }
}

resource "aws_lb_listener" "example" {
  load_balancer_arn = aws_lb.example.id

  default_action {
    target_group_arn = aws_lb_target_group.example.id
    type             = "forward"
  }
}
```

## Argument Reference

The following arguments are required:

* `default_action` - (Required) Configuration block for default actions. Detailed below.
* `load_balancer_arn` - (Required, forces new resource) ARN of the load balancer.

The following arguments are optional:

* `alpn_policy` - (Optional)  Name of the Application-Layer Protocol Negotiation (ALPN) policy. Can be set if `protocol` is `TLS`.
    * _Valid values_: `HTTP1Only`, `HTTP2Only`, `HTTP2Optional`, `HTTP2Preferred`, and `None`
* `certificate_arn` - (Optional) ARN of the default SSL server certificate. Exactly one certificate is required if the protocol is HTTPS. For adding additional SSL certificates, see the [`aws_lb_listener_certificate` resource](/docs/providers/aws/r/lb_listener_certificate.html).
* `port` - (Optional) Port on which the load balancer is listening. Not valid for gateway load balancers.
* `protocol` - (Optional) Protocol for connections from clients to the load balancer. For application load balancers, valid values are `HTTP` and `HTTPS`, with a default of `HTTP`. For network load balancers, valid values are `TCP`, `TLS`, `UDP`, and `TCP_UDP`. Not valid to use `UDP` or `TCP_UDP` if dual-stack mode is enabled. Not valid for gateway load balancers.
* `ssl_policy` - (Optional) Name of the SSL policy for the listener.
    * _Constraints_: Required if `protocol` is `HTTPS` or `TLS`.
* `tags` - (Optional) Map of tags to assign to the resource. If configured with a provider [`default_tags` configuration block](/docs/providers/aws/index.html#default_tags-configuration-block) present, tags with matching keys will overwrite those defined at the provider level.

~> **Note::** Please note that listeners that are attached to application load balancers must use either `HTTP` or `HTTPS` protocols while listeners that are attached to network load balancers must use the `TCP` protocol.

### default_action

The following arguments are required:

* `type` - (Required) Type of routing action.
    * _Valid values_: `forward`, `redirect`, `fixed-response`, `authenticate-cognito` and `authenticate-oidc`

The following arguments are optional:

* `authenticate_cognito` - (Optional) Configuration block for using Amazon Cognito to authenticate users. Specify only when `type` is `authenticate-cognito`. Detailed below.
* `authenticate_oidc` - (Optional) Configuration block for an identity provider that is compliant with OpenID Connect (OIDC). Specify only when `type` is `authenticate-oidc`. Detailed below.
* `fixed_response` - (Optional) Information for creating an action that returns a custom HTTP response.
    * _Constraints_: Required if `type` is `fixed-response`.
* `forward` - (Optional) Configuration block for creating an action that distributes requests among one or more target groups. Specify only if `type` is `forward`. If you specify both `forward` block and `target_group_arn` attribute, you can specify only one target group using `forward` and it must be the same target group specified in `target_group_arn`. Detailed below.
* `order` - (Optional) Order for the action. This value is required for rules with multiple actions. The action with the lowest value for order is performed first.
    * _Valid values_: between `1` and `50000`
* `redirect` - (Optional) Configuration block for creating a redirect action. Detailed below.
    * _Constraints_: Required if `type` is `redirect`.
* `target_group_arn` - (Optional) ARN of the target group to which to route traffic. Specify only if `type` is `forward` and you want to route to a single target group. To route to one or more target groups, use a `forward` block instead.

#### authenticate_cognito

The following arguments are required:

* `user_pool_arn` - (Required) ARN of the Cognito user pool.
* `user_pool_client_id` - (Required) ID of the Cognito user pool client.
* `user_pool_domain` - (Required) Domain prefix or fully-qualified domain name of the Cognito user pool.

The following arguments are optional:

* `authentication_request_extra_params` - (Optional) Query parameters to include in the redirect request to the authorization endpoint. Max: 10. Detailed below.
* `on_unauthenticated_request` - (Optional) Behavior if the user is not authenticated.
    * _Valid values_: `deny`, `allow` and `authenticate`
* `scope` - (Optional) Set of user claims to be requested from the IdP.
* `session_cookie_name` - (Optional) Name of the cookie used to maintain session information.
* `session_timeout` - (Optional) Maximum duration of the authentication session, in seconds.

##### authentication_request_extra_params

* `key` - (Required) Key of query parameter.
* `value` - (Required) Value of query parameter.

#### authenticate_oidc

The following arguments are required:

* `authorization_endpoint` - (Required) Authorization endpoint of the IdP.
* `client_id` - (Required) OAuth 2.0 client identifier.
* `client_secret` - (Required) OAuth 2.0 client secret.
* `issuer` - (Required) OIDC issuer identifier of the IdP.
* `token_endpoint` - (Required) Token endpoint of the IdP.
* `user_info_endpoint` - (Required) User info endpoint of the IdP.

The following arguments are optional:

* `authentication_request_extra_params` - (Optional) Query parameters to include in the redirect request to the authorization endpoint. Max: 10.
* `on_unauthenticated_request` - (Optional) Behavior if the user is not authenticated.
    * _Valid values_:  `deny`, `allow` and `authenticate`
* `scope` - (Optional) Set of user claims to be requested from the IdP.
* `session_cookie_name` - (Optional) Name of the cookie used to maintain session information.
* `session_timeout` - (Optional) Maximum duration of the authentication session, in seconds.

#### fixed_response

The following arguments are required:

* `content_type` - (Required) Content type.
    * _Valid values_: `text/plain`, `text/css`, `text/html`, `application/javascript` and `application/json`

The following arguments are optional:

* `message_body` - (Optional) Message body.
* `status_code` - (Optional) HTTP response code.
    * _Valid values_: `2XX`, `4XX`, or `5XX`

#### forward

The following arguments are required:

* `target_group` - (Required) Set of 1-5 target group blocks. Detailed below.

The following arguments are optional:

* `stickiness` - (Optional) Configuration block for target group stickiness for the rule. Detailed below.

##### target_group

The following arguments are required:

* `arn` - (Required) ARN of the target group.

The following arguments are optional:

* `weight` - (Optional) Weight. The range is 0 to 999.

##### stickiness

The following arguments are required:

* `duration` - (Required) Time period, in seconds, during which requests from a client should be routed to the same target group. The range is 1-604800 seconds (7 days).

The following arguments are optional:

* `enabled` - (Optional) Whether target group stickiness is enabled. Default is `false`.

#### redirect

~> **Note::** You can reuse URI components using the following reserved keywords: `#{protocol}`, `#{host}`, `#{port}`, `#{path}` (the leading "/" is removed) and `#{query}`.

The following arguments are required:

* `status_code` - (Required) HTTP redirect code. The redirect is either permanent (`HTTP_301`) or temporary (`HTTP_302`).

The following arguments are optional:

* `host` - (Optional) Hostname. This component is not percent-encoded. The hostname can contain `#{host}`.
    * _Default value_: `#{host}`
* `path` - (Optional) Absolute path, starting with the leading "/". This component is not percent-encoded. The path can contain #{host}, #{path}, and #{port}.
    * _Default value_: `/#{path}`
* `port` - (Optional) Port. Specify a value from `1` to `65535` or `#{port}`.
    * _Default value_: `#{port}`
* `protocol` - (Optional) Protocol.
    * _Valid values_: `HTTP`, `HTTPS`, or `#{protocol}`
    * _Default value_: `#{protocol}`
* `query` - (Optional) Query parameters, URL-encoded when necessary, but not percent-encoded. Do not include the leading "?".
    * _Default value_: `#{query}`

## Attributes Reference

In addition to all arguments above, the following attributes are exported:

* `arn` - ARN of the listener (matches `id`).
* `id` - ARN of the listener (matches `arn`).
* `tags_all` -Map of tags to assign to the resource, including those inherited from the provider [`default_tags` configuration block](/docs/providers/aws/index.html#default_tags-configuration-block).

## Import

Listeners can be imported using their ARN, e.g.,

```
$ terraform import aws_lb_listener.front_end arn:aws:elasticloadbalancing:us-west-2:187416307283:listener/app/front-end-alb/8e4497da625e2d8a/9ab28ade35828f96
```
