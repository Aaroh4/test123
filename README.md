```
void Player::move(const float &dir_x, const float &dir_y, float deltaTime)
{
	this->_velocity.x += dir_x * this->_acceleration * deltaTime;
	this->_velocity.y += dir_y * this->_acceleration * deltaTime;

	if (std::abs(this->_velocity.x) > this->_maxVelocity)
	{
		this->_velocity.x = this->_maxVelocity * ((this->_velocity.x > 0.f) ? 1.f : -1.f);
	}
}

void Player::updatePhys(const std::vector<sf::RectangleShape> &blocks, float deltaTime)
{
	this->_velocity.y += this->_gravity * deltaTime;
	if (std::abs(this->_velocity.y) > this->_maxGravityVel)
	{
		this->_velocity.y = this->_maxGravityVel * ((this->_velocity.y > 0.f) ? 1.f : -1.f);
	}

	this->_velocity *= std::pow(this->_drag, deltaTime);

	checkCollision(blocks, deltaTime);

	if (this->_collidingX == true || std::abs(this->_velocity.x) < this->_minVelocity)
		this->_velocity.x = 0.f;
	if (this->_collidingY == true) 
		this->_velocity.y = 0.f;

	this->_sprite.move(this->_velocity * deltaTime);
}

void Player::checkCollision(const std::vector<sf::RectangleShape> &blocks, float deltaTime)
{
	this->_collidingX = false;
	this->_collidingY = false;
	this->_futureBounds.setRadius(this->_sprite.getRadius());
	this->_futureBounds.setPosition(this->_sprite.getPosition());

	this->_futureBounds.move(0, this->_velocity.y * deltaTime);
	for (size_t i = 0; i < blocks.size(); i++)
	{
		if (this->_futureBounds.getGlobalBounds().intersects(blocks[i].getGlobalBounds()))
		{
			if (this->_velocity.y > 0)
				this->_grounded = true;
			this->_collidingY = true;
			break ;
		}
		else
			this->_grounded = false;
	}
	this->_futureBounds.setPosition(this->_sprite.getPosition());
	this->_futureBounds.move(this->_velocity.x * deltaTime, 0);
	for (size_t i = 0; i < blocks.size(); i++)
		if (this->_futureBounds.getGlobalBounds().intersects(blocks[i].getGlobalBounds()))
		{	
			this->_collidingX = true;
			break ;
		}
}
```
