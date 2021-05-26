import React, {Component, createRef, Fragment} from 'react';
import {PanResponder} from 'react-native';
import {getRange, getSmallPoint, getFullRange} from './utils';
import {
  ViewContainer,
  RulerContainer,
  SmallTick,
  LargeTick,
  LabelTick,
  Label,
} from './styles';

class VerticalRuler extends Component {
  constructor(props) {
    super(props);
    this.rulerContainer = createRef();
    this.fullRange = getFullRange(props.min, props.max);
  }

  UNSAFE_componentWillMount() {
    this.panResponder = PanResponder.create({
      onStartShouldSetPanResponder: () => true,
      onMoveShouldSetPanResponder: () => true,
      onPanResponderGrant: (event, gestureState) => {
        const {onTouchStart} = this.props;
        onTouchStart?.();

        this.tapTimeout = setTimeout(() => {
          this.onTouchPoint(this.findTouchedPoint(gestureState.y0));
        }, 100);
      },
      onPanResponderMove: (event, gestureState) => {
        clearTimeout(this.tapTimeout);
        this.onTouchPoint(this.findTouchedPoint(gestureState.moveY));
      },
      onPanResponderTerminate: this.onPanResponderEnd,
      onPanResponderRelease: this.onPanResponderEnd,
    });
  }

  onTouchPoint = (point) => {
    const {onTouchPoint} = this.props;
    point && onTouchPoint?.(point);
  };

  onPanResponderEnd = () => {
    requestAnimationFrame(() => {
      const {onTouchEnd} = this.props;
      onTouchEnd?.();
    });
  };

  findTouchedPoint = (y) => {
    const top = y - (this.absContainerTop || 0);
    if (top >= 1 && top <= this.containerHeight) {
      const position = Math.round(
        (top / this.containerHeight) * this.fullRange.length,
      );
      return this.fullRange[position];
    }
  };

  onLayout = () => {
    this.rulerContainer?.current?.measure((x1, y1, width, height, px, py) => {
      this.absContainerTop = py;
      this.containerHeight = height;
    });
  };

  renderListSmallTick = (startPoint) => {
    const smallRange = getSmallPoint(startPoint);
    return smallRange.map((point) => <SmallTick key={point} />);
  };

  renderRuler = () => {
    const {min, max} = this.props;
    const range = getRange(min, max);
    return range.map((point, index) => (
      <Fragment key={point}>
        <LabelTick>
          <Label>{point}</Label>
          <LargeTick />
        </LabelTick>

        {index !== range.length - 1 && this.renderListSmallTick(point)}
      </Fragment>
    ));
  };

  render() {
    const {panHandlers} = this.panResponder;
    return (
      <ViewContainer>
        <RulerContainer
          ref={this.rulerContainer}
          onLayout={this.onLayout}
          {...panHandlers}>
          {this.renderRuler()}
        </RulerContainer>
      </ViewContainer>
    );
  }
}

export default VerticalRuler;
